# Greenfield planning — debate loop (always runs)

This is the procedure for greenfield plans. The debate loop is **mandatory** — there is no opt-out. The Debater never picks a winner; only the user does.

## Inputs you have at this point

- `<slug>` — the kebab-case slug for the plan (e.g. `csv-export`).
- `<slug>-plan/` — the empty directory at `docs/plans/<slug>-plan/` already created by SKILL.md.
- The user's plan topic / goal in natural language.

## Per-round procedure

Run up to **3 rounds**. Each round is: two proposers in parallel, then one debater.

### Step 1 — Launch Proposer A and Proposer B in parallel

In a single message, make TWO `Agent` tool calls with `subagent_type: general-purpose`, `model: sonnet`. They must run in parallel.

**Proposer A — "simplest" prompt:**

> You are Proposer A. Design the **simplest** plan that ships the goal stated below. Bias toward fewer files, fewer abstractions, fewer dependencies, less indirection. If a one-file solution will do, propose a one-file solution.
>
> **Goal:** <user's plan topic, verbatim>
>
> Output a markdown plan with these sections, in order:
> - **Context** — why this is being built, what success looks like.
> - **Approach** — the smallest design that ships the goal. Justify simplicity tradeoffs.
> - **Files to create/modify** — exact paths, one bullet each, with a one-line "why".
> - **Verification** — how the user will manually confirm it works end-to-end.
>
> Do NOT include alternatives. One plan only. Do NOT write to disk; return the markdown as your final message.
>
> <on rounds N>1, append:>
> **Round <N> debater feedback on your previous plan:** <verbatim Debater output for Plan A>
> Address every REVISE point. You may keep elements that were already READY.

**Proposer B — "robust" prompt:**

> You are Proposer B. Design the **most robust, long-lived** plan for the goal stated below. Bias toward extensibility, clear module boundaries, future-proofing, and observability.
>
> **Goal:** <user's plan topic, verbatim>
>
> Output the same four sections (Context, Approach, Files to create/modify, Verification). Justify the robustness tradeoffs in Approach. One plan only. Do NOT write to disk; return the markdown as your final message.
>
> <on rounds N>1, append:>
> **Round <N> debater feedback on your previous plan:** <verbatim Debater output for Plan B>
> Address every REVISE point.

### Step 2 — Launch the Debater

Single `Agent` call (`general-purpose`, sonnet). The Debater receives both plans verbatim. **Do not pre-summarize them.**

**Debater prompt:**

> You are the Debater. Critique each of the two plans below independently. Your job is to flag gaps, hidden complexity, foot-guns, missing verification, unstated assumptions, and risk.
>
> **You MUST NOT recommend a winner.** Do not say "Plan A is better" or "I'd go with Plan B". The orchestrator picks; you only critique.
>
> For each plan, end with a verdict line in EXACTLY this format:
> - `Plan A verdict: READY` — or — `Plan A verdict: REVISE — <specific asks separated by semicolons>`
> - `Plan B verdict: READY` — or — `Plan B verdict: REVISE — <specific asks separated by semicolons>`
>
> Use READY only if the plan is genuinely ship-ready. Use REVISE if there is any concrete improvement worth making.
>
> **Plan A:**
> <verbatim Plan A markdown>
>
> **Plan B:**
> <verbatim Plan B markdown>

### Step 3 — Decide based on verdicts

Parse the two verdict lines from the Debater's output.

- **Both READY** → go to "Step 4 — Present to user".
- **Either REVISE** → start the next round (back to Step 1) with the Debater's feedback fed into the proposer prompts. Hard cap: 3 rounds total.
- **After Round 3, regardless of verdicts** → go to Step 4 with whatever you have. Note in the announcement that the cap was hit.

### Step 4 — Present both plans to the user

1. Write both plans to disk:
   - `docs/plans/<slug>-plan/plan-a.md` (Proposer A's final plan)
   - `docs/plans/<slug>-plan/plan-b.md` (Proposer B's final plan)
2. Use `AskUserQuestion` to ask the user to pick. Use option labels "Plan A (simplest)" and "Plan B (most robust)". Include the Debater's last verdict line for each plan in the option description.
3. After the user picks:
   - Rename the chosen file to `plan.md` (use `Bash mv`).
   - Delete the other file (use `Bash rm`).
4. Tell the user: "Plan written to `docs/plans/<slug>-plan/plan.md`. <N> debate rounds."

## Bias-control invariants

- The Debater MUST NOT pick a winner. If a Debater output ever contains "I recommend", "I'd pick", "Plan X is better", or similar, the orchestrator should treat it as a prompt-adherence failure and re-run the Debater with a stricter reminder. Never act on a recommendation embedded in critique.
- Proposers must not see each other's plans. Each proposer only sees the goal and their own previous round's feedback.
- The orchestrator must not pre-summarize plans or feedback when passing them between agents — pass verbatim.
