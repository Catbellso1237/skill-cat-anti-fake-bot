# Điều phối bot con

Dùng mỗi lần sau khi sửa [cat-anti-fake-bot](https://github.com/Catbellso1237/cat-anti-fake-bot) mà việc đụng hơn một mảng.

## Khi nào giao

- Một mảng (chỉ raid, chỉ lệnh, chỉ test): không giao. Đọc đúng một brief rồi làm.
- Hai mảng trở lên, file không chồng: giao song song.
- Cùng sửa `config.js`, `interactionCreate.js`, `index.js`, `ready.js` hoặc `utils/`: **không** giao. Bot chính sửa sau khi bot con báo cần gì.

## Bot chính làm trước

1. Đọc `SKILL.md` và `references/invariants.md`.
2. Viết một câu mục tiêu và danh sách bot con được gọi. Bỏ bot không liên quan.
3. Nêu ràng file nào bot con không được đụng vào.
4. Qa chỉ chạy khi các bot kia xong.

## Prompt giao (copy, điền ngọc)

```
Bạn là bot con "<tên>" của Cat Anti Fake Bot.
Đọc workers/<tên>.md và references/invariants.md. Làm đúng phần việc trong brief.
Mục tiêu lần này: <một câu>.
Repo bot: Catbellso1237/cat-anti-fake-bot. Chỉ sửa file bạn sở hữu.
Không sửa src/index.js, src/config.js, src/events/ready.js,
src/events/interactionCreate.js, src/utils/.
Nếu thiếu một default, một nút, hoặc một hàm util, dừng lại và ghi rõ cần bot chính thêm gì.
Xong thì báo: file đã sửa, hành vi đổi, test cần thêm, rủi ro đụng luật.
Không commit token, .env, webhook, data/config.json. Không bật mode act.
```

## Thứ tự gộp

1. Nhận báo cáo từng bot. Chỗi nếu ai sửa file ngoài danh sách.
2. Bot chính mới sửa file dùng chung (default mới, nhánh nút, tick trong `ready`).
3. Giao `workers/qa.md` với danh sách file vừa đổi.
4. `npm run check` và `npm test` phải pass trước khi kết thúc.
5. Một commit (hoặc một PR) cho cả lần, message nói mảng nào đổi. Không mỗi bot con một commit dở dang.

## Thêm bot con mới

Chỉ khi xuất hiện một nhóm file chưa có chủ và hay sửa cùng nhau. Viết `workers/<tên>.md` cùng dạng các brief kia, thêm một dòng vào bảng ở `SKILL.md`. Không tạo bot con cho một file tiện ích.
