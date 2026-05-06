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

## Why a marketplace and not just one repo?

Each plugin has its own scope, README, issues, license. Mashing them
into one repo would obscure what each one does and break the install
flow. A marketplace catalogs them; users add one URL and see
everything.
