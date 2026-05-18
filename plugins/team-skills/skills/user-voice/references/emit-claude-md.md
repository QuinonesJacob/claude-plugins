# Emit: write the Voice Profile into CLAUDE.md

You reach this doc when the user chose the CLAUDE.md output (or both). The goal
is to make the voice rules load automatically for sessions in scope, without
ever damaging an existing CLAUDE.md.

## The sentinel contract

The voice rules always live inside an exactly-matched delimited block:

```
<!-- BEGIN user-voice (managed by the user-voice skill — edit via /user-voice) -->
... rendered voice rules ...
<!-- END user-voice -->
```

Every read and every write targets only the span between `<!-- BEGIN
user-voice` and `<!-- END user-voice -->`. Anything outside those sentinels is
never parsed for rewrite and never reformatted. This is what makes repeated
runs safe: refine replaces the block, it does not regenerate the file.

## Step 1 — choose the target file

`AskUserQuestion`:

- **User-level `~/.claude/CLAUDE.md` (recommended)** — applies to all of the
  user's sessions. This matches the skill's scope: every message Claude sends
  on the user's behalf, in any project. On Windows the path is
  `C:\Users\<user>\.claude\CLAUDE.md`.
- **Project `./CLAUDE.md`** — only this repo/project.
- **Both** — write the block into each (run Step 2 per file).

## Step 2 — write, by file state

Read the target file first (if it exists) and branch:

**Case A — file does not exist.** (This is the user's current state; there is
no `~/.claude/CLAUDE.md` today.) Create it minimal: a single short header
comment saying this file is Claude project memory, one blank line, then the
sentinel block. Do not invent unrelated CLAUDE.md content — the user did not
ask for a memory file, they asked for voice rules.

**Case B — file exists, no sentinels.** Read it. Append one blank line and the
sentinel block at the end. Do not touch, reorder, or reformat the existing
content. You are adding, not editing.

**Case C — file exists, sentinels present.** This is the refine path. Replace
only the text from `<!-- BEGIN user-voice` through `<!-- END user-voice -->`
inclusive, with the freshly rendered block. Match the sentinels literally
(string match, not fuzzy). Everything before the BEGIN and after the END stays
byte-for-byte.

## Rendering the profile into the block

Convert the structured Voice Profile into imperative rules a session will
actually follow. Start the block with a scope line, then the rules, then the
channel table verbatim, then a stamp.

Template for the block body:

```
<!-- BEGIN user-voice (managed by the user-voice skill — edit via /user-voice) -->
## Writing voice (messages sent on the user's behalf)

These rules apply ONLY to messages Claude composes as the user — email,
Slack, Teams, Outlook, iMessage/SMS, PR and code-review comments. They do
NOT apply to code, code comments, commit bodies, documentation, or
analytical responses to the user.

- Never use em dashes or "--" as connectors. Use a period or semicolon; if a
  dash joins two clauses, split into two sentences. <or the user's chosen
  replacement>
- Do not open with scaffolding ("I hope this finds you well", "I wanted to
  reach out", "I just wanted to"). Start with the point.
- Never use: <banned_ai_tells list, comma-joined>.
- Assume shared context (level <dial>/5): <context directive>. Do not explain
  things the recipient already knows.
- Length: <length_discipline>. No preamble, no closing recap of what was
  just said.
- Match the channel register from the table below. When the channel is
  ambiguous, ask before sending.
- Signature: <per-channel signatures>.

| channel | register | greeting | sign-off | emoji |
|---------|----------|----------|----------|-------|
<rows from formality_by_channel>

<!-- user-voice: last updated YYYY-MM-DD; profile confidence: <samples> samples, channels: <list> -->
<!-- END user-voice -->
```

Keep it tight. This block is read every session, so every line has to earn
its place. Do not paste the raw schema; render it as the short list above.

## Step 3 — verify

- The BEGIN and END sentinels appear exactly once each and BEGIN precedes END.
- For Case B/C: capture the bytes outside the block before and after the
  write and confirm they are identical. If they differ, you edited outside
  the span — revert and redo.
- Print the final block and the file path back to the user.
