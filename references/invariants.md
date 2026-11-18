# Luật không được phá

Đọc hết mục liên quan trước khi sửa. Mỗi mục là một lỗi đã bị chặn cố ý trong code hiện tại.

## Config

- `getConfig` chỉ merge default **một lần mỗi guild mỗi process** (`initialized` set). Object trả về là object trong `store`. Mọi handler phải mutate đúng object đó rồi `saveConfig()`.
- `saveConfig` xếp hàng promise. Ghi file tạm rồi `rename`. Đừng `writeFile` thẳng vào `config.json`, đừng song song hai hàm ghi khác.
- Thêm key mới: thêm vào `DEFAULTS` trong `src/config.js`. Server cũ được merge lần đầu tiên process thấy guild. Đừng migration script trừ khi đổi nghĩa field cũ.
- Default `mode` là `log`. Default phản ứng nguy hiểm (duyệt bot, escalation, auto lockdown) chỉ có tác dụng thật khi user chủ động bật `act`.
- `data/config.json` và `.env` nằm ngoài git. Đừng in token hay webhook đầy đủ ra log, reply, hay commit.

## Lockdown

- Snapshot lưu quyền **trước** khi sửa: `true` allow, `false` deny, `null` chưa ghi đè, cho `SendMessages`.
- Kênh đã `false` (vốn cấm chat) không sửa, nhưng vẫn ghi snapshot để lúc mở khóa bỏ qua.
- Mở khóa: `false` thì không đụng; `null` xóa overwrite; `true` trả allow. `AddReactions` trả `null`.
- `exclusive()` bắt buộc. Hai lần khóa chồng nhau sẽ chụp kênh vừa bị cấm thành "vốn cấm" và không bao giờ trả quyền.
- Đừng khóa kênh `logChannelId`.
- Đừng tự đặt `cfg.lockdown = null` ở chỗ khác `unlockInner`. Mở khóa phải đi qua `unlock`.
- Verification cũ chỉ lưu khi thật sự nâng lên High. Trả đúng giá trị đó, đừng hard-code None.

## Raid và phạt

- `startRaid` trả `false` nếu đã có raid state: chỉ kéo dài `until` và thêm id, không khóa lần hai.
- Timeout/kick/xóa chỉ sau khi đã biết `mode === 'act'`.
- Thiếu quyền thì ghi hành động thất bại vào log, không throw ra interaction (trừ lỗi lập trình).
- Không ban vì tên giống. Lookalike là field cảnh báo trên phiếu duyệt hoặc một dòng log.
- Anti-nuke chỉ hành động khi đếm **bằng** ngưỡng, không phải mỗi lần vượt ngưỡng.
- Không suy đoán executor khi audit log trống. Bỏ qua hoặc chỉ cảnh báo, đúng như nhánh hiện tại.
- `isExempt` không được dùng cho anti-nuke hay escalation. Admin bị chiếm quyền phải bị gỡ role.
- Owner không bị bot xử lý. Nêu giới hạn này khi user hỏi "chặn cả chủ server": không làm được, bảo họ bật 2FA.

## Duyệt bot

- customId luôn có guildId vì nút có thể nằm trong DM. Sửa format thì sửa cả `botApprovalId.js` và test parse.
- Confirm mới trust. Allow chưa trust.
- Khôi phục đúng `BigInt(p.origPerms)`, không cấp bộ quyền mới.
- Pending tối đa 10. Đừng bỏ giới hạn.
- Nút cũ sau restart vẫn đọc `pendingBots` từ config. Đừng cất pending chỉ trong RAM.

## Tương tác Discord

- Mọi reply lỗi dùng ephemeral (`MessageFlags.Ephemeral`). Đừng để lỗi kỹ thuật hoặc URL webhook ra kênh công khai.
- `interactionCreate` bắt lỗi và `safeReply`. Handler mới phải nằm trong try đó, không tự `client.on`.
- customId: `verify` cố định, `botapp:` cho duyệt, `panel:` cho panel. Đừng đụng prefix.
- Cooldown theo user + tên lệnh. Đừng tắt cho lệnh phá hoại (`purge reset_channel`, lockdown).
- `npm run deploy` là lệnh riêng. Gọi nó trong `index.js` sẽ ăn rate limit.

## Quyền và thứ bậc

- `REQUIRED` trong `hierarchyCheck.js` là hợp đồng với user. Thêm tính năng cần quyền mới thì thêm cặp `[perm, lý do tiếng Việt]` vào đó.
- Role xác minh phải thấp hơn bot, không nó nút verify chết.
- `@everyone` có bất kỳ quyền `DANGEROUS` là error.
- Member count trên role là cache. Đừng coi là đủ trên server > 1500 người.

## An toàn khi AI sửa

- Đừng "dọn" bằng cách gộp ba lớp miễn thành một.
- Đừng đổi ngưỡng mặc định cho "chặt hơn" trừ khi user xin và hiểu false positive.
- Đừng thêm dependency nếu `node:` và discord.js đủ. Bot cố tình không có DB.
- Logic quyết định (ngưỡng, parse id, analyze, preset) để hàm thuần, test trong `tests/` không cần mạng.
- Giữ comment tiếng Việt ngắn ở chỗ dễ gãy (mutex, audit delay, bitfield). Đừng viết lại cả file cho "sạch" nếu diff không đòi.
