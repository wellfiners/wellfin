---
name: daily
description: Morning brief and evening wrap-up for your workstreams. Type $daily to start the day, $daily wrap up to close it.
disable-model-invocation: true
argument-hint: "wrap up for the evening close; leave empty for the morning brief"
---

Work in exactly one of two moves per invocation: **brief** (morning) or **wrap** (evening). Bare `$daily` runs brief; `$daily wrap up` (or evening language) runs wrap. When the move is genuinely unclear, ask which in one line and wait.

## Brief: what is up for work

Assemble the canonical picture from three sources, then report it grouped by stream with one suggested first move. Run brief once per morning in the first session; sessions opened afterwards for individual streams resume from their STATUS section plus linked handoff/issue instead of re-running the full brief.

1. Fetch shared state: `git fetch`, then list assigned open issues (`gh issue list --assignee @me --state open`), open PRs by `@me` and review requests, and own recent branches (`git branch --sort=-committerdate`, top 5). Note the map frontier only when the human asks for team scope; the brief is personal first.
2. Read the personal log: `.scratch/personal/<user>/STATUS.md`, where `<user>` is `git config user.name`. When it is missing, say so in one line and continue on shared state alone; never invent personal streams.
3. Read linked handoffs: open only the handoff files `STATUS.md` points at for still-open streams, newest first. Do not trawl the whole `handoffs/` directory.
4. Report grouped by stream (building, learning, researching, prototyping, experimenting, fixing — only streams with a trace), each as state plus next action, then one suggested first move for the day. Streams with no trace anywhere are omitted, not guessed at.

Brief completion: every reported stream carries a state and a next action grounded in something read this session.

## Wrap: close the session

Run `$daily wrap up` in each session as you leave it. Capture this session's stopping state for the next brief to read. Work the open streams from this conversation plus current GitHub state.

1. Name each open stream and its stopping point: what is done, what is next, what is blocked and on what. Scope is the streams named at this session's start (open with "continuing <stream>" where possible); when none was named, infer from the conversation and name them in the report so the next brief can see the scope.
2. Write one handoff per broken-off stream following `.agents/skills/handoff/SKILL.md` (user-invoked skills cannot invoke each other, so follow its format by reference), pinned to `.scratch/personal/<user>/handoffs/` (stable and linkable; overrides the temp-dir default). Reference shared artifacts by path or URL; never paste what issues, PRs, or commits already hold.
3. Update `.scratch/personal/<user>/STATUS.md` surgically: re-read it fresh first (never from an earlier read in this session), then rewrite only the sections for streams touched here — state, next action, links, plus an `Updated: <YYYY-MM-DD HH:MM>` stamp each. Leave every other section byte-identical so parallel sessions never clobber each other; retire sections for streams that closed. English, no secrets, no tokens.
4. Report what was recorded and where; name any stream left intentionally unrecorded.

Wrap completion: `STATUS.md` rewritten, every broken-off stream has a linked handoff, nothing recorded outside `personal/`.

## STATUS.md shape

```markdown
# STATUS — <user>

## Building — #<n> <slug>: <state>. Next: <action>.
Updated: <YYYY-MM-DD HH:MM>
## Learning — mission <n>: <state>. Next: <action>.
Updated: <YYYY-MM-DD HH:MM>
```

One section per live stream. Each section carries its own stamp; the brief trusts the latest stamp per section and flags sections whose stamp predates visible activity (branch commits, issue comments) as possibly stale. Wrap runs per session at close — there is no single global wrap.

One section per live stream, retired streams deleted at wrap. The file is local-only (`.scratch/` is gitignored); it is the personal third of the canonical picture in `git-workflow.md`'s personal lane.

## Reach, don't restate

Branching, identity, commit, and push rules live in `docs/agents/git-workflow.md`; issue operations in `docs/agents/issue-tracker.md`; the handoff format in the `handoff` skill. This skill adds only the two moves above.
