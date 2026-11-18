# Bot con: spam

Việc: tin nhắn rác, nội dung lặp, link mời, mention hàng loạt, xóa tin raider ngay khi họ chat, ai được miễn spam.

## Sở hữu

- `src/protection/antiSpam.js`
- `src/protection/exempt.js`
- `src/events/messageCreate.js`

## Cấm đụng

`raid.js` (dùng `trackMessage` / `isRaider` sẵn có, không sửa), `antiNuke.js`, `escalationEvents.js`, command, panel, file bot chính giữ.
Cần hàm mới trong `raid.js` hoặc `utils/`: dừng và báo bot chính.

## Phải giữ

- Bỏ qua bot và `isExempt`.
- `isExempt` chỉ dùng cho spam: owner, trusted user, Administrator, `exemptRoleIds`. Đừng mang hàm này sang nuke hoặc escalation.
- Chỉ `trackMessage` member vào dưới 24 giờ.
- Lý do cộng dồn: đang là raider, `checkSpam`, `INVITE_RE`, >= 5 user mention.
- `act`: xóa tin, timeout `spam.timeoutMs` nếu `moderatable`. `log`: không xóa, không timeout.
- Log tối đa 1 lần / 30s / user.
- Không bật Message Content Intent trong code. Thiếu nội dung thì nhánh spam chữ vẫn chạy, invite/trùng có thể yếu. Ghi rõ giới hạn đó nếu sửa.

## Xong khi

`checkSpam` và `INVITE_RE` vẫn test thuần. `messageCreate` không phạt ở mode log.
