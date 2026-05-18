# Improvement planning — revise existing OR new product improvement

This is the procedure when SKILL.md routed to improvement mode. Unlike greenfield, the debate loop is **opt-in** here — ask the user.

## Step 1 — Disambiguate the user's intent

Use `AskUserQuestion` with this question and two options:

> "Are you improving an existing plan, or planning a new product improvement?"
>
> - **Revise an existing plan** — rework one of the existing `<slug>-plan/` plans. The current one will be archived.
> - **New product improvement** — plan a new feature on top of what's already shipped. A fresh `<slug>-plan/` will be created.

Branch on the user's choice.

## Branch A — Revising an existing plan

### A1. Pick the target plan

List the existing `<slug>-plan/` folders under `docs/plans/` (exclude `plan-archive/`). Use `AskUserQuestion` to let the user pick which one. Read the existing `plan.md` from that folder before continuing — its content is the seed for the revision.

### A2. Ask: debate or single-shot?

Use `AskUserQuestion`:

> "How do you want to revise this plan?"
>
> - **Single-shot revision (Recommended)** — the orchestrator drafts the revised plan directly using the existing plan as context.
> - **Debate loop** — same procedure as greenfield, seeded with the existing plan.

### A3. Auto-archive the existing plan

Before writing anything new, move the existing `<slug>-plan/` to `docs/plans/plan-archive/<slug>-plan-<YYYYMMDD-HHMMSS>/`. Use `Bash` with a portable `mv` command and a timestamp from `date +%Y%m%d-%H%M%S`. Then create a fresh empty `docs/plans/<slug>-plan/`.

Announce the archive: "Archived previous plan to `docs/plans/plan-archive/<slug>-plan-<timestamp>/`."

### A4. Produce the revised plan

- **Single-shot path:** the orchestrator drafts a revised `plan.md` directly. Sections: Context (include "What changed and why" referencing the archived plan), Approach, Files to create/modify, Verification. Write to `docs/plans/<slug>-plan/plan.md`.
- **Debate path:** follow `greenfield.md`'s debate loop, but include the archived plan in the proposer prompts as: "**Existing plan being revised (do not blindly inherit — assume parts may be wrong):** <verbatim archived plan>". Final output also lands at `docs/plans/<slug>-plan/plan.md` after the user picks.

## Branch B — New product improvement (new feature, not a revision)

### B1. Generate the slug and create the directory

Same slug rules as SKILL.md (kebab-case, ≤6 words, strip trailing `-plan`). Create `docs/plans/<slug>-plan/` (empty). No archival.

### B2. Ask: debate or single-shot?

Use `AskUserQuestion`:

> "How do you want to plan this improvement?"
>
> - **Single-shot (Recommended)** — orchestrator drafts the plan directly.
> - **Debate loop** — same procedure as greenfield.

### B3. Produce the plan

- **Single-shot:** orchestrator writes `docs/plans/<slug>-plan/plan.md` with the four sections. Reference any existing plans in `docs/plans/` only when relevant — don't force a connection.
- **Debate:** follow `greenfield.md`. No seed plan needed.

## Hard rules for improvement mode

- **Always ask whether to debate.** Never auto-debate in improvement mode (greenfield is the only auto-debate path).
- **Never overwrite an existing `plan.md` without archiving first** (Branch A). Archival is mandatory and uses the timestamped `plan-archive/<slug>-plan-<timestamp>/` pattern.
- **Don't archive on Branch B** — new-feature plans are additive, not replacements.
- **Same sub-agent model rules** as greenfield: `general-purpose` + sonnet for any agent calls.
