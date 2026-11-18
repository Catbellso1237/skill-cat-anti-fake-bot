# skill-cat-anti-fake-bot

Skill cho AI (Claude, Cursor, Codex, Grok, và agent đọc `SKILL.md`) khi làm việc với bot Discord **Cat Anti Fake Bot**.

Bot nguồn: [Catbellso1237/cat-anti-fake-bot](https://github.com/Catbellso1237/cat-anti-fake-bot)  
Snapshot skill: `1949df84c82633538f6d4db74b545edab4956ce6`

Skill không chứa token, không chứa source bot. Nó chỉ nói cách đọc, cách sửa cho khỏi phá lockdown / duyệt bot / phân quyền, và những việc AI không được tự làm (bật `act`, commit `.env`, cấp Administrator).

## Cài cho AI khác

Copy cả repo thành một thư mục skill (bên trong phải có `SKILL.md`):

```bash
git clone https://github.com/Catbellso1237/skill-cat-anti-fake-bot.git cat-anti-fake-bot
```

| Agent | Đặt thư mục vào |
|---|---|
| Claude Code | `~/.claude/skills/cat-anti-fake-bot/` hoặc `.claude/skills/cat-anti-fake-bot/` trong repo bot |
| Cursor | `~/.cursor/skills/cat-anti-fake-bot/` hoặc `.cursor/skills/cat-anti-fake-bot/` |
| Codex / agent tương thích Agent Skills | `~/.agents/skills/cat-anti-fake-bot/` hoặc `.agents/skills/cat-anti-fake-bot/` |
| Grok / agent đọc repo | mở repo này, hoặc để `AGENTS.md` + `SKILL.md` trong context khi sửa bot |

Tên skill là `cat-anti-fake-bot`. Mô tả ở frontmatter của `SKILL.md` là câu agent dùng để quyết định có nạp skill hay không.

## Bên trong

| File | Nội dung |
|---|---|
| `SKILL.md` | Cửa vào. Đọc trước khi sửa bot. |
| `references/architecture.md` | File map và luồng raid, bot, nuke, spam. |
| `references/commands.md` | Slash, panel, env, quyền mời. |
| `references/invariants.md` | Luật cấm phá. |
| `references/extending.md` | Thêm command, event, test. |

## Giấy phép

[MIT](LICENSE). Bot nguồn cũng khai MIT trong `package.json`.
