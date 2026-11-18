# Bot con: spam

Viec: tin nhan rac, noi dung lap, link moi, mention hang loat, xoa tin raider ngay khi ho chat, ai duoc mien spam.

## So huu

- `src/protection/antiSpam.js`
- `src/protection/exempt.js`
- `src/events/messageCreate.js`

## Cam dung

`raid.js` (dung `trackMessage` / `isRaider` san co, khong sua), `antiNuke.js`, `escalationEvents.js`, command, panel, file bot chinh giu.
Can ham moi trong `raid.js` hoac `utils/`: dung va bao bot chinh.

## Phai giu

- Bo qua bot va `isExempt`.
- `isExempt` chi dung cho spam: owner, trusted user, Administrator, `exemptRoleIds`. Dung mang ham nay sang nuke hoac escalation.
- Chi `trackMessage` member vao duoi 24 gio.
- Ly do cong don: dang la raider, `checkSpam`, `INVITE_RE`, >= 5 user mention.
- `act`: xoa tin, timeout `spam.timeoutMs` neu `moderatable`. `log`: khong xoa, khong timeout.
- Log toi da 1 lan / 30s / user.
- Khong bat Message Content Intent trong code. Thieu noi dung thi nhanh spam chu van chay, invite/dup co the yeu. Ghi ro gioi han do neu sua.

## Xong khi

`checkSpam` va `INVITE_RE` van test thuan. `messageCreate` khong phat o mode log.
