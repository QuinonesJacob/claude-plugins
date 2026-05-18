# Voice Profile — <UserName>

Fill every field. If still unknown after samples and interview, write
`unknown — ask on first use`. Keep observed claims tied to a sample quote.

```
identity:
  name: <name>
  context: <role/relationship that shapes tone, or n/a>
  signature:
    external_email: <literal text incl. line breaks, or none>
    internal_email: <literal text, or none>
    slack_teams: <literal text, or none>
    imessage_sms: <literal text, or none>
    pr_comment: <literal text, or none>

punctuation:
  em_dash: BANNED. No "—", no "--" as a connector. Replace with <period |
    semicolon | comma | rhythm-dependent>. If a dash joins two clauses,
    split into two sentences.
  oxford_comma: <yes | no>
  exclamation: <rate / when allowed>
  ellipsis: <sparing | not used | situational>
  parentheticals: <frequent | rare>

sentence_rhythm:
  stance: <e.g. friendly and concise, not explanatory>
  target_mean_words: <n>
  fragments_ok: <yes | no>
  pattern: <e.g. mostly short; one longer to land a point>

formality_by_channel:
  | channel        | reg | greeting        | sign-off        | emoji |
  | external email | <1-5> | <token>       | <token>         | <y/n> |
  | internal email | <1-5> | <token>       | <token>         | <y/n> |
  | Slack/Teams    | <1-5> | <token/none>  | <token/none>    | <y/n> |
  | iMessage/SMS   | <1-5> | <token/none>  | <token/none>    | <y/n> |
  | PR/review      | <1-5> | <token/none>  | <token/none>    | <y/n> |

vocabulary:
  prefer: [<observed words/phrases the user reaches for>]
  avoid:  [<words that read as not-them>]

banned_ai_tells:
  - <phrase or reflex to never emit>
  - <...>

context_assumption:
  dial: <1-5>
  directive: <one line: who the reader is and what not to explain>

casing_emoji:
  email: <sentence case | ...>
  slack: <sentence case | lowercase ok | ...>
  pr: <sentence case | ...>
  emoji: <none | rare | situational; which ones if characteristic>

length_discipline: <e.g. shortest message that does the job; no preamble,
  no recap closer; note any channel that runs longer on purpose>

confidence:
  samples: <count>
  channels_covered: [<channels with at least one real sample>]
  interview_only: [<fields set from interview, not samples>]
  conflicts:
    - field: <name>
      observed: <what samples showed>
      chosen: <what the profile uses>
      note: <why the chosen value wins>
```
