# Bot con: nuke

Viec: xoa kenh/role va ban hang loat, cap quyen nguy hiem trai phep, kiem tra thu bac role.

## So huu

- `src/protection/antiNuke.js`
- `src/protection/hierarchy.js`
- `src/protection/hierarchyCheck.js`
- `src/events/nukeEvents.js`
- `src/events/escalationEvents.js`

## Cam dung

`exempt.js` (spam giu), `raid.js`, `botApproval.js`, command, panel, file bot chinh giu.
Can them quyen vao `REQUIRED`: duoc, vi file nay la cua ban. Sua ca dong mo ta tieng Viet.
Can them event audit moi: duoc them vao mang trong `nukeEvents.js`, dung tu `client.on`.

## Phai giu

- `DANGEROUS` la dung bo bay quyen dang co. Them quyen moi phai noi ro trong bao cao, vi escalation va health doi theo.
- `permissions.has(perm, false)` de Administrator khong bi dem thanh moi quyen.
- Anti-nuke: bo qua chinh bot, chu server, trusted user, trusted bot. Admin thuong khong mien.
- Khong co executor trong audit (5 entry, khop target, < 5s): return, khong doan.
- Chi hanh dong khi dem **bang** `nuke.maxActions`, khong lap khi vuot.
- `act`: `roles.set` giu role managed, @everyone, va role >= role cao nhat cua bot.
- Escalation: khong executor thi chi canh bao, khong hoan tac mu. Role khong `editable` thi ghi that bai.
- `oldMember.partial`: bo qua MemberUpdate.
- Doi position role: `scheduleHealthCheck`, khong check ngay lap tuc.
- `analyze()` giu thuan, khong import discord.js. `ok` chi false khi co `error`.

## Xong khi

Nguong nuke va ham `analyze` van test duoc khong can token. Ba lop mien khong bi gop vao `isExempt`.
