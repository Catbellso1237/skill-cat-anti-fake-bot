# skill-cat-anti-fake-bot

Skill cho AI (Claude, Cursor, Codex, Grok, va agent doc `SKILL.md`) khi lam viec voi bot Discord **Cat Anti Fake Bot**.

Bot nguon: [Catbellso1237/cat-anti-fake-bot](https://github.com/Catbellso1237/cat-anti-fake-bot)  
Snapshot skill: `1949df84c82633538f6d4db74b545edab4956ce6`

Skill khong chua token, khong chua source bot. No noi cach doc, cach sua cho khoi pha lockdown / duyet bot / phan quyen, va nhung viec AI khong duoc tu lam (bat `act`, commit `.env`, cap Administrator).

Lan sau, viec lon duoc chia cho bot con trong `workers/`. Chung la brief cho AI, khong phai token Discord thu hai.

## Cai cho AI khac

Copy ca repo thanh mot thu muc skill (ben trong phai co `SKILL.md`):

```bash
git clone https://github.com/Catbellso1237/skill-cat-anti-fake-bot.git cat-anti-fake-bot
```

| Agent | Dat thu muc vao |
|---|---|
| Claude Code | `~/.claude/skills/cat-anti-fake-bot/` hoac `.claude/skills/cat-anti-fake-bot/` trong repo bot |
| Cursor | `~/.cursor/skills/cat-anti-fake-bot/` hoac `.cursor/skills/cat-anti-fake-bot/` |
| Codex / agent tuong thich Agent Skills | `~/.agents/skills/cat-anti-fake-bot/` hoac `.agents/skills/cat-anti-fake-bot/` |
| Grok / agent doc repo | mo repo nay, hoac de `AGENTS.md` + `SKILL.md` trong context khi sua bot |

Ten skill la `cat-anti-fake-bot`. Mo ta o frontmatter cua `SKILL.md` la cau agent dung de quyet dinh co nap skill hay khong.

## Ben trong

| File | Noi dung |
|---|---|
| `SKILL.md` | Bot chinh. Doc truoc khi sua bot. |
| `workers/DISPATCH.md` | Khi nao giao viec, mau prompt, thu tu gop. |
| `workers/joins.md` | Raid, lockdown, nguoi vao, bot gia. |
| `workers/approval.md` | Duyet bot moi. |
| `workers/nuke.md` | Anti-nuke, leo thang, health. |
| `workers/spam.md` | Spam va mien spam. |
| `workers/surface.md` | Slash, panel, deploy. |
| `workers/qa.md` | Test, chay sau cung. |
| `references/architecture.md` | File map va luong. |
| `references/commands.md` | Slash, panel, env, quyen moi. |
| `references/invariants.md` | Luat cam pha. |
| `references/extending.md` | Them command, event, test. |

## Giay phep

[MIT](LICENSE). Bot nguon cung khai MIT trong `package.json`.
