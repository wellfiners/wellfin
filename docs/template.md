# Project template (bootstrap #21)

Canonical template source lives outside this repo (this repo is the lived reference instance, not the template source):

- Template repo: https://github.com/IBruteDude/agentic-project-template (public)
- Layout in template repo: prose `docs/template/*.tmpl`, runnables `scripts/template/*`, plus `bootstrap.mts`, `TEMPLATE-OWNERSHIP.md`, `TEMPLATE-CHANGELOG.md` at root. Root stays clean.
- Updater `sync-template.mts` lands in #22.

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
