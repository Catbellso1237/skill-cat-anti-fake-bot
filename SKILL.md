---
name: cat-anti-fake-bot
description: >
  Work on Cat Anti Fake Bot, a discord.js v14 security bot (anti-raid, anti-fake
  bot, anti-spam, anti-nuke, bot approval, Wick-style role hierarchy). Use when
  the user mentions cat-anti-fake-bot, Cat Anti Fake, bot Discord chống raid,
  lockdown, duyệt bot, anti-nuke, /security, /panel, or files under
  Catbellso1237/cat-anti-fake-bot. Read this before editing that bot.
---

# Cat Anti Fake Bot

Bot Discord (Node >= 20, discord.js 14) bảo vệ máy chủ: raid, bot giả, spam, nuke, leo thang quyền.
Mã nguồn sống ở [Catbellso1237/cat-anti-fake-bot](https://github.com/Catbellso1237/cat-anti-fake-bot).
Skill này chốt theo commit `1949df84c82633538f6d4db74b545edab4956ce6`. Nếu repo bot đã đi tiếp, đọc diff trước khi tin vào chi tiết dưới đây.

Ngôn ngữ code, comment, lệnh và log là **tiếng Việt**. Giữ nguyên phong cách đó khi sửa.

## Đọc thêm khi cần

Không nhét hết vào context. Mở file đúng việc:

- `references/architecture.md` — file map, luồng sự kiện, customId, dữ liệu.
- `references/commands.md` — slash command, panel, biến môi trường.
- `references/invariants.md` — luật không được phá. Đọc trước mọi lần sửa bảo vệ hoặc config.
- `references/extending.md` — thêm command, event, module bảo vệ, test.

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

## Việc bạn không được làm

- Commit `.env`, token, webhook URL, `data/config.json`.
- Cấp quyền Administrator cho bot, hoặc bảo user cấp quyền đó.
- Đổi snapshot lockdown, mutex `exclusive()`, hoặc thứ tự gỡ quyền bot mới mà không đọc `references/invariants.md`.
- Kick / ban chỉ vì tên giống (lookalike chỉ cảnh báo).
- Tự bật `act` trên server thật.
- Đăng ký lại slash command mỗi lần khởi động. Chỉ `npm run deploy` khi đổi `data` của command.
