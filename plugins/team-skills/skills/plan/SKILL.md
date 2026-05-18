---
name: plan
description: "Bootstrap and maintain product plans in a project. Activates on phrases like 'p:plan', '/p:plan', 'make a product plan', 'draft a plan for', 'plan this feature', 'product plan for', 'I want to plan', or any user request to start or revise a product/feature plan. Enforces a docs/plans/<slug>-plan/plan.md directory pattern. For greenfield (no existing plans) it ALWAYS runs a debate loop between two proposer agents (simplest vs. most robust) refereed by a critique-only debater. For improvements it asks first whether to debate or single-shot, and auto-archives superseded plans."
version: 0.1.0
---

# p:plan — product planning skill

The job: take a user's planning request, set up the right directory under `docs/plans/`, then route to greenfield or improvement flow. The skill itself never writes `plan.md` — that's done by proposer agents (greenfield) or the orchestrator (single-shot improvement).

## The five things this skill does, in order

1. **Detect mode (greenfield vs improvement) from cwd.** Run a single check on `docs/plans/`:
   - Missing OR contains only `plan-archive/` → propose **greenfield**.
   - Contains one or more `<slug>-plan/` folders (excluding `plan-archive/`) → propose **improvement**.
2. **Confirm the mode.** Use `AskUserQuestion` with the auto-detected mode as the default (recommended) option, plus the other mode as override. The user can flip the routing if the auto-detection is wrong.
3. **Generate the slug** from the user's plan topic:
   - kebab-case
   - max ~6 meaningful words
   - strip a trailing `-plan` if the user typed it (avoid `foo-plan-plan/`)
   - examples: "let users export their data as CSV" → `csv-export`; "user onboarding" → `user-onboarding`
4. **Create the directory skeleton** in cwd:
   - `docs/plans/<slug>-plan/` (the active plan folder; empty for now)
   - `docs/plans/plan-archive/` (only if it doesn't already exist)
5. **Route to the matching reference doc.** Read it with the `Read` tool, then follow its instructions:
   - greenfield → `references/greenfield.md`
   - improvement → `references/improvement.md`

## Hard rules

- **Never write `plan.md` from this SKILL.md.** That is the proposer/orchestrator's job per the routed reference doc.
- **Always confirm the mode** before creating directories. The user may want a fresh greenfield plan even if `docs/plans/` already has plans.
- **Slug uniqueness.** Before creating `docs/plans/<slug>-plan/`, check it doesn't already exist. If it does and the user is in greenfield mode, append a numeric suffix (`-2`, `-3`, ...). If in improvement mode, the existing folder is the target — see `references/improvement.md`.
- **Cwd is the project.** The skill operates on `docs/` relative to the current working directory. Do not write into a parent or unrelated directory.
- **Sub-agent model.** Proposer A, Proposer B, and the Debater all use `subagent_type: general-purpose` with `model: sonnet`. Do not use opus or haiku for these roles unless the user overrides.

## Outputs (what the user sees)

- The directory skeleton (announced once it's created).
- For greenfield: progress per debate round (e.g. "Round 1: proposers running… Debater verdict: Plan A REVISE, Plan B READY"). On both READY, present both plans and ask the user to pick.
- For improvement: the routing question (revise existing vs. new feature), the archive notice (if applicable), and the final plan.
