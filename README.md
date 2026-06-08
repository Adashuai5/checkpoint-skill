# checkpoint

A Claude Code / Agent Skill that hands a long session off to a **file**, so a fresh session can resume cheaply — without re-deriving context or hallucinating.

Long sessions rot: early detail blurs, hallucination rises, and the harness silently auto-summarizes (dropping detail you can't see). This skill keeps the source of truth in files, not in the conversation.

When you (or the model) sense the chat is getting long, `/checkpoint`:

1. finds your project's **existing** memory file (`MEMORY.md` / `HANDOFF.md` / `findings.md` / `CLAUDE.md` / `项目记忆.md`),
2. distills this session's durable decisions & state into it — **after showing you for confirmation**,
3. emits a paste-able **opener** for your next session (which files to read first + current thrust + next action).

> Principle: the source of truth lives in files, not in conversation memory.

## Install

```bash
npx skills add Adashuai5/checkpoint-skill
```

Or copy `skills/checkpoint/SKILL.md` into your `~/.claude/skills/checkpoint/`.

## Triggers

`/checkpoint`, "wrap up", "save state", "handoff", "context is getting long" — and Chinese: 收尾 / 结一下 / 存档 / 上下文太长了.

## License

MIT
