# Kiến trúc

Runtime: `node src/index.js`. Không có database. Mọi thứ bền là process memory hoặc `data/config.json`.

## Khởi động (`src/index.js`)

Intent bắt buộc: `Guilds`, `GuildMembers` (privileged), `GuildMessages`, `GuildModeration`.
`MessageContent` chỉ khi `USE_MESSAGE_CONTENT` khác `false`.

Loader:

- `src/commands/*.js` — mỗi file export `{ data, execute, cooldownSeconds? }`. Thiếu `data` hoặc `execute` thì bỏ qua.
- `src/events/*.js` — export một event `{ name, once?, execute }` **hoặc một mảng** event. Đăng ký đúng một lần ở đây. Đừng `client.on` bên trong module bảo vệ.

Đăng nhập thử 5 lần, backoff 5s–60s. `TokenInvalid`, `DisallowedIntents`, `TokenMissing` dừng ngay.
`uncaughtException` thoát 1 để pm2 khởi động lại sạch. SIGINT/SIGTERM gọi `client.destroy()`.

Cache: `MessageManager` 50, `ReactionManager` 0, sweeper message 30 phút. `allowedMentions.parse` rỗng.

## File map

```
src/index.js                 client, loader, login
src/config.js                data/config.json, merge defaults, ghi nguyên tử
src/deploy-commands.js       REST put slash commands (chạy tay)
src/commands/                setup, security, health, lockdown, purge, panel, ping
src/events/ready.js          sweep + expire mỗi 30s, health lúc startup
src/events/guildMemberAdd.js người vào: raid / quarantine; bot: duyệt
src/events/messageCreate.js  spam, invite, mention, xóa tin raider
src/events/nukeEvents.js     ChannelDelete, RoleDelete, BanAdd
src/events/escalationEvents.js RoleUpdate + MemberUpdate
src/events/interactionCreate.js verify, botapp:, panel:, slash
src/events/guildCreate.js    health khi vào server mới
src/protection/raid.js       đếm join, lockdown, purge, sweep
src/protection/antiBot.js    audit BotAdd + Levenshtein tên
src/protection/botApproval.js nút duyệt, hết hạn, trust
src/protection/botApprovalId.js customId thuần
src/protection/antiNuke.js   đếm hành động phá, gỡ role
src/protection/antiSpam.js   cửa sổ tốc độ / nội dung lặp, INVITE_RE
src/protection/hierarchy.js  snapshot Discord -> analyze, debounce 30s
src/protection/hierarchyCheck.js DANGEROUS, REQUIRED, analyze() thuần
src/protection/exempt.js     miễn spam
src/panel/controlPanel.js    nút ephemeral prefix panel:
src/panel/presets.js         low/mid/high/max
src/utils/                   log, embed, webhook, cooldown, time
tests/core.test.js           test thuần, không token
```

## Luồng raid

1. `GuildMemberAdd` (người, không phải bot) gọi `recordJoin`.
2. Đủ `raid.maxJoins` trong `raid.windowMs` (mặc định 8 / 10s) thì `startRaid`.
3. Lần đầu: đánh dấu raider, mod-log. Chế độ `act` và `autoLockdown`: khóa SendMessages + AddReactions mọi kênh text/announcement/forum trừ kênh log, tăng verification lên High, xóa tin đã track nếu `purgeRaiders`.
4. Timeout cả nhóm vừa vào (`quarantineMs`, mặc định 1 giờ) chỉ khi `act` và `member.moderatable`.
5. Người vào trong lúc raid đang active bị đánh dấu raider và quarantine, không kích hoạt lại lockdown.
6. `ready` sweep 30s: hết `lockdown.until` thì mở khóa và trả verification. Snapshot nằm trong config nên sống qua restart.
7. Nút `verify` từ chối khi raid active.

Tin nhắn chỉ được `trackMessage` nếu member vào dưới 24 giờ. Tối đa 25 tin / user, TTL 30 phút. `bulkDelete` bỏ tin > 14 ngày.

Lockdown bỏ qua kênh đã có trong snapshot (lần khóa trước). Sleep 300ms mỗi kênh. Một guild chỉ một thao tác khóa/mở (`exclusive`). Đang bận thì lần sau trả 0, không xếp hàng.

## Luồng bot mới

`handleBot` trong `guildMemberAdd.js`:

1. `checkBotAdder`: bot đã trust thì trusted. Ngược lại sleep 1.5s, đọc audit `BotAdd` trong 30s.
2. `findImpersonation`: tên đã chuẩn hóa dài >= 4, Levenshtein <= max(1, floor(len/6)) so với user/bot tin cậy.
3. Trusted và không lookalike: bỏ qua.
4. `botApproval.enabled` và chưa trust: `requestApproval` (kể cả lookalike). Tối đa 10 pending; đủ thì kick nếu `act`.
5. Tắt duyệt: chỉ kick ở `act` khi **biết** người thêm và người đó không tin cậy. `unknown` chỉ log. Lookalike không bao giờ tự ban vì tên.

Duyệt (`botApproval.js`):

- `act` và role bot mới `editable`: `setPermissions(0n)`, lưu `origPerms`.
- Gửi kênh `logChannelId`, không được thì DM chủ server. Không gửi được và `act`: kick.
- Nút: `botapp:allow|deny|confirm|cancel:<guildId>:<botId>`.
- Chỉ `ownerId` hoặc `trustedUserIds` bấm được.
- Allow hiện xác nhận lần hai. Confirm khôi phục bitfield và push `trustedBotIds`.
- Deny kick. Hết hạn (`timeoutMs` mặc định 10 phút) do `expire()` trên tick 30s: `act` thì kick.

## Anti-nuke

`nukeEvents.js` -> `handleDestructive`. Đếm theo `guild:executor:auditType` trong `nuke.windowMs` (10s). Chỉ chạy **đúng lúc** `count === nuke.maxActions` (mặc định 3), không lặp lại khi vượt.
`act`: `roles.set` giữ role managed, @everyone, và role >= role cao nhất của bot.
Executor lấy từ 5 audit gần, target khớp, tuổi < 5s. Không thấy executor thì return, không đoán.

## Chống leo thang

`escalation.enabled` mặc định true.

- `GuildRoleUpdate`: quyền trong `DANGEROUS` mới xuất hiện. Đổi `position` thì `scheduleHealthCheck` (gom 30s).
- `GuildMemberUpdate`: role mới có quyền nguy hiểm. Bỏ qua nếu `oldMember.partial` hoặc chính bot.
- Executor không hợp lệ: log. `act` và `escalation.revert`: hoàn tác nếu role `editable`. Không có executor thì chỉ cảnh báo, không hoàn tác mù.

`DANGEROUS`: Administrator, ManageGuild, ManageRoles, ManageChannels, ManageWebhooks, BanMembers, KickMembers.
Dùng `permissions.has(perm, false)` để Administrator không bị tính thành mọi quyền.

## Spam

`messageCreate` bỏ bot và `isExempt`. Lý do cộng dồn: đang là raider, `checkSpam`, invite regex, >= 5 user mention.
`act`: xóa tin + timeout `spam.timeoutMs` (10 phút) nếu moderatable.
Log tối đa 1 lần / 30s / user. `checkSpam` cần nội dung tin; tắt Message Content Intent thì nhánh nội dung yếu đi.

## Health

`hierarchyCheck.analyze` thuần, test được. `hierarchy.snapshot` lấy Discord.
Fetch hết member chỉ khi `memberCount <= 1500`.
`maybeWarnHealth` chỉ báo kênh log khi có `error`, tối đa 30 phút / server (trừ `force`).
Gọi lúc ready, `guildCreate`, và sau khi role đổi chỗ (debounce).

## Tick sống sót qua restart

| Thứ | Nơi lưu | Ai dọn |
|---|---|---|
| Lockdown snapshot + until | config | `raid.sweep` |
| Pending bot | `cfg.pendingBots` | `botApproval.expire` |
| Join window, raider set, tin gần | RAM | mất khi restart; tin chưa xóa có thể sót |
| Đếm nuke / spam | RAM | mất khi restart |

Đừng đưa bộ đếm RAM vào config.
