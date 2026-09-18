# Contributing to Wellfin

How anyone — graduate trio member, future teammate, or agent-assisted contributor — works effectively in this repo. The short version: pick up a ticket, work its lane, learn in the open, wrap up when you leave.

## 1. Repo map

- `AGENTS.md` — the agent entry point; every automation rule in this guide is pointed at from there.
- `docs/agents/` — workflow source of truth: `git-workflow.md` (branches, commits, pushes, identity), `issue-tracker.md` (GitHub operations), `domain.md` + `triage-labels.md` (docs and label conventions).
- `CONTEXT.md` + `docs/adr/` — the agreed fintech glossary and hard-to-reverse decisions. Read them before touching product code; use glossary terms verbatim.
- `docs/learning/` — expiring concept notes with an `INDEX.md` inbox (max 10). Staging area for knowledge, not a wiki.
- `docs/academy/` — the team's fintech school: mission, lessons, learning records (see §7).
- `.agents/skills/` — automation skills, including `$brief` (human morning brief) and `$wrap` (model-invoked lite close; `docs/agents/personal-status.md` owns STATUS contract).
- `.scratch/` — personal local-only zone. Never pushed, never reviewed.

## 2. First-day setup

1. Get access: you need collaborator status on `wellfiners/wellfin` (ask `IBruteDude`), then clone and `gh auth login`.
2. Claim your identity — every commit is authored by a human, never by an agent or model:
   `git config user.name "<AdelTamer35|mohamedelawakey|IBruteDude>"` and
   `git config user.email "<same>@users.noreply.github.com"`.
   Agents verify this before every commit proposal and stop on anything else.
3. Read `AGENTS.md`, `docs/agents/git-workflow.md`, and `CONTEXT.md` (or the map #1 when it points somewhere newer).
 4. Say `$brief` to an agent and confirm your `STATUS.md` exists under `.scratch/personal/<you>/` (shape/contract in `docs/agents/personal-status.md`).

## 3. The daily driver — $brief and $wrap

- **Morning:** open one session, type `$brief` (once per morning; later sessions resume from STATUS section + linked handoff/issue instead of re-running).
- **Work:** open one session per stream you will actively touch; open with its name ("continuing Building #6") so the agent scopes itself. Other streams keep yesterday's stamp.
- **Leaving:** `$wrap` fires autonomously at session pause when intent-to-change was shown (edit on scoped/personal branch, issue/PR write, or stated work on #NN); you can also type `$wrap`/`$wrap up` by hand. Inspect-only sessions are a no-op. It updates only your streams' sections surgically (fresh re-read, others byte-identical) and writes one handoff per broken-off stream under `.scratch/personal/<you>/handoffs/` with markdown links.
- The 10-second test: would tomorrow-you need anything from this session? Yes wraps, no doesn't. A wrong skip costs one stale flag; a wrong wrap costs thirty seconds.

## 4. Finding work

- The [foundation map](https://github.com/wellfiners/wellfin/issues/1) is the shared plan: destination, open decision tickets, fog (visible but unscheduled ideas), and out-of-scope boundaries.
- Take the frontier: open, unblocked, unclaimed child tickets. Claim by assigning yourself **first**, before any work, so parallel sessions skip it.
- Blocked tickets wait — every blocker listed must be closed first (native GitHub dependencies, `Blocked by` lines as fallback).
- Learning work lives in mission issues (e.g. academy mission 1). Same claim rule.

## 5. Branches and issues

Three lanes, exactly one at a time: **primary** (`main`, protected), **scoped** (`NN-<slug>` per issue, short-lived, deleted after merge), **personal** (`personal/<you>/<topic>`, local-only — the sole exception to issue numbering).

- Create a scoped branch only with explicit intent (ticket claimed/assigned first). Strict format: `NN-<kebab-slug>`, e.g. `12-add-daily-skill`. Scope comes from the issue number, ownership from the assignee.
- Labels are a closed set: `wayfinder:map`, `wayfinder:research|prototype|grilling|task`, `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix`. Propose anything new in an issue body and wait for approval.

## 6. Commits and pull requests

- Messages are conventional commits — `type(scope): imperative lowercase subject` (`feat, fix, docs, style, refactor, perf, test, build, ci, chore, revert`), body for what/why, footer `Part of #NN`. One ticket per commit.
- Every commit: the agent stages only the ticket's scope, shows diff plus message, and **waits for your explicit Yes** — on any lane, including `main`, where direct commits additionally need separate primary confirmation.
- Authorship is human; agent-wholesale content approved unmodified adds `Assisted-by: OpenCode (Muse Spark)`. Real `Co-authored-by` waits for a team bot account — never invent identities.
- Never commit secrets, `personal/*`, or unrelated scopes together; never `--no-verify`, never amend someone else's commit.
- Commit approval on a pre-authorized scoped branch carries push approval — push it so review can happen. `personal/*` is never pushed; `main` is never direct-pushed. Land via squash-merge PR from the scoped branch and delete the branch after merge (see `git-workflow.md` § Landing PRs for message shape and stacking).

## 7. Learning your way up

- The academy (`docs/academy/`) builds human fintech skill: one team mission, bite-size lessons with practice, per-member learning records (`NNNN-slug-name.md`, one insight each — one topic holds many records, corroborate instead of duplicating).
- Raw jottings stay in `personal/` until shaped; academy entries must tie to the mission and cite `RESOURCES.md`.
- Records graduate through the inbox: indexed → reviewed → `CONTEXT.md` term or ADR → marked `promoted`, kept forever. Lessons never enter agent context.
- The inbox holds at most 10: at full capacity, adding one forces a pick-one triage — you choose which of the 10 gets promoted, merged, or expired first. Nothing is ever silently disposed.

## 8. Language and libraries

- Talk to agents in any language; **everything recorded in the repo is English** — code, commits, issues, ADRs, glossary, records. Sole exception: human-facing lesson/reference HTML under `docs/academy/` may carry Arabic.
- Prefer mature libraries over building from scratch. When one satisfies the need, adopt it and record the choice; hand-rolling needs a written justification.

## 9. Review checklist (every PR, every session)

- English records? Glossary terms exact, no synonym drift? ADR added/linked for hard-to-reverse choices?
- Inbox ≤ 10, none past expiry? Ledger append-only preserved (no UPDATE/DELETE; corrections reverse; balances derived)?
- V1 scope kept (Egypt/EGP/English/manual-entry)? Adopt-over-build respected, contracts versioned?
- Session loaded only its grounding set — no notebook/model/history dumps?

## 10. The deal with agents

The setup runs the rails itself — gates, caps, hooks, grounding, promotion drafts. You drive the judgments: mission scope, triage picks, promotion approvals, lesson-worthiness, and every commit Yes. When lost, `$brief` knows where you left off (from `$wrap`'s last stopping point).
