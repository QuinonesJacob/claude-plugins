# Voice Profile — schema, extraction checklist, worked example

## Why a structured profile and not a prose description

A paragraph like "the user writes warmly but concisely" gets re-interpreted
differently every session and enforces nothing. A structured profile with
explicit do/don't lists and a banned-phrase array is checkable: the generated
artifact can literally scan a draft for a banned phrase or an em dash before
it sends. Structure also makes refine work — you can diff last month's profile
against this month's instead of re-reading prose and guessing what changed.

So the profile is data, not vibes. Every field is something a future session
can act on without having to "feel" the voice.

## The schema

Fill every field. If a field is unknown after samples and interview, write
`unknown — ask on first use` rather than guessing.

1. **identity** — the user's name; role/context if it shapes tone; the literal
   signature string per channel (what characters actually get typed, e.g.
   `Thanks,\nJacob` for email, nothing on Slack).

2. **punctuation** — explicit, enforceable rules. Always includes an em-dash
   rule. Default: *no em dashes (—) and no double hyphen as a connector;
   replace with a period, semicolon, or comma; if two clauses are joined by a
   dash, split into two sentences or use a semicolon.* Also: Oxford comma
   (yes/no, from evidence), exclamation marks (rate), ellipses (sparing or
   not), parenthetical asides (frequent or rare).

3. **sentence_rhythm** — target mean sentence length (words), whether
   sentence fragments are allowed, the variance pattern ("mostly short, one
   longer to land a point"), and the observed range from samples.

4. **formality_by_channel** — a table, one row per channel. Columns: channel,
   register 1–5 (1 = texting-casual, 3 = direct-professional, 5 =
   external-formal), greeting style, sign-off style, emoji allowed (y/n).
   Rows to cover: external email, internal email, Slack/Teams, iMessage/SMS,
   PR or code-review comment.

5. **greetings_signoffs** — the exact opening and closing tokens per channel
   (not a description — the literal text), including "no greeting" / "no
   sign-off" where that is the truth.

6. **vocabulary** — `prefer`: words/phrases the user actually reaches for.
   `avoid`: words the user does not use and that read as not-them. Keep these
   to real, observed items, not a thesaurus.

7. **banned_ai_tells** — the enforceable heart of the skill: an array of
   phrases and reflexes to never emit. Seed list (trim or extend in the
   interview):
   - "I hope this email finds you well"
   - "I wanted to reach out"
   - "I just wanted to" (as a softener)
   - "Please don't hesitate to"
   - "Certainly!" / "Absolutely!" / "Great question!"
   - "I'd be happy to" / "Happy to help!"
   - "As an AI" / "I'm an AI"
   - reflexive rule-of-three lists when a sentence would do
   - restating the question before answering it
   - summarizing what was just said in a closing recap
   - over-signposting ("Firstly, … Secondly, … In conclusion, …")
   - over-hedging stacks ("it might be worth potentially considering maybe")
   - empty enthusiasm openers ("Exciting news!" with nothing exciting)

8. **context_assumption** — a 1–5 dial. 1 = define every term and give
   background; 5 = assume the reader shares the context and omit the obvious.
   Plus a one-line directive that operationalizes it, e.g. "Recipient is a
   colleague on this project; do not explain what the deploy/PR/ticket is."

9. **casing_emoji** — sentence case vs. lowercase-casual per channel; emoji
   frequency (none / rare / situational) and which ones if any are
   characteristic.

10. **length_discipline** — the default length stance: usually "the shortest
    message that does the job; no preamble, no recap closer." Note any channel
    where the user runs longer on purpose.

11. **confidence** — sample count; channels actually covered by samples;
    list of fields that are interview-only or still `unknown`. This is honesty
    about overfit, and it tells refine where to focus next time.

## Sample-analysis feature checklist (step 3)

For each sample, and then aggregated per channel, extract:

- em-dash count and how it was used (connector vs. true aside)
- other punctuation rates: semicolons, exclamation marks, ellipses, parentheses
- sentence count and length: mean and rough spread; fragment usage
- opening token (literal first words) and closing/sign-off token (literal)
- contraction rate (don't/I'll vs. do not/I will)
- hedge density (maybe, I think, perhaps, sort of, just, a bit)
- AI-tell phrases present in the user's own writing (note them — they become
  conflicts the stated preference will override)
- list usage: bulleted/numbered vs. running prose
- paragraph length and whitespace habits
- emoji and casing

Every extracted claim gets a cited quote. No quote, no claim.

## Worked example — Jacob's Voice Profile (seeded from stated preferences)

Use this as the shape to produce, and as the safe default when samples are
thin. It is built from the user's stated pain points; sample-derived fields
are marked pending.

```
identity:
  name: Jacob
  signature:
    email: "Thanks,\nJacob"
    slack: (none)
    pr_comment: (none)

punctuation:
  em_dash: BANNED. No "—", no "--" as a connector. Replace with a period or
    semicolon. If a dash joins two clauses, split into two sentences.
  oxford_comma: unknown — infer from samples
  exclamation: sparing; not as default enthusiasm
  ellipsis: rare

sentence_rhythm:
  stance: friendly and concise, not explanatory
  target_mean_words: ~12-16
  fragments_ok: yes, when natural
  pattern: mostly short; cut anything that restates the prompt

formality_by_channel:
  | channel        | reg | greeting        | sign-off    | emoji |
  | external email | 4   | "Hi <name>,"    | "Thanks,\nJacob" | no |
  | internal email | 3   | "Hi <name>,"    | "Thanks, J" | no  |
  | Slack/Teams    | 2   | (none)          | (none)      | rare |
  | iMessage/SMS   | 1   | (none)          | (none)      | situational |
  | PR/review      | 3   | (none)          | (none)      | no  |

greetings_signoffs: see table; never "Dear", never "Best regards,"

vocabulary:
  prefer: unknown — infer from samples
  avoid: corporate filler ("circle back", "synergy", "touch base")

banned_ai_tells:
  - "I hope this email finds you well"
  - "I wanted to reach out" / "I just wanted to"
  - "Please don't hesitate to"
  - "Certainly!" / "Great question!" / "Happy to help!"
  - restating the question before answering
  - closing recap that summarizes what was just said
  - rule-of-three lists used reflexively
  - over-signposting (Firstly/Secondly/In conclusion)

context_assumption:
  dial: 4
  directive: Reader is a colleague who shares the context. Do not explain
    what a deploy/PR/ticket/project is. No unsolicited background paragraph.

casing_emoji:
  email: sentence case
  slack: sentence case ok; lowercase acceptable; emoji rare
  pr: sentence case, no emoji

length_discipline: shortest message that does the job; no preamble, no recap

confidence:
  samples: 0 (seed profile from stated preferences)
  channels_covered: none yet
  interview_only: punctuation.em_dash, banned_ai_tells, context_assumption,
    length_discipline
  note: tighten on first real sample set — re-invoke user-voice to refine
```
