---
name: user-voice
description: "Set up or refine how Claude writes messages in YOUR voice. Activates on 'user-voice', '/user-voice', 'learn my writing voice', 'make Claude sound like me', 'stop sounding like AI', 'my email/Slack voice', 'capture my tone', 'train my voice', or any request to make drafted messages (email, Slack, Teams, iMessage, PR comments) read as the user wrote them. One-time setup skill: analyzes writing samples plus a short interview, builds a structured Voice Profile, then writes it into a standalone my-voice skill and/or CLAUDE.md so FUTURE sessions draft in that voice. Re-invoke to refine. Not invoked per-message."
version: 0.1.0
---

# user-voice — teach Claude your writing voice

This skill runs once to *install* voice emulation. It learns how you actually
write, captures that as a structured **Voice Profile**, and writes the profile
into whatever artifact will make future sessions draft like you: a portable
`my-voice` skill, a `CLAUDE.md` section, or both.

This skill is not the thing that drafts your emails. It is the thing that
teaches the thing that drafts your emails. The reason that split matters: a
setup skill that also fired on every "reply to this" would be noisy and
slow. So `user-voice` runs at setup time only, and the artifact it emits is
what carries the voice into day-to-day drafting.

The job to get right is "stop sounding like AI." That is not one rule, it is a
cluster: em dashes used as connectors, explaining things the reader already
knows, scaffolding phrases ("I hope this email finds you well"), reflexive
rule-of-three lists, restating the question before answering it. The Voice
Profile names these explicitly so the generated artifact can enforce them.

## The seven things this skill does, in order

1. **Detect intent: first-time setup vs. refine.** Check, in this order, for
   an existing profile:
   - `~/.claude/skills/my-voice/SKILL.md`
   - a `user-voice` sentinel block in `~/.claude/CLAUDE.md`
   - a `user-voice` sentinel block in `./CLAUDE.md`

   Found one → propose **refine** (seed everything below from it; never start a
   refine from zero). None → propose **first-time setup**. Confirm with
   `AskUserQuestion`, auto-detected mode as the recommended default, the other
   mode as the override. The user can flip it.

2. **Gather writing samples.** Ask the user to paste 3–8 messages *they wrote*
   (not received), or point you to files/threads. Ask for a spread of channels
   — a formal external email, an internal Slack/Teams message, a PR or review
   comment — because voice is a range, not one register. Tell the user plainly:
   a spread of channels teaches more than ten emails of the same kind. If they
   have fewer than 3 samples, continue, but you will mark confidence low in the
   profile and lean harder on the interview.

3. **Analyze samples into observable features.** Read
   `references/voice-profile-schema.md` — it has the feature-extraction
   checklist. Pull only things you can point at: punctuation counts (em dashes
   especially), sentence length and variance, greeting and sign-off tokens,
   contraction rate, hedge density, emoji and casing, list habits — per
   channel. Attach a short quote from a sample to every claim. Do not record
   impressions you cannot evidence; "feels warm" is not a feature, "opens with
   first name, no 'Hi'" is.

4. **Interview to fill gaps.** Read `references/interview.md`. Ask only what
   the samples did not already answer — if every sample signs "Thanks, J" you
   do not ask about sign-offs. Keep it to one `AskUserQuestion` round of
   focused choices where you can; reserve free text for the literal signature
   and the hard bans.

5. **Synthesize the Voice Profile.** Copy `assets/voice-profile.template.md`
   and fill it from sample evidence plus interview answers. Resolution rule:
   for *observable* fields the samples win; for *aspirational* fields (the
   em-dash ban, the AI-tell bans, how much context to assume, length
   discipline) the stated preference wins even when the user's own past
   writing breaks it — that is the whole point, the user wants to write
   differently than the samples show. When observed and stated conflict,
   record both and a one-line note so a later refine can see why the rule
   overrode the data.

6. **Confirm with the user.** Show the filled profile verbatim. Use
   `AskUserQuestion`: accept / edit a field / re-run the interview. Write
   nothing to disk until the user accepts. This is the one place to be strict:
   you are about to install rules that shape everything Claude sends as this
   person, so they see the rules first.

7. **Ask output mode, then route.** `AskUserQuestion` with three options:
   - **Both (recommended)** — portable skill *and* CLAUDE.md section.
   - **Standalone `my-voice` skill** — portable across projects, triggers on
     drafting tasks.
   - **CLAUDE.md voice section** — always-on wherever that CLAUDE.md loads.

   Then route, reading the reference doc(s) and following them:
   - skill or both → `references/emit-standalone-skill.md`
   - CLAUDE.md or both → `references/emit-claude-md.md`

   The CLAUDE.md emitter handles the "user-level vs. project file" choice and
   the "no CLAUDE.md exists yet" case itself. Do not ask those here.

## Hard rules

- **Nothing is written before the user accepts the profile (step 6).** No
  exceptions; the user must see what gets installed.
- **Stated preference beats observed behavior, and the conflict is shown.**
  "Make it not sound like AI" outranks "but your old emails had em dashes."
  Record both values so it is auditable, not silently overridden.
- **CLAUDE.md edits are surgical.** Only ever touch the span between the
  `user-voice` sentinels. Never reformat or rewrite the user's existing
  CLAUDE.md content.
- **One voice, formality dial per channel.** Never collapse the profile to a
  single register; Slack-casual must not leak into an external formal email.
- **This skill installs; it does not draft.** The only messages it writes
  during a run are the optional self-check samples below.

## Outputs (what the user sees)

- The detected mode (setup vs. refine), announced once.
- The filled Voice Profile, shown verbatim for acceptance.
- The output-mode choice, then the path(s) of what was written.
- A closing **self-check** (offer, do not force): draft two short messages
  with the new profile so the user hears the voice before trusting it — one
  Slack-casual ("tell a teammate the build is red") and one formal email
  ("decline a meeting invite politely"). This is a sanity listen, not a score.
- A one-line reminder of how to refine: re-run `user-voice`; refine mode will
  pick up the installed profile and tighten it from there.
