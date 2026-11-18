# Lệnh, panel, môi trường

Mọi slash trừ `/ping` chỉ chạy trong guild. Cooldown mặc định 3s (`command.cooldownSeconds`, security đặt 2). Hết hạn Discord 3 giây phải ack: việc chậm dùng `deferReply`.

## Slash

| Lệnh | Quyền Discord | Việc |
|---|---|---|
| `/setup` | Administrator | Tạo kênh riêng `cat-mod-log` và role `Verified`, ghi vào config |
| `/security status` | ManageGuild | Đọc config + raid đang active |
| `/security mode` | ManageGuild | `log` hoặc `act` |
| `/security raid` | ManageGuild | max_joins 2–50, seconds 3–120, lockdown_minutes 1–240, các cờ auto |
| `/security accountage` | ManageGuild | 0–365 ngày -> `minAccountAgeMs` |
| `/security logchannel` | ManageGuild | kênh text |
| `/security webhook` | ManageGuild | URL Discord hoặc bỏ trống để xóa. Validate trước khi lưu |
| `/security webhook_test` | ManageGuild | gửi embed thử |
| `/security botapproval` | ManageGuild | enabled, timeout 1–60 phút |
| `/security escalation` | ManageGuild | enabled, revert |
| `/security trust` | ManageGuild | add/remove user. Bot vào `trustedBotIds`, người vào `trustedUserIds` |
| `/health` | ManageGuild | embed từ `healthEmbed` |
| `/lockdown on\|off\|status` | ManageGuild | khóa/mở tay. `off` khôi phục snapshot |
| `/purge messages` | ManageMessages | tối đa 100 tin gần, lọc user tuỳ chọn, không xóa bulk tin > 14 ngày |
| `/purge raiders` | ManageMessages | `purgeUsers` các id đang đánh dấu |
| `/purge reset_channel` | ManageMessages + ManageChannels | clone kênh rồi xóa kênh cũ, không hoàn tác |
| `/panel control` | ManageGuild | panel ephemeral |
| `/panel verify` | ManageGuild | đăng nút `customId === "verify"` |
| `/ping` | mọi người | sống/chết |

Đổi option của builder thì chạy lại `npm run deploy`. Có `GUILD_ID` thì đăng ký guild (hiện ngay). Không có thì global, có thể trễ.

## Panel (`/panel control`)

Tin ephemeral, customId bắt đầu `panel:`. Sau restart nút cũ vẫn gọi được handler; bấm Làm mới để cập nhật số.
Mỗi lần bấm kiểm tra lại ManageGuild (và ManageMessages với nút xóa tin). Đừng tin quyền lúc gửi panel.

Nút: đổi log/act, bật duyệt bot, khóa/mở chat, bật tự khóa, bật chống leo thang, health, thử webhook, xóa tin raider, làm mới.
Menu nhạy (`src/panel/presets.js`):

| Khóa | Nhãn | Ngưỡng |
|---|---|---|
| low | Thấp | 12 / 10s |
| mid | Vừa | 8 / 10s |
| high | Cao | 5 / 10s |
| max | Rất cao | 3 / 8s |

`presetOf` trả null nếu config không khớp preset (user đã chỉnh `/security raid`). Đừng ép về preset khi mở panel.

Webhook, kênh log, danh sách trust không nằm trên panel. Dùng `/security`.

## Biến môi trường (`.env`, không commit)

```
DISCORD_TOKEN=
CLIENT_ID=
GUILD_ID=            # optional, chỉ cho deploy dev
USE_MESSAGE_CONTENT=true
RAID_WEBHOOK_URL=    # fallback nếu server chưa /security webhook
```

Webhook gửi đi phải che trong log (`maskWebhook`). URL hợp lệ dạng `https://discord.com/api/webhooks/ID/TOKEN` (hoặc discordapp.com tùy `isValidWebhookUrl`).

## Quyền mời bot

Cần: ManageChannels, ManageRoles, ManageGuild, ModerateMembers, KickMembers, ViewAuditLog, ManageMessages, SendMessages, EmbedLinks.
Không mời bằng Administrator.
Bật Server Members Intent. Bật Message Content Intent nếu không tắt `USE_MESSAGE_CONTENT`.
Kéo role bot lên trên cùng (dưới role chủ server nếu Discord buộc), rồi `/health`.

## Mod-log

`sendModLog` ghi kênh `logChannelId`. `alert: true` cũng gửi webhook (config trước, rồi `RAID_WEBHOOK_URL`).
Embed qua `safeEmbed`. Màu nằm trong `utils/embed.js` (`COLORS`). Đừng tự bị embed trần field.
