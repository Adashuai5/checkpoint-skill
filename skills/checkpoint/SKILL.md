---
name: checkpoint
description: Session-handoff / checkpoint routine. Flush this session's durable conclusions into the project's existing memory file, then emit a paste-able "new session opener" so a fresh session resumes cheaply without re-deriving context or hallucinating. Use when the user types /checkpoint or says 收尾 / 结一下 / 存档 / handoff / 上下文太长了 / "context is getting long" / "let's wrap up" / "start a new session" / "save state", OR when you (the model) sense the conversation has grown long, started repeating, or is about to be auto-compacted. Encodes the principle: source of truth lives in files, not in conversation memory.
---

# Checkpoint — hand the session off to a file

**Iron Law: Only write what was actually decided or established this session. Never invent or upgrade a tentative idea into a "decision," never dump raw conversation, and never create a parallel memory file — update the project's existing one.**

Why this exists: a fresh session should resume from FILES, not from a long chat. Long sessions rot — early detail blurs, hallucination rises, and the harness silently auto-summarizes (dropping detail you can't see). A clean file beats a 100-turn context.

## Workflow

```
- [ ] 1. Locate the canonical memory file        ⛔ BLOCKING
- [ ] 2. Diff: what's new this session, not yet in the file
- [ ] 3. Draft updates + opener, SHOW the user    ⚠️ confirm before writing
- [ ] 4. Write to the file
- [ ] 5. Emit the paste-able new-session opener
```

### 1. Locate the canonical memory file ⛔ BLOCKING

Find the project's EXISTING memory/handoff file — do not create a new one. Look, in order:
- a memory path already referenced this session (e.g. `项目记忆.md`)
- `项目记忆.md`, `MEMORY.md`, `HANDOFF.md`, `findings.md` in the working dir or project root
- a memory section inside `CLAUDE.md`

If none exists, ask the user where project memory lives (or whether to create one). Never guess a path.

### 2. Diff — what changed this session

Scan the session for DURABLE items not yet captured:
- decisions made (and the why)
- conclusions reached / options ruled out
- state changes (status, positions, what's done vs pending)
- open questions / next steps

Per item ask: "Will the next session need this, AND was it actually established?" If no to either → drop it. Ignore transient back-and-forth and anything already in the file.

### 3. Draft + confirm ⚠️

Show the user, in the user's language:
- the exact additions/edits you'll make (a short list, not prose)
- the new-session opener

Wait for approval. They may trim or correct. Do not write until they confirm.

### 4. Write

Append/update the canonical file; update its "last updated" date. Keep entries distilled — one line per fact where possible; follow the file's existing format. Don't delete prior content unless it's now wrong (if so, say which and why).

### 5. Emit the opener

Output a short, paste-able snippet in the user's language, with these fields:
- which file(s) to read first (canonical memory file + any other key files, each with a one-line "when to read")
- the one-line current thrust
- the immediate next action
- "continue from here; ask if unclear, don't guess"

Example (Chinese):

```
> 先读这些文件再开工,别重推:
> 1. <canonical memory file path>(先读这个)
> 2. <other key file(s) if any, with one-line "when to read">
> 我们在做:<one-line thrust>
> 当前这一步:<the immediate next action>
> 接着上一轮往下做,不清楚先问我、别猜。
```

## Anti-patterns

- ❌ Dumping conversation transcript into the file — distill to durable facts only.
- ❌ Inventing or upgrading a tentative idea into a "decision" — if unsettled, mark it 未决, don't assert it.
- ❌ Creating `HANDOFF_v2.md` / a new file when a memory file already exists — update the existing one.
- ❌ Writing before showing the user — always confirm (step 3).
- ❌ Vague entries ("讨论了策略") — write the actual conclusion ("定了 X,因为 Y").

## Done when

- [ ] Updates written to the existing canonical file, dated, distilled.
- [ ] Zero invented/unconfirmed claims.
- [ ] Paste-able opener emitted, naming the file(s) to read first + current thrust + next action.
