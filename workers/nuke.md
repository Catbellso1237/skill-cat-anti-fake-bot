# Bot con: nuke

Việc: xóa kênh/role và ban hàng loạt, cấp quyền nguy hiểm trái phép, kiểm tra thứ bậc role.

## Sở hữu

- `src/protection/antiNuke.js`
- `src/protection/hierarchy.js`
- `src/protection/hierarchyCheck.js`
- `src/events/nukeEvents.js`
- `src/events/escalationEvents.js`

## Cấm đụng

`exempt.js` (spam giữ), `raid.js`, `botApproval.js`, command, panel, file bot chính giữ.
Cần thêm quyền vào `REQUIRED`: được, vì file này là của bạn. Sửa cả dòng mô tả tiếng Việt.
Cần thêm event audit mới: được thêm vào mảng trong `nukeEvents.js`, đừng tự `client.on`.

## Phải giữ

- `DANGEROUS` là đúng bộ bảy quyền đang có. Thêm quyền mới phải nói rõ trong báo cáo, vì escalation và health đổi theo.
- `permissions.has(perm, false)` để Administrator không bị đếm thành mọi quyền.
- Anti-nuke: bỏ qua chính bot, chủ server, trusted user, trusted bot. Admin thường không miễn.
- Không có executor trong audit (5 entry, khớp target, < 5s): return, không đoán.
- Chỉ hành động khi đếm **bằng** `nuke.maxActions`, không lặp khi vượt.
- `act`: `roles.set` giữ role managed, @everyone, và role >= role cao nhất của bot.
- Escalation: không executor thì chỉ cảnh báo, không hoàn tác mù. Role không `editable` thì ghi thất bại.
- `oldMember.partial`: bỏ qua MemberUpdate.
- Đổi position role: `scheduleHealthCheck`, không check ngay lập tức.
- `analyze()` giữ thuần, không import discord.js. `ok` chỉ false khi có `error`.

## Xong khi

Ngưỡng nuke và hàm `analyze` vẫn test được không cần token. Ba lớp miễn không bị gộp vào `isExempt`.
