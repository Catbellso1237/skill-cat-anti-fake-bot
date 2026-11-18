# Bot con: qa

Chay **sau** cac bot khac, khi code da gop. Khong song song voi bot dang sua cung file test.

## So huu

- `tests/**/*.js`
- `scripts/check.js`

Duoc doc moi file. Khong sua file ngoai hai cho tren. Test do phai sua production thi bao bot chinh, dung "sua tam" module bao ve.

## Viec

1. Doc `references/invariants.md` va bao cao cua cac bot vua chay.
2. Them test thuan cho hanh vi moi: nguong, parse customId, `analyze`, preset, mask webhook, exempt. Khong login Discord, khong doc token.
3. Chay `npm run check` va `npm test`. Fail thi sua test hoac tra loi dung file production nao sai luat.
4. Doi chieu luat, khong chi nhin test xanh:
   - mode mac dinh van `log`
   - ba lop mien khong bi gop
   - lockdown van qua `exclusive()`
   - confirm moi trust bot
   - anti-nuke chi hanh dong dung luc bang nguong
   - khong co `.env`, token, webhook that, `data/config.json` trong diff

## Xong khi

Hai lenh pass, va ban ngan: da test gi, luat nao khong kiem duoc bang test thuan (can server that).
