# Project template (bootstrap #21, updater #22, adopt #26)

Canonical template source lives outside this repo (this repo is the lived reference instance, not the template source):

- Template repo: https://github.com/IBruteDude/agentic-project-template (public)
- Layout in template repo: prose `docs/template/*.tmpl`, runnables `scripts/template/*`, plus `bootstrap.mts`, `sync-template.mts`, `adopt.mts`, `TEMPLATE-OWNERSHIP.md`, `TEMPLATE-CHANGELOG.md` at root. Root stays clean.

## Tokens (bootstrap inputs)

`PROJECT_NAME` `PROJECT_SLUG` `GITHUB_ORG` `MEMBER_LIST` `MEMBER_PIPE` `DOMAIN_ONELINER` `DOMAIN_SEED_TERMS` `STACK_DESC` `MODEL_ID` `CREDENTIAL_VAR` `LEARNING_DEPTH` `V1_SCOPE`. Team size goes to the personalization log only. Stack and model credential are free-text with explicit `VERIFY-TODO`s where unknown.

Model (per #21 comment decision): default `opencode/muse-spark-1.3-contributor-free`. `.sandcastle/main.mts` template keeps that default but reads `OPENCODE_MODEL` from env. `.env.example` template is provider-neutral: `GH_TOKEN` (required) + `OPENCODE_MODEL` (required) + credential-var slot; Claude OAuth / Anthropic key remain only as commented examples. Contributing setup template reworded to match.

Credential-var answer (best-effort, also in template changelog 0.2.0): outside this harness, Muse Spark via opencode uses OpenCode Zen — `opencode providers login` (stored in `~/.local/share/opencode/auth.json`) or `OPENCODE_API_KEY` for headless/CI. Wizard defaults `CREDENTIAL_VAR` to `OPENCODE_API_KEY` with a VERIFY-TODO.

## Hard-excludes

`.env`, `.scratch/` content, `node_modules/`, `.sandcastle/logs|worktrees/`, secrets, product code never ship. Arabic exception intact: human-facing guide/reference HTML under `docs/knowledge/` may carry Arabic; everything else English.

## Ownership (summary; canonical in template repo `TEMPLATE-OWNERSHIP.md`)

Template-owned: `AGENTS.md`, `CONTRIBUTING.md` structure, `docs/agents/`, `.sandcastle/` runner, `package.json`, `skills-lock.json` + skills wiring, inbox `INDEX` structure, `docs/adr/` seed, `scripts/token-audit.mts`, updater. Project-owned (never overwritten): `CONTEXT.md` terms, `README.md` (both created by adopt only when absent, otherwise always protected), numbered ADRs, `docs/knowledge/**`, `PERSONALIZATION.log.md`, `.template-sync.json` machine record (updater reads, never overwrites), product code, local-only paths.

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

## Fixture adopt proof (#26 acceptance)

- Adopt: template repo `adopt.mts` (`--template <checkout> --downstream <old-project> --input <json> [--yes] [--non-interactive]`, same shape as the updater). Infer-then-confirm when `--input` is omitted (slug + org from downstream git remote, name from downstream manifest with directory fallback, rest from fixture defaults, every value shown for confirmation). Additive-only: missing template-owned files created; differing files left as `conflict` for an interactive per-file Yes (`--yes` applies only creates + manifest merge + sync record, never overwrites); `README.md` + `CONTEXT.md` always protected (created only when absent, otherwise untouched with no prompt); `package.json` is the sole structural merge (missing scripts/deps added, existing kept, semver-higher-wins with a report line); anything outside the template-owned list never written. Records: human `PERSONALIZATION.log.md` (created when absent, appended when present) + machine `.template-sync.json` (template source, version, inputs; updater prefers it when `--input` is omitted). Reruns are a safe no-op.
- Method: fixture old project (existing README, glossary `CONTEXT.md`, manifest with custom scripts + express + older tsx, product code `src/app.js`, differing `AGENTS.md` + `.gitignore`, git remote old-org/old-project), adopt with `--yes --non-interactive`, then rerun, then updater without `--input`, then `scripts/token-audit.mts`.
- Result 2026-09-20: run 1 `create=23` (18 rails + 3 root meta + sync record + log) `conflict=2` (`AGENTS.md`, `.gitignore` left as-is) `protected=2` (README + glossary byte-identical) `merged=1` (manifest: kept test/start/express, added sandcastle/typecheck + runner dep, tsx `^4.0.0` vs `^4.21.0` kept higher); adopt audit 0 tokens, 0 residue, 0 wording hits. Rerun `create=0 update=0 merged=0 skip=23 conflict=2 protected=2` with README/glossary/manifest/product code unchanged. Updater continuity without `--input` reads the sync record: `update=0 create=0 skip=21 conflict=3 protected=3`, manifest preserved. Inference check without `--input` (SSH remote + manifest name): slug/org/name inferred, fixture defaults for the rest. Downstream `scripts/token-audit.mts` PASS. Bootstrap regression on the same checkout: 23 files, 0/0/0, fire-once verified, token-audit PASS (empty-clone path unchanged; bootstrap audit skip list only gains `adopt.mts`, same precedent as `sync-template.mts`).
- Changelog: template `TEMPLATE-CHANGELOG.md` 0.4.0 (adopt + proof above).
