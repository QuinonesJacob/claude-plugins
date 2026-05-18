# Interview — gap-filling questions and the conflict rule

You reach this doc after analyzing samples. The point of the interview is to
fill what the samples could not show, not to re-ask what they already proved.
Asking a user to confirm something you can already see in their own writing
wastes their time and makes the skill feel dumb.

## Rule: skip what the samples already answered

Before asking anything, run the samples against each field. Skip the question
when the samples are unambiguous:

- **Signature/greeting** — skip if every same-channel sample opens and closes
  the same way. Confirm only if samples disagree or a channel has no sample.
- **Punctuation (non-em-dash)** — skip if contraction rate, exclamation use,
  and comma style are consistent across samples.
- **Casing/emoji** — skip if consistent within a channel.
- **Sentence rhythm** — skip if samples are consistent; you measured it.

Always ask, regardless of samples, the **aspirational** items, because samples
show how the user *did* write, not how they *want* Claude to write:

- the em-dash ban and the replacement preference
- the banned-AI-tell list (confirm/trim/extend)
- context-assumption dial
- length stance

## Question bank

Prefer one `AskUserQuestion` round with multi-choice. Use free text only where
the answer is a literal string (signature) or an open list (extra bans).

1. **Channels** (multi-select): "Which channels should Claude write for you?"
   Options: external email, internal email, Slack/Teams, iMessage/SMS, PR or
   code-review comments. Only profile the channels they pick.

2. **Em-dash replacement**: "When a thought would normally take an em dash,
   what should Claude use instead?" Options: a period (two sentences) /
   a semicolon / a comma / depends, decide by rhythm. (The ban itself is not
   optional here; only the replacement default is being chosen.)

3. **Register per chosen channel**: for each channel they selected, a 1–5
   register pick with concrete anchors (1 "like texting a friend", 3 "direct
   and professional, no fluff", 5 "external, formal, careful").

4. **Signature** (free text, per channel that needs one): "Type your sign-off
   exactly as you'd write it, including line breaks. Enter 'none' if you don't
   sign off on that channel."

5. **Banned AI-tells**: show the seed list from
   `voice-profile-schema.md` field 7. "Anything here that's actually fine for
   you to say?" (remove) and "Anything that bugs you that's not listed?"
   (free-text add). This is the field most worth getting exactly right; it is
   the user's explicit complaint.

6. **Context assumption** — ask behaviorally, not abstractly, because people
   answer "do you over-explain?" inaccurately about themselves. Scenario:
   "A teammate asks for the deploy status. Do you (a) just answer, they know
   what the deploy is, (b) answer and add a one-line refresher, (c) explain
   the deploy and then answer?" Map a→5, b→3, c→1.

7. **Length stance**: "Default message length?" Options: shortest that does
   the job / brief but a touch warm / fuller, you'd rather over-communicate.

## The conflict rule (record both, note why)

When the interview answer contradicts the samples, do not silently pick one.
Decide by field type:

- **Observable fields** (rhythm, contractions, casing, actual greeting tokens)
  → the samples win. People describe their own style inaccurately; the writing
  is ground truth for how they *do* write.
- **Aspirational fields** (em-dash ban, AI-tell bans, context dial, length)
  → the stated preference wins, even if every sample violates it. The user is
  explicitly asking Claude to write better than their rushed past emails.

Either way, when observed and stated disagree, write both into the profile and
add a one-line `note:` on that field, e.g.
`note: samples averaged 2 em dashes/email; user chose to ban — ban wins`.
That note is for the next refine run: it explains why a rule looks like it
contradicts the user's own corpus, so a future session does not "fix" it back.
