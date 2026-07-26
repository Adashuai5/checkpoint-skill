# checkpoint

A runtime-neutral [Agent Skill](https://agentskills.io/) for durable project checkpoints and clean session handoffs.

Codex and Claude Code are the primary compatibility targets. Any Agent Skills-compatible coding agent with project-file access can use the same core workflow. Agents without native skill discovery can load `SKILL.md` manually and use the documented safe fallbacks.

The agent proactively reconciles verified decisions and state at meaningful task boundaries. Routine, objective, low-risk updates can be automatic when the surrounding project delegates memory maintenance. Ambiguous decisions, policy changes, conflicts, deletions, and sensitive information still require confirmation.

> Canonical project files are the source of truth. Hooks are lifecycle signals; chat history and generated memories are recall caches.

`/checkpoint` still produces a clean cross-thread or cross-runtime handoff. Ordinary syncs and reliable same-thread resumes do not create unnecessary opener text.

## Compatibility contract

The core skill does not depend on vendor-specific hooks, memory stores, event names, or frontmatter extensions.

| Runtime | Discovery | Checkpoint behavior |
|---|---|---|
| Codex | Native Agent Skills support | Full workflow; hooks optional |
| Claude Code | Native skills support | Full workflow; hooks optional |
| Other Agent Skills clients | Runtime-specific skill path | Full workflow when project files are readable/writable |
| Other file-working agents | Load `SKILL.md` manually | Explicit invocation; exact-patch fallback when read-only |

Other documented Agent Skills clients include [Cursor](https://cursor.com/docs/skills), [Gemini CLI](https://geminicli.com/docs/cli/using-agent-skills/), [OpenCode](https://opencode.ai/docs/skills), and [GitHub Copilot](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills). Their discovery paths and lifecycle features differ.

Lifecycle automation varies by runtime. An adapter may request `sync`, `compact`, `handoff`, or `resume`, but every runtime must still apply the same evidence, privacy, ownership, and confirmation gates.

## Install

Using the open `skills` CLI:

```bash
npx skills add Adashuai5/checkpoint-skill -g -a codex -a claude-code
```

Manual discovery locations:

- Cross-client / Codex: `~/.agents/skills/checkpoint/`
- Claude Code: `~/.claude/skills/checkpoint/`

For a repository-scoped install, use `.agents/skills/checkpoint/` and `.claude/skills/checkpoint/` as required by the active runtimes.

The canonical skill directory in this repository is `skills/checkpoint/`. Keep one checkout. Use installer-managed links or symlinks only where the runtime version supports them; do not hand-maintain divergent copies.

## Triggers

Explicit: `/checkpoint`, "wrap up", "save state", "handoff", "context is getting long", 收尾, 结一下, 存档, 上下文太长了.

Proactive: durable decisions or state changes, meaningful task boundaries, pre-compaction, and actual cross-runtime handoffs. It intentionally does not run on every ordinary turn.

## License

MIT
