---
name: cat-anti-fake-bot
description: >
  Work on Cat Anti Fake Bot, a discord.js v14 security bot (anti-raid, anti-fake
  bot, anti-spam, anti-nuke, bot approval, Wick-style role hierarchy). Use when
  the user mentions cat-anti-fake-bot, Cat Anti Fake, bot Discord chống raid,
  lockdown, duyệt bot, anti-nuke, /security, /panel, or files under
  Catbellso1237/cat-anti-fake-bot. Read this before editing that bot. If the
  task spans two areas, dispatch the child workers in workers/ instead of
  doing everything in one pass.
---

# Cat Anti Fake Bot

Bot Discord (Node >= 20, discord.js 14) bảo vệ máy chủ: raid, bot giả, spam, nuke, leo thang quyền.
Mã nguồn sống ở [Catbellso1237/cat-anti-fake-bot](https://github.com/Catbellso1237/cat-anti-fake-bot).
Skill này chốt theo commit `1949df84c82633538f6d4db74b545edab4956ce6`. Nếu repo bot đã đi tiếp, đọc diff trước khi tin vào chi tiết dưới đây.

Ngôn ngữ code, comment, lệnh và log là **tiếng Việt**. Giữ nguyên phong cách đó khi sửa.

Đây là **bot chính** (người điều phối). Năm bot con nằm trong `workers/`. Chúng không phải token Discord thứ hai. Chúng là phân việc cho những lần AI sửa bot sau này.

## Đọc thêm khi cần

Không nhét hết vào context. Mở file đúng việc:

- `references/architecture.md` — file map, luồng sự kiện, customId, dữ liệu.
- `references/commands.md` — slash command, panel, biến môi trường.
- `references/invariants.md` — luật không được phá. Đọc trước mọi lần sửa bảo vệ hoặc config.
- `references/extending.md` — thêm command, event, module bảo vệ, test.
- `workers/DISPATCH.md` — khi nào giao việc, bot con nào giữ file nào.

## Phân việc (bắt buộc khi việc đụng hai mảng)

Một việc nằm trọn trong một bot con: làm một mình, vẫn đọc brief của bot đó.
Hai mảng trở lên: bot chính giao song song, rồi tự gộp. Không để hai bot sửa cùng một file.

| Bot con | Brief | Giữ |
|---|---|---|
| joins | `workers/joins.md` | Raid, lockdown, người vào, bot giả lúc vào |
| approval | `workers/approval.md` | Duyệt bot, nút, hết hạn |
| nuke | `workers/nuke.md` | Anti-nuke, leo thang quyền, thứ bậc role |
| spam | `workers/spam.md` | Tin nhắn, invite, mention, miễn spam |
| surface | `workers/surface.md` | Slash, panel, deploy lệnh |
| qa | `workers/qa.md` | Chạy sau cùng: test và đối luật |

Bot chính giữ một mình: `src/index.js`, `src/config.js`, `src/events/ready.js`, `src/events/interactionCreate.js`, `src/utils/*`. Bot con cần đổi default hoặc nút mới thì báo về, không tự sửa các file này.

Cách giao, mẫu prompt, và thứ tự gộp: `workers/DISPATCH.md`.

## Mô hình bạn phải giữ

Hai chế độ, mặc định `log`:

| `mode` | Hành vi |
|---|---|
| `log` | Chỉ ghi mod-log / webhook. Không timeout, kick, khóa kênh, gỡ role, gỡ quyền bot. |
| `act` | Làm thật các hành động trên. |

Không đổi default thành `act`. Không bỏ qua `cfg.mode` ở bất kỳ nhánh xử phạt nào.

Cấu hình mỗi server nằm trong `data/config.json` (không commit). `getConfig(guildId)` trả **cùng một object**. Sửa field rồi `await saveConfig()`. Đừng clone rồi ghi đè, đừng ghi file bằng tay.

Role của bot phải nằm trên những gì nó cần xử lý. Discord từ chối mọi thao tác với member/role cao hơn. `/health` là cách kiểm tra đúng.

## Ai được miễn

Không gộp chung một hàm. Ba lớp khác nhau:

1. **Spam / tin nhắn** (`isExempt`): chủ server, user tin cậy, ai có Administrator, role trong `exemptRoleIds`.
2. **Anti-nuke và chống leo thang**: chỉ chính bot này, chủ server, `trustedUserIds`, `trustedBotIds`. Admin thường **không** được miễn. Đó là cố ý.
3. **Duyệt bot mới**: bỏ qua nếu bot đã tin cậy, hoặc người thêm là chủ server / user tin cậy. Không đọc được audit log thì coi là chưa biết, không kick mù.

Cho phép một bot lạ = thêm vào `trustedBotIds` và **ngừng giám sát** bot đó. Luôn đi qua nút xác nhận rủi ro, đừng tự thêm trust trong code.

## Việc bạn được làm

- Sửa và thêm tính năng trong repo bot, theo `references/extending.md`.
- Giải thích lệnh, chẩn đoán cấu hình, viết test thuần không cần token.
- Chạy `npm run check` và `npm test` sau khi sửa logic.
- Giao bot con khi việc chạm từ hai dòng trong bảng phân việc.

## Việc bạn không được làm

- Commit `.env`, token, webhook URL, `data/config.json`.
- Cấp quyền Administrator cho bot, hoặc bảo user cấp quyền đó.
- Đổi snapshot lockdown, mutex `exclusive()`, hoặc thứ tự gỡ quyền bot mới mà không đọc `references/invariants.md`.
- Kick / ban chỉ vì tên giống (lookalike chỉ cảnh báo).
- Tự bật `act` trên server thật.
- Đăng ký lại slash command mỗi lần khởi động. Chỉ `npm run deploy` khi đổi `data` của command.
- Tạo thêm một Discord bot / token để "chia tải". Phân việc ở đây là phân việc cho AI, không phải thêm process.
