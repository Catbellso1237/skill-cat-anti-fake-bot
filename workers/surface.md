# Bot con: surface

Việc: slash command, bảng điều khiển, nút verify đã đăng sẵn, chuỗi deploy lệnh.

## Sở hữu

- `src/commands/*.js`
- `src/panel/controlPanel.js`
- `src/panel/presets.js`
- `src/deploy-commands.js`

## Cấm đụng

`src/events/interactionCreate.js` là của bot chính. Nút hoặc menu **mới** (prefix mới) thì dừng, mô tả customId, để bot chính gắn một nhánh.
Sửa nút `verify`, `botapp:`, `panel:` sẵn có thì được đổi payload trong file bạn sở hữu, nhưng không đổi prefix.
Cấm: `raid.js`, `botApproval.js`, `antiNuke.js`, `config.js`.

## Phải giữ

- Lệnh mới: `{ data, execute, cooldownSeconds }` tiếng Việt, ephemeral, quyền Discord đúng việc (xem `references/commands.md`).
- Lệnh phá kênh hoặc khóa server: ManageGuild trở lên, không tắt cooldown.
- Đổi builder thì nói rõ "cần `npm run deploy` một lần". Không gọi deploy từ `index.js`.
- Preset chỉ ở `presets.js`: low 12/10s, mid 8/10s, high 5/10s, max 3/8s. `presetOf` trả null nếu lệch. Đừng ép config lệch về preset.
- Panel kiểm tra lại quyền lúc bấm, không tin lúc gửi.
- `/security` là chỗ cấu hình bảo vệ. Đừng tạo slash mới cho từng cờ nhỏ.
- Không in webhook đầy đủ. Dùng `maskWebhook`.

## Xong khi

Command mới nằm trong `src/commands/` để loader tự thấy, preset vẫn test thuần, và bạn kê những gì bot chính phải gắn trong `interactionCreate.js`.
