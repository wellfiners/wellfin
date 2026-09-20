# Project template (bootstrap #21, updater #22)

Canonical template source lives outside this repo (this repo is the lived reference instance, not the template source):

- Template repo: https://github.com/IBruteDude/agentic-project-template (public)
- Layout in template repo: prose `docs/template/*.tmpl`, runnables `scripts/template/*`, plus `bootstrap.mts`, `sync-template.mts`, `TEMPLATE-OWNERSHIP.md`, `TEMPLATE-CHANGELOG.md` at root. Root stays clean.

## Tokens (bootstrap inputs)

`PROJECT_NAME` `PROJECT_SLUG` `GITHUB_ORG` `MEMBER_LIST` `MEMBER_PIPE` `DOMAIN_ONELINER` `DOMAIN_SEED_TERMS` `STACK_DESC` `MODEL_ID` `CREDENTIAL_VAR` `LEARNING_DEPTH` `V1_SCOPE`. Team size goes to the personalization log only. Stack and model credential are free-text with explicit `VERIFY-TODO`s where unknown.

Model (per #21 comment decision): default `opencode/muse-spark-1.3-contributor-free`. `.sandcastle/main.mts` template keeps that default but reads `OPENCODE_MODEL` from env. `.env.example` template is provider-neutral: `GH_TOKEN` (required) + `OPENCODE_MODEL` (required) + credential-var slot; Claude OAuth / Anthropic key remain only as commented examples. Contributing setup template reworded to match.

Credential-var answer (best-effort, also in template changelog 0.2.0): outside this harness, Muse Spark via opencode uses OpenCode Zen — `opencode providers login` (stored in `~/.local/share/opencode/auth.json`) or `OPENCODE_API_KEY` for headless/CI. Wizard defaults `CREDENTIAL_VAR` to `OPENCODE_API_KEY` with a VERIFY-TODO.

## Hard-excludes

`.env`, `.scratch/` content, `node_modules/`, `.sandcastle/logs|worktrees/`, secrets, product code never ship. Arabic exception intact: human-facing guide/reference HTML under `docs/knowledge/` may carry Arabic; everything else English.

## Ownership (summary; canonical in template repo `TEMPLATE-OWNERSHIP.md`)

Template-owned: `AGENTS.md`, `CONTRIBUTING.md` structure, `docs/agents/`, `.sandcastle/` runner, `package.json`, `skills-lock.json` + skills wiring, inbox `INDEX` structure, `docs/adr/` seed, `scripts/token-audit.mts`, updater. Project-owned (never overwritten): `CONTEXT.md` terms, numbered ADRs, `docs/knowledge/**`, `PERSONALIZATION.log.md`, product code, local-only paths.

## Fixture bootstrap proof (#21 acceptance)

- Fixture: template repo `scripts/template/fixtures/sample-inputs.json` (Acme Ledger / acme-ledger / acme-org, members ada/bilal/camelia, guided, Node 22 + TypeScript + Docker, Muse Spark default, `OPENCODE_API_KEY`).
- Method: scratch clone of template repo, `npx tsx bootstrap.mts --non-interactive --input ...`, then `npx tsx scripts/token-audit.mts`.
- Result 2026-09-20: rendered 23 files; bootstrap audit 0 token hits, 0 residue hits, 0 schooling hits; `PERSONALIZATION.log.md` present; `bootstrap.mts` + `docs/template/` + `scripts/template/` sources removed (fire-once verified); standalone `scripts/token-audit.mts` PASS (0 tokens, 0 residue, wording clean outside vendored skills).
- Changelog: template `TEMPLATE-CHANGELOG.md` 0.1.0 (skeleton) + 0.2.0 (provider-neutral model auth + proof + credential answer above).

## Fixture updater round-trip (#22 acceptance)

- Updater: template repo `sync-template.mts` (`--template <repo> --downstream <project> --input <json> [--yes]`). Template-owned renders from `.tmpl` with the same inputs; root meta syncs by copy; `README.md` + `CONTEXT.md` project-owned after bootstrap and never touched; log append-only. Outcomes: `update` / `create` / `skip` / `conflict` (incl. `LOCAL-EDIT` marker even with `--yes`) / `protected`. Changelog printed first for judgment.
- Method: bootstrap Acme sample from pre-bump, diverge (`CONTRIBUTING.md` LOCAL-EDIT, `CONTEXT.md` glossary term, new inbox note, updater removed), bump rail-fix line in `git-workflow` template + changelog 0.3.0, sync with `--yes --non-interactive`.
- Result 2026-09-20: `update=2` (`docs/agents/git-workflow.md` with rail fix, `TEMPLATE-CHANGELOG.md` with 0.3.0), `create=1` (`sync-template.mts`), `skip=20` (incl. `AGENTS.md`), `conflict=1` (`CONTRIBUTING.md` LOCAL-EDIT preserved, not overwritten), `protected=3` (`CONTEXT.md` glossary term, `README.md`, log untouched; inbox note preserved). Post-sync `scripts/token-audit.mts` PASS.
- Changelog: template `TEMPLATE-CHANGELOG.md` 0.3.0 (updater + round-trip above).
