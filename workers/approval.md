# Bot con: approval

Việc: duyệt bot mới. Tạm gỡ quyền, nút bấm, hết hạn, trust sau khi xác nhận rủi ro.

## Sở hữu

- `src/protection/botApproval.js`
- `src/protection/botApprovalId.js`

## Cấm đụng

`guildMemberAdd.js` (joins gọi `requestApproval`), `raid.js`, `antiNuke.js`, command, panel, file bot chính giữ.
Cần nút mới hiện trong Discord: mô tả customId, để bot chính gắn nhánh trong `interactionCreate.js`. Đừng tự gắn.

## Phải giữ

- Prefix `botapp:`, đúng 4 action `allow|deny|confirm|cancel`, kèm guildId và botId. Parse sai trả null.
- Chỉ `ownerId` hoặc `trustedUserIds` bấm được.
- `allow` chỉ mở màn xác nhận. `confirm` mới khôi phục `BigInt(origPerms)` và push `trustedBotIds`.
- `act` và role `editable`: `setPermissions(0n)` trước khi hỏi. Không editable: hỏi, không giả vờ đã gỡ quyền.
- Gửi log channel trước, DM chủ server sau. Không gửi được và `act`: kick.
- Tối đa 10 pending. Đủ thì không xếp thêm; `act` kick bot thứ 11.
- Pending nằm trong `cfg.pendingBots` để sống qua restart. `expire()` kick khi `act` và quá `expiresAt`.
- Cho phép = chấp nhận rủi ro và ngừng giám sát bot đó. Đừng tự thêm trust ở chỗ khác.

## Xong khi

Parse id vẫn test được không cần discord.js, luồng allow/deny/confirm/cancel không mất bước xác nhận, và bạn liệt kê case test mới.
