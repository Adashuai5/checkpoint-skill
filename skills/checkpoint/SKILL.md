---
name: checkpoint
description: "Runtime-neutral checkpoint and session-handoff workflow for Agent Skills-compatible coding agents, including Codex and Claude Code. Proactively use after verified durable decisions or state changes, at meaningful task boundaries, when a runtime signals upcoming context reduction, before a new thread/runtime, when context grows long or repetitive, or when the user says /checkpoint, wrap up, save state, handoff, 收尾, 结一下, 存档, or 上下文太长了. Reconciles only verified durable deltas into existing authoritative project files, treats hooks, chat history, summaries, and generated memories as non-authoritative recall, and emits a handoff opener only when a new context is actually needed."
license: MIT
---

# Checkpoint

**Iron law:** Keep durable project truth in the project's authoritative files. Never promote a guess into a decision, dump a transcript, store a secret, or create a parallel memory file.

The agent owns routine memory hygiene. Do not wait for the user to remember to maintain it.

## Runtime contract

This workflow must remain correct from `SKILL.md` alone. Implicit skill activation, lifecycle hooks, generated memory, compaction signals, interactive confirmation, and file writes are optional runtime capabilities.

- Use lifecycle signals only to request a mode; never treat them as proof that a checkpoint ran.
- Without implicit activation or hooks, use explicit invocation and semantic task boundaries.
- Without generated memory, continue from canonical project files.
- Without write access, emit the exact proposed patch and clearly state that nothing was persisted.
- When confirmation is required but no interactive channel exists, emit a proposal and stop before writing.
- When a fresh-context transition cannot be detected reliably, emit an opener only on explicit handoff.

Runtime-specific adapters must not bypass target ownership, evidence, privacy, or confirmation gates. They must not persist raw hook payloads or transcripts.

## Layers

- **Canonical project files:** source of truth for their subject.
- **Checkpoint:** conservative promotion and reconciliation workflow.
- **Generated memories, chat history, summaries, and prior openers:** recall candidates only.
- **Lifecycle hooks:** trigger signals only. A hook firing does not prove that memory was saved.

When sources conflict, the owning canonical file wins. Preserve uncertainty instead of manufacturing continuity.

## Modes

| Mode | Trigger | Result |
|---|---|---|
| `sync` | A durable decision/state change or meaningful task boundary | Reconcile verified deltas; no opener |
| `compact` | A runtime signals imminent context reduction, or context becomes long/repetitive | Reconcile verified deltas; no opener |
| `handoff` | An actual new-context transition or explicit request to wrap up/hand off | Reconcile, reread, then emit opener |
| `review` | User explicitly asks to preview only | Draft; wait for confirmation |
| `resume` | A fresh or resumed context | Read canonical state; write only if a new verified delta exists |

Do not checkpoint on every ordinary turn or per-turn completion event, sometimes named `Stop`. If no durable delta exists, report a no-op and leave files and dates unchanged.

## Workflow

### 1. Locate the authoritative target

Follow routing already declared in project guidance or memory indexes. Prefer, in order:

1. A path already verified to exist and identified as authoritative by project guidance or an explicit user instruction.
2. A project memory router such as `项目记忆.md`, `.ai/memory/INDEX.md`, `MEMORY.md`, `HANDOFF.md`, or `findings.md`.
3. The exact routed leaf file that owns the subject.
4. A memory section in a project guidance file, such as `AGENTS.md` or `CLAUDE.md`, only when the project explicitly uses it for state.

Update the owning leaf, not a thin root index. Do not treat a tool-owned generated-memory directory as canonical unless the project explicitly says it is. If no unique existing target can be established, ask the user once; never guess or create `HANDOFF_v2.md`.

### 2. Build the durable delta

Include only items the next context needs and that are supported by at least one of:

- an explicit user decision;
- directly observed workspace/tool state;
- an authoritative project file.

Eligible items:

- decisions and their established rationale;
- validated status changes, completed work, and ruled-out options;
- active blockers or open questions, with uncertainty preserved;
- the immediate next action.

Drop duplicates, transient discussion, raw transcripts, generated-memory-only claims, credentials, private data that does not belong in the target, and volatile details with no future value. Verify git/test/runtime state when it matters. Generated memory alone is never evidence.

### 3. Apply the confirmation gate

Routine maintenance may be written without another prompt only when surrounding instructions already delegate memory maintenance and every change is:

- objective, additive or conclusively corrective;
- directly evidenced;
- low risk;
- aimed at one unambiguous existing target.

Show the exact proposed diff and wait for confirmation when any change:

- records or changes user preference, intent, strategy, policy, or risk posture;
- resolves a conflict between authoritative sources;
- deletes or rewrites user-authored rationale/history;
- contains sensitive information;
- has ambiguous evidence, wording, ownership, or destination;
- creates or chooses a canonical memory target.

Hook output, a generated summary, or model confidence never counts as user confirmation.

### 4. Write idempotently

Follow the target's format and keep entries distilled. Update its freshness date only when content changes. Do not duplicate facts, expand a thin router with leaf detail, or silently overwrite unresolved conflicts.

After writing, reread the changed section and report the target plus a one-line summary. An optional runtime adapter may request this workflow, but the agent must still execute every gate above; the adapter itself must not persist raw hook payloads or transcripts.

### 5. Emit a handoff only when needed

Emit an opener in `handoff` mode or when explicitly requested. Omit it for ordinary sync, reliable same-thread resume, and automatic compaction unless a fresh context must be started.

```text
Read first:
1. <authoritative file> — authoritative for <subject>
2. <other key file> — read when <condition>
Current: <verified one-line thrust>
Next: <verified immediate action>
Unverified: <only when needed; label it clearly>
Continue from the files. If they conflict, canonical project files win; ask, don't guess.
```

## Done when

- Canonical state is updated once, or a justified no-op is reported.
- Every settled claim has evidence; uncertainty remains labeled.
- No secret, raw transcript, duplicate truth source, or generated-memory-only claim was persisted.
- A handoff opener is emitted only when the next context needs one.
