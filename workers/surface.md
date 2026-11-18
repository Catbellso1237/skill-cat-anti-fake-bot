# Bot con: surface

Viec: slash command, bang dieu khien, nut verify da dang san, chuoi deploy lenh.

## So huu

- `src/commands/*.js`
- `src/panel/controlPanel.js`
- `src/panel/presets.js`
- `src/deploy-commands.js`

## Cam dung

`src/events/interactionCreate.js` la cua bot chinh. Nut hoac menu **moi** (prefix moi) thi dung, mo ta customId, de bot chinh gan mot nhanh.
Sua nut `verify`, `botapp:`, `panel:` san co thi duoc doi payload trong file ban so huu, nhung khong doi prefix.
Cam: `raid.js`, `botApproval.js`, `antiNuke.js`, `config.js`.

## Phai giu

- Lenh moi: `{ data, execute, cooldownSeconds }` tieng Viet, ephemeral, quyen Discord dung viec (xem `references/commands.md`).
- Lenh pha kenh hoac khoa server: ManageGuild tro len, khong tat cooldown.
- Doi builder thi noi ro "can `npm run deploy` mot lan". Khong goi deploy tu `index.js`.
- Preset chi o `presets.js`: low 12/10s, mid 8/10s, high 5/10s, max 3/8s. `presetOf` tra null neu lech. Dung ep config lech ve preset.
- Panel kiem tra lai quyen luc bam, khong tin luc gui.
- `/security` la cho cau hinh bao ve. Dung tao slash moi cho tung co nho.
- Khong in webhook day du. Dung `maskWebhook`.

## Xong khi

Command moi nam trong `src/commands/` de loader tu thay, preset van test thuan, va ban ke nhung gi bot chinh phai gan trong `interactionCreate.js`.
