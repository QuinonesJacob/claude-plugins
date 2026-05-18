# Emit: generate the portable `my-voice` skill

You reach this doc when the user chose the standalone skill output (or both).
This produces the skill that actually fires on day-to-day drafting. Its job is
the opposite of `user-voice`: where `user-voice` is setup-only and must not
trigger per message, `my-voice` must trigger reliably whenever Claude is about
to send something as the user.

## Target path

`~/.claude/skills/my-voice/SKILL.md` (user-level, so it is available in every
project, matching the skill's scope). On Windows:
`C:\Users\<user>\.claude\skills\my-voice\SKILL.md`. On refine, overwrite this
file in place.

## Generated frontmatter

Exactly three fields, same house style as the sibling skills. The description
is deliberately pushy and drafting-oriented — under-triggering here means the
voice silently does not get applied, which is the failure the user is trying
to fix. Substitute `<UserName>` from the profile.

```
---
name: my-voice
description: "Write this in <UserName>'s voice. Use BEFORE composing or sending ANY message on the user's behalf — email, reply, Slack, Teams, Outlook, iMessage, SMS, PR or code-review comment, follow-up. Triggers on 'draft', 'reply', 'send', 'write an email', 'message <person>', 'respond to', 'follow up with', or any task that produces text another human will read as the user. Applies the user's Voice Profile: no em dashes, no AI scaffolding phrases, concise, assumes shared context, channel-appropriate register. Skip only for code, docs, commits, or analysis shown to the user — this is for human-to-human messages."
version: 0.1.0
---
```

## Generated body

The body must be self-contained: it embeds the whole Voice Profile inline so
the skill works even if `user-voice` and `team-skills` are not installed. Do
not reference `user-voice` or any external file from the generated skill.

Structure:

```
# my-voice — write as <UserName>

Before you send this as <UserName>, apply the profile below. The profile is
the contract. Do not paraphrase it away, and do not let a polite default
override it; the user installed this precisely because the default voice was
wrong for them.

## Voice Profile

<the accepted, filled Voice Profile, pasted verbatim from the
voice-profile.template.md instance — the full structured block, not a summary>

## Pick the channel first

Infer the channel from the request or the tool in use (an email tool → email
register; a Slack/Teams send → that row; a PR comment → PR row). If you
cannot tell, ask one short question before drafting. Use the matching row of
the formality table; never default to the most formal or most casual.

## Pre-send checklist

Before returning the draft, scan it and fix any hit:

- any "—" or "--" used as a connector → rewrite per the punctuation rule
- any phrase from banned_ai_tells → cut or replace
- a sentence that restates the request before answering → delete it
- an explanation of something the recipient already knows → delete it
- a closing paragraph that recaps what was just said → delete it
- wrong register for the channel, or missing/incorrect signature → fix
- reflexive three-item list where one sentence works → collapse it

Return only the message, in the user's voice. Do not add a note explaining
what you changed unless asked.
```

## Verify

- The file parses as valid YAML frontmatter and the `name` is `my-voice`,
  matching the directory.
- The body contains the full profile, not a summary, and contains no
  reference to `user-voice`, `team-skills`, or any external path.
- Print the path back to the user.
