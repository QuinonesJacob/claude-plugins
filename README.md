# Jacob Quinones — Claude Plugins

A marketplace of Claude plugins I've built. Local-first, MCP-driven, no
cloud LLM API in the source — they all use Claude Desktop, Claude Code,
or Cowork as the surface and ship the *capability* layer (MCP tools +
markdown skills) that those surfaces drive.

## Install

Add this marketplace once. Browse and install any plugin from it.

**Claude Code (terminal):**
```bash
claude plugin marketplace add QuinonesJacob/claude-plugins
claude plugin install resume-studio@quinones-claude-plugins
claude plugin install team-skills@quinones-claude-plugins
```

**Claude Desktop (GUI):** **+** next to the prompt → **Plugins** → **Add plugin** → paste `QuinonesJacob/claude-plugins`.

**Cowork:** **Customize** → **Plugins** → **Add plugin** → paste `QuinonesJacob/claude-plugins`.

After adding the marketplace, the full plugin catalog appears in your
plugin browser — install the ones you want, no separate clones or config
edits.

## Plugins

### [`resume-studio`](https://github.com/QuinonesJacob/resume-studio)
Local-first job-application workspace. Drop your resume, paste a JD,
Claude tailors a variant + writes a rationale + tracks the application
in SQLite. Render the diff inline as an HTML artifact and export to
DOCX with one prompt. Optional Electron viewer ships in the same repo.

- 15 MCP tools (resume, applications, variants)
- 4 skills: `parse-resume`, `summarize-jd`, `tailor-resume`, `review-variant`

### [`math-mcp`](https://github.com/QuinonesJacob/math-mcp)
Math + finance MCP server. Claude picks the right operation from
**named tools with rich descriptions** instead of writing Python and
flipping sign conventions. Excel-compatible TVM math is the
differentiator — `finance_pmt(rate, nper, pv)` returns the same
correctly-signed answer every time.

- 16 MCP tools across 5 categories
- Finance: PMT / FV / PV / NPV (scipy + Excel conventions) / IRR / XIRR / NPER / RATE
- Newton+bisection IRR/XIRR solver with cashflow sign-change validation; analytic-derivative RATE
- Sample-stat statistics with documented NaN edge cases, unit conversion, expression-eval fallback
- `list_functions` for browsing the ~250 mathjs functions reachable through `calculate`
- mathjs sandboxed (import/createUnit disabled)
- Pre-bundled — no install step on plugin install

### `team-skills`
Shared team skills for product workflows, bundled in this repo so the
set can grow without spinning up a repo per skill.

**`plan`** — sets up `docs/plans/<slug>-plan/plan.md` and routes to the
right flow. Greenfield plans **always** run a debate loop: a "simplest"
proposer and a "most robust" proposer in parallel, refereed by a
critique-only debater that never picks a winner (the user does).
Improvements ask whether to debate or single-shot first, and
auto-archive any superseded plan.

**`user-voice`** — one-time setup skill that makes Claude stop sounding
like AI. It learns your writing voice from a few real samples plus a
short interview, builds a structured Voice Profile (punctuation rules,
per-channel formality, banned AI-tell phrases, how much context to
assume), then installs it as a portable `my-voice` skill and/or a
sentinel-delimited `CLAUDE.md` section. Future sessions then draft
email, Slack, Teams, iMessage, and PR comments in your voice. No em
dashes, no "I hope this email finds you well," concise, channel-aware.
Re-invoke it to refine.

- 2 skills: `plan` (greenfield debate loop + improvement/revision flow),
  `user-voice` (voice capture → installs a my-voice skill / CLAUDE.md section)
- Bundled local source — installs straight from this marketplace, no extra repo

> *More coming.* This marketplace is a living portfolio — each new
> plugin gets an entry here so you only have to add this marketplace
> once.

## What's a Claude plugin marketplace?

A `.claude-plugin/marketplace.json` catalog hosted on GitHub. When you
`/plugin marketplace add` it, Claude knows about every plugin listed
inside and you can install them through the standard plugin browser
(GUI or CLI). The plugins themselves live in their own repos; the
marketplace just routes you to them.

Reference: [code.claude.com/docs/en/plugin-marketplaces](https://code.claude.com/docs/en/plugin-marketplaces.md).
