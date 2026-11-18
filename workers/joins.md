# Bot con: joins

Việc: người vào server, phát hiện raid, lockdown, tài khoản quá mới, bot giả lúc vừa vào.

## Sở hữu

- `src/protection/raid.js`
- `src/protection/antiBot.js`
- `src/events/guildMemberAdd.js`

## Cấm đụng

`botApproval.js`, `antiNuke.js`, `messageCreate.js`, mọi file trong `src/commands/`, `src/panel/`, và file bot chính giữ (`index.js`, `config.js`, `ready.js`, `interactionCreate.js`, `utils/`).

Cần thêm key vào `DEFAULTS`, đổi tick sweep, hoặc đổi nút verify: dừng, ghi yêu cầu cho bot chính.

## Phải giữ

- `recordJoin` chỉ đếm trong `raid.windowMs`. Raid khi đủ `maxJoins`.
- `startRaid` lần sau trong cùng đợt chỉ kéo `until` và thêm id, không khóa lần nữa. Trả `false`.
- Lockdown đi qua `exclusive()`. Không tự gán `cfg.lockdown`.
- Không khóa `logChannelId`. Snapshot: true / false / null. Mở khóa đúng nghĩa đó.
- Timeout cả nhóm chỉ khi `mode === 'act'` và `moderatable`.
- Người vào lúc raid đang active: `markRaider` + quarantine, không gọi lại lockdown.
- Tài khoản mới: so `minAccountAgeMs`, chỉ phạt khi `act`.
- Bot: `checkBotAdder` (sleep 1.5s, audit BotAdd < 30s). Không thấy người thêm thì `unknown`, không kick mù.
- Trusted và không lookalike: bỏ qua. Lookalike không bao giờ tự ban chỉ vì tên.
- `botApproval.enabled` và chưa trust: gọi `requestApproval`, không tự viết lại luồng duyệt.
- Tắt duyệt: chỉ kick ở `act` khi biết người thêm và người đó không tin cậy.

## Xong khi

Hành vi join/raid đổi đúng mục tiêu, file ngoài danh sách không đổi, và bạn ghi rõ test thuần nào cần thêm (ngưỡng join, impersonation, unknown adder).
