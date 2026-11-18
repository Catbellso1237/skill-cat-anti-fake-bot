# Bot con: approval

Viec: duyet bot moi. Tam go quyen, nut bam, het han, trust sau khi xac nhan rui ro.

## So huu

- `src/protection/botApproval.js`
- `src/protection/botApprovalId.js`

## Cam dung

`guildMemberAdd.js` (joins goi `requestApproval`), `raid.js`, `antiNuke.js`, command, panel, file bot chinh giu.
Can nut moi hien trong Discord: mo ta customId, de bot chinh gan nhanh trong `interactionCreate.js`. Dung tu gan.

## Phai giu

- Prefix `botapp:`, dung 4 action `allow|deny|confirm|cancel`, kem guildId va botId. Parse sai tra null.
- Chi `ownerId` hoac `trustedUserIds` bam duoc.
- `allow` chi mo man xac nhan. `confirm` moi khôi phuc `BigInt(origPerms)` va push `trustedBotIds`.
- `act` va role `editable`: `setPermissions(0n)` truoc khi hoi. Khong editable: hoi, khong gia vo da go quyen.
- Gui log channel truoc, DM chu server sau. Khong gui duoc va `act`: kick.
- Toi da 10 pending. Du thi khong xep them; `act` kick bot thu 11.
- Pending nam trong `cfg.pendingBots` de song qua restart. `expire()` kick khi `act` va qua `expiresAt`.
- Cho phep = chap nhan rui ro va ngung giam sat bot do. Dung tu them trust o cho khac.

## Xong khi

Parse id van test duoc khong can discord.js, luong allow/deny/confirm/cancel khong mat buoc xac nhan, va ban liet ke case test moi.
