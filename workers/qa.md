# Bot con: qa

Chạy **sau** các bot khác, khi code đã gộp. Không song song với bot đang sửa cùng file test.

## Sở hữu

- `tests/**/*.js`
- `scripts/check.js`

Được đọc mọi file. Không sửa file ngoài hai chỗ trên. Test đỏ phải sửa production thì báo bot chính, đừng "sửa tạm" module bảo vệ.

## Việc

1. Đọc `references/invariants.md` và báo cáo của các bot vừa chạy.
2. Thêm test thuần cho hành vi mới: ngưỡng, parse customId, `analyze`, preset, mask webhook, exempt. Không login Discord, không đọc token.
3. Chạy `npm run check` và `npm test`. Fail thì sửa test hoặc trả lời đúng file production nào sai luật.
4. Đối chiếu luật, không chỉ nhìn test xanh:
   - mode mặc định vẫn `log`
   - ba lớp miễn không bị gộp
   - lockdown vẫn qua `exclusive()`
   - confirm mới trust bot
   - anti-nuke chỉ hành động đúng lúc bằng ngưỡng
   - không có `.env`, token, webhook thật, `data/config.json` trong diff

## Xong khi

Hai lệnh pass, và bạn ngắn: đã test gì, luật nào không kiểm được bằng test thuần (cần server thật).
