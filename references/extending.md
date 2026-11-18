# Thêm tính năng

Làm trong repo [cat-anti-fake-bot](https://github.com/Catbellso1237/cat-anti-fake-bot), không sửa repo skill này trừ khi hợp đồng bot đổi.

## Command mới

Tạo `src/commands/<tên>.js`:

```js
'use strict';
const { SlashCommandBuilder, PermissionFlagsBits, MessageFlags } = require('discord.js');
const { getConfig, saveConfig } = require('../config');

const data = new SlashCommandBuilder()
  .setName('ten')
  .setDescription('Mô tả ngắn tiếng Việt')
  .setDefaultMemberPermissions(PermissionFlagsBits.ManageGuild);

async function execute(interaction) {
  const cfg = getConfig(interaction.guildId);
  // sửa cfg...
  await saveConfig();
  return interaction.reply({ content: 'Xong.', flags: MessageFlags.Ephemeral });
}

module.exports = { data, execute, cooldownSeconds: 3 };
```

Loader tự nạp. Chạy `npm run deploy` một lần sau khi đổi builder.
Lệnh phá dữ liệu hoặc khóa server phải ManageGuild trở lên và ephemeral.

Subcommand dài nên đi vào `/security` nếu nó là cấu hình bảo vệ, không tạo slash mới cho mỗi cờ.

## Event mới

File trong `src/events/`. Export object hoặc mảng:

```js
module.exports = {
  name: Events.SomeEvent,
  async execute(...args) {
    // getConfig, tôn trọng mode, sendModLog khi có việc
  },
};
```

`once: true` chỉ cho thứ chạy một lần (ready đã dùng). Đừng đăng ký trùng event ở hai file trừ khi cố ý (loader gắn từng handler).

Audit log có độ trễ. Bot-add đã sleep 1500ms; hành động nuke chỉ nhận entry < 5s. Đừng siết cửa sổ hơn nếu chưa đo.

## Module bảo vệ mới

- Quyết định (có phạt không, parse id, so khớp) viết hàm không import discord.js nếu làm được. Nhìn `hierarchyCheck.js`, `botApprovalId.js`, `presets.js`, `antiSpam.js`.
- Tác động Discord nằm file kế bên, nhận `guild` / `member` đã fetch.
- Phạt chỉ khi `cfg.mode === 'act'`.
- Log một dòng rõ: tiêu đề, user, `action` là việc thật sự đã xảy ra ("Chỉ ghi log", "Timeout thất bại"...).
- Cần nhớ qua restart: field trong `DEFAULTS`, không phải `Map` module.
- Cần quyền Discord mới: cập nhật `REQUIRED` và đoạn mời bot trong README repo bot.

Nút bấm: thêm nhánh trong `interactionCreate.js`. Prefix riêng, parse chặt, từ chối id lạ. Kiểm tra quyền người bấm tại thời điểm bấm.

## Sửa config an toàn

```js
const cfg = getConfig(guild.id);
cfg.raid.maxJoins = 6;
await saveConfig();
```

Không:

```js
const cfg = structuredClone(getConfig(guild.id));
cfg.raid.maxJoins = 6;
await saveConfig(); // bản clone bị bỏ, store không đổi
```

## Test

```bash
npm run check   # cú pháp + require được mọi module
npm test        # node --test tests/*.test.js
```

Thêm case vào `tests/core.test.js` hoặc file `tests/<tên>.test.js` mới cho hàm thuần: ngưỡng, parse customId, analyze hierarchy, preset, webhook mask.
Đừng test bằng cách login Discord trong CI.

`scripts/check.js` chỉ nạp module. Đừng để top-level của file mới gọi mạng hoặc đọc token (trừ `index.js` và `deploy-commands.js`).

## Việc vận hành, không phải sửa code

Khi user muốn chạy bot, không tự đăng nhập bằng token trong chat.

1. Portal: bật intent, mời đủ quyền, role trên cùng.
2. `npm install`, điền `.env` từ `.env.example`, `npm run deploy`, `npm start`.
3. `/setup`, `/security webhook`, `/health`, `/panel verify` nếu dùng xác minh.
4. Để `log` vài ngày. Đọc mod-log. Rồi mới `/security mode act`.
5. 24/7: `pm2 start src/index.js --name cat-anti-fake-bot --max-restarts 10 --restart-delay 5000`.
6. Backup `data/config.json`. Mất file này là mất trust list, pending bot, snapshot lockdown.

## Cập nhật skill này

Chỉ sửa repo `skill-cat-anti-fake-bot` khi hành vi bot đổi (command mới, đổi default, đổi mutex, đổi customId). Ghi commit bot mới vào đầu `SKILL.md`. Đừng copy nguyên source vào skill.
