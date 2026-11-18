# Bot con: joins

Viec: nguoi vao server, phat hien raid, lockdown, tai khoan qua moi, bot gia luc vua vao.

## So huu

- `src/protection/raid.js`
- `src/protection/antiBot.js`
- `src/events/guildMemberAdd.js`

## Cam dung

`botApproval.js`, `antiNuke.js`, `messageCreate.js`, moi file trong `src/commands/`, `src/panel/`, va file bot chinh giu (`index.js`, `config.js`, `ready.js`, `interactionCreate.js`, `utils/`).

Can them key vao `DEFAULTS`, doi tick sweep, hoac doi nut verify: dung, ghi yeu cau cho bot chinh.

## Phai giu

- `recordJoin` chi dem trong `raid.windowMs`. Raid khi du `maxJoins`.
- `startRaid` lan sau trong cung dot chi keo `until` va them id, khong khoa lan nua. Tra `false`.
- Lockdown di qua `exclusive()`. Khong tu gan `cfg.lockdown`.
- Khong khoa `logChannelId`. Snapshot: true / false / null. Mo khoa dung nghia do.
- Timeout ca nhom chi khi `mode === 'act'` va `moderatable`.
- Nguoi vao luc raid dang active: `markRaider` + quarantine, khong goi lai lockdown.
- Tai khoan moi: so `minAccountAgeMs`, chi phat khi `act`.
- Bot: `checkBotAdder` (sleep 1.5s, audit BotAdd < 30s). Khong thay nguoi them thi `unknown`, khong kick mu.
- Trusted va khong lookalike: bo qua. Lookalike khong bao gio tu ban chi vi ten.
- `botApproval.enabled` va chua trust: goi `requestApproval`, khong tu viet lai luong duyet.
- Tat duyet: chi kick o `act` khi biet nguoi them va nguoi do khong tin cay.

## Xong khi

Hanh vi join/raid doi dung muc tieu, file ngoai danh sach khong doi, va ban ghi ro test thuat nguoi can them (nguong join, impersonation, unknown adder).
