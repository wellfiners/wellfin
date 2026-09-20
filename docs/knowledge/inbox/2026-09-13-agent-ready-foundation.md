# Agent-ready mono-repo foundation survey

Status: inbox
Expires: 2026-12-12
Promotes to: scaffold work (map #1, ticket #7) + `CONTEXT.md` hygiene terms

- Map: https://github.com/wellfiners/wellfin/issues/1
- Ticket: https://github.com/wellfiners/wellfin/issues/4 (`wayfinder:research` — review this note before closing it)
- Method: `research` skill (primary sources only, one cited Markdown file) + `ask-matt` routing for the learning-record question.
- Standing rules honored: all records in English; prefer mature libraries over building from scratch; V1 = manual-ledger tracer, Egypt/EGP/English, append-only ledger; stack unfixed — recommend, do not lock; no new safety guardrails imposed (resurface points noted only).
- Work: local-only, no remote/GitHub actions.

## ask-matt routing (learning-record design question)

Question routed: "how to keep a growing fintech concept library without polluting agent context?"

`ask-matt` is a router, not a doer. Routing result:

- NOT `triage` (that is only for raw incoming issues the team did not create; these learning notes are team-created).
- NOT `implement`/`tdd` (there is no behavior to build; this is a words problem).
- YES — `grill-with-docs` as the stateful wrapper + `domain-modeling` as the vocabulary underneath + `writing-for-agents` as the doc-shape reference.

Reason: the repo has a working directory, so the stateful interview (`grill-with-docs`) is strictly better than stateless `grill-me`; the problem is fuzzy terminology ("account", "record", "merchant", "plan") which is exactly what `domain-modeling` owns (challenge fuzzy terms, resolve overloads, record hard-to-reverse choices as ADRs, keep `CONTEXT.md` a clean glossary); the output is agent-consumed docs, which is what `writing-for-agents` governs. Source: `.agents/skills/ask-matt/SKILL.md` (main flow step 1, vocabulary-underneath section, standalone definitions).

Practical consequence used below: `CONTEXT.md` stays a small glossary; `docs/knowledge/inbox/` is a staging inbox; `docs/adr/` is the only promotion target for decisions. Nothing learns its way into agent context without passing `domain-modeling`.

## 1. Single-context CONTEXT.md + docs/adr/ hygiene

Repo convention (primary, local): single-context layout — root `CONTEXT.md` + `docs/adr/`, with per-context `src/<context>/docs/adr/` only if a `CONTEXT-MAP.md` ever appears. Agents must read `CONTEXT.md` (or each relevant one behind `CONTEXT-MAP.md`) plus ADRs touching the work area before exploring; if the files do not exist, proceed silently and let `domain-modeling` create them lazily. Glossary terms are normative: use the glossary term in issue titles, refactors, hypotheses, test names; do not drift to synonyms it avoids; flag any output that contradicts an ADR explicitly. Source: `docs/agents/domain.md` (this repo) and root `AGENTS.md` pointing at it.

ADR format (primary, external): Nygard's five-part record — Title, Status (proposed/accepted/rejected/deprecated/superseded), Context, Decision, Consequences. Sources: Nygard template mirror (`github.com/jamesmh/architecture_decision_record/blob/master/adr_template_by_michael_nygard.md`); UK GDS summary of the same format (`gds-way.digital.cabinet-office.gov.uk/standards/architecture-decisions.html`); Red Hat explainer tracing the format to Nygard 2011 and ThoughtWorks Adopt (`redhat.com/en/blog/architecture-decision-records`).

Placement and numbering (primary): Markdown ADRs in `docs/adr/` of the version-controlled project (`docs/adr/NNNN-slug.md`), per the MADR paper which extends `adr-tools` with `adr new docs/adr madr` and an `adr-log` index (`ceur-ws.org/Vol-2072/paper9.pdf`); UK government ADR framework (2025) standardizes the same version-controlled, per-decision-file practice (`gov.uk/government/publications/architectural-decision-record-framework`). ADRs are immutable once accepted: never rewrite history, supersede with a new numbered ADR linking the old one. Only architecturally significant choices get ADRs (structure, non-functional characteristics, dependencies, interfaces, construction techniques) — not a changelog, not commit messages.

What belongs where (minimal rule for Wellfin):

| Location | Contains | Does NOT contain |
|---|---|---|
| `CONTEXT.md` | Agreed fintech glossary: term, one-line definition, what it is NOT, V1 scope note. ~50-150 lines. | Tutorials, options considered, math, API shapes, transient notes |
| `docs/adr/` | Accepted/superseded hard-to-reverse decisions (ledger core, stack picks, mono-repo layout, inference contract). Nygard/MADR shape. | Learning drafts, open questions |
| `docs/knowledge/inbox/` | Dated concept notes (Diataxis explanation/tutorials). Each has `Status: inbox`, `Expires:` date, `Promotes to:` pointer. | Normative definitions (those live in CONTEXT.md once agreed) |
| `AGENTS.md` + nested `apps/*/AGENTS.md` | How to work: commands, test entry, doc pointers, English-only rule. | Domain definitions (point at CONTEXT.md) |
| `.scratch/wellfin-foundation/` | Wayfinding map + open decision tickets. Interim local tracker until ticket 06 migrates to GitHub Issues. | Anything normative |

Diataxis justification (primary): four needs, four forms — tutorial (learning-oriented lesson), how-to (goal-oriented steps for a competent user), reference (neutral technical description, structured like the machinery), explanation (understanding-oriented, why/background). Sources: `diataxis.fr/` (home), `diataxis.fr/start-here`, `diataxis.fr/map`, `diataxis.fr/reference`, `diataxis.fr/reference-explanation`. Mapping: `CONTEXT.md` ≈ reference (austere glossary); `docs/adr/` ≈ explanation + decision log (why + consequences); `docs/knowledge/inbox/` ≈ tutorial/explanation drafts; `services/api` OpenAPI + `packages/contracts` ≈ reference. Do not mix them: Diataxis warns that blurring tutorial/how-to and reference/explanation breaks both needs — this is the theoretical basis for "do not pollute CONTEXT.md".

## 2. docs/knowledge/inbox/ promotion and expiry rules

Problem: a trio learning fintech will accumulate dozens of concept notes (interest math, amortization, payee matching, dashboard metrics). Without rules these leak into `CONTEXT.md` and every agent session.

Rules (enforced by convention + checklist, not new tooling):

1. Every note lives at `docs/knowledge/inbox/YYYY-MM-DD-slug.md` with front-matter: `Status: inbox | promoted | expired`, `Expires: YYYY-MM-DD` (default +90 days), `Promotes to: CONTEXT.md term | docs/adr/NNNN | nothing (drill only)`.
2. Inbox notes are NEVER loaded by default. Agents load a learning note only when its ticket explicitly points at it.
3. Promotion requires a `grill-with-docs`/`domain-modeling` pass: sharpen the term, check glossary collisions, then either (a) merge one glossary entry into `CONTEXT.md`, or (b) record a decision as a new ADR, then mark the note `Status: promoted` with a link. The note itself is kept (history) but excluded from session grounding.
4. Expiry is automatic: past `Expires:` with no promotion → `Status: expired`, moved out of the index. No revival by editing; write a new dated note if the topic returns.
5. Index file `docs/knowledge/inbox/INDEX.md` lists only `inbox` notes (title, expiry, promotes-to). Cap: max ~10 inbox notes; if the cap is hit, the oldest must be promoted or expired before adding. This is the anti-pollution backpressure valve.
6. Language: English only (see §3), even for Arabic-source concepts; keep the Arabic source as a cited reference link, translate the substance.

This implements the map's "single-context layout + learning record with promotion/expiry rules" note (`.scratch/wellfin-foundation/map.md`, Notes + Context hygiene) via the `ask-matt` route above.

## 3. English-only enforcement when humans chat in Arabic

Standing rule (user-locked, map Notes): conversation may be in Arabic; everything recorded in the repo is in English. Enforcement must be cheap and non-punitive:

- Policy layer: one line in root `AGENTS.md` ("Records language: English only. If the human writes in Arabic, reason in whichever language is clearest but write all files, commits, ADRs, and tickets in English; translate Arabic domain terms, keep the Arabic original only as a parenthetical citation on first use."). Precedence model is primary: closest `AGENTS.md` wins, explicit user chat prompts override files — so the rule binds the agent's writes, never the human's chat language. Sources: `agents.md/` ("closest AGENTS.md to the edited file wins; explicit user chat prompts override everything"); Codex project-scope docs (`developers.openai.com/codex/guides/agents-md`, merge-order/root-down); OpenCode hierarchy note (`github.com/IA-Generative/opencode-setup/blob/main/config/agents-md.md`, walk-up + nearest-wins + ~150-line guidance).
- Mechanical layer: `pre-commit` local hook + CI check, not a new framework. `pre-commit` runs hooks on every commit (trailing-whitespace, end-of-file-fixer, YAML checks are the canonical examples — `pre-commit.com/`; hook catalogue `github.com/pre-commit/pre-commit-hooks`). Add one `local` hook (e.g. `scripts/check-english-only.sh`) that scans staged `*.md` under `CONTEXT.md docs/ .scratch/**/issues/ .scratch/**/research/` for Arabic Unicode blocks (`[\u0600-\u06FF\u0750-\u077F\u08A0-\u08FF]`) and fails with "translate to English, keep Arabic only as cited parenthetical". Same script runs in CI so `--no-verify` cannot silently pollute the record.
- Session behavior: agent acknowledges in the human's language if useful, but the file write is always English. Review checklist item: "any non-English string outside a citation parenthetical?" If yes, translate before commit.

No new safety guardrails are added here — this is a records-hygiene rule the user already locked, not a product safety boundary.

## 4. Grounding elements every agent session must load

Minimal load order (keeps sessions inside the smart zone; sessions degrade past ~150k tokens per the `ask-matt` context-hygiene note citing `aihero.dev/ai-coding-dictionary/smart-zone` — treat as budget rationale, not a hard limit):

1. Closest `AGENTS.md` (root, then nested `apps/android/AGENTS.md` / `services/api/AGENTS.md` / `data-science/AGENTS.md` when touching those trees). Nested files for monorepos are the documented pattern (`agents.md/`, "large monorepo? use nested AGENTS.md files"; `github.com/agentsmd/agents.md` sample).
2. Root `CONTEXT.md` glossary (terms only).
3. ADRs touching the area (`docs/adr/` filtered by topic, newest wins on conflict; surface contradictions explicitly per `docs/agents/domain.md`).
4. The ticket being worked (`issues/NN-*.md`: question, constraints, blocking edges) + the map's Tasks/Out-of-scope lines for scope guard.
5. Spec pointers the ticket names (V1 12-screen spec slice, ledger invariants) — load the slice, not the whole spec.
6. `docs/knowledge/inbox/INDEX.md` inbox list only — load a full learning note solely by explicit pointer.

Explicitly NOT loaded by default: full learning notes, closed-ticket history, full API reference, notebooks, model artifacts. Fetch on demand.

## 5. Mono-repo layout for Android + backend + data-science that stays inference-ready

Android guidance (primary): at least two layers — UI layer (Compose + state holders/ViewModels, unidirectional data flow) and data layer (repositories over data sources, immutable exposed data); optional domain layer (use cases) only for reused/complex logic. Sources: `developer.android.com/topic/architecture` (recommended app architecture), `/topic/architecture/ui-layer`, `/topic/architecture/data-layer`. Local persistence: Room (Entity + DAO + Database class) over SQLite for offline cache; migrations via `Migration`/`AutoMigration`. Source: `developer.android.com/training/data-storage/room/index.html` (+ v2 page). Modularization: multi-Gradle-module projects buy reusability/visibility/delivery/ownership at the cost of overhead; start coarse, split when size/complexity demands it; feature modules per screen-flow, common `core/ui` modules for shared theming/widgets. Sources: `developer.android.com/topic/modularization`, `/topic/modularization/patterns`, Now-in-Android reference app (cited therein).

Backend guidance (primary): FastAPI + SQLModel + PostgreSQL is the maintained happy path — FastAPI's own SQL tutorial uses SQLModel (`fastapi.tiangolo.com/tutorial/sql-databases`; source mirror `github.com/fastapi/fastapi/blob/master/docs/en/docs/tutorial/sql-databases.md`); SQLModel is deliberately the Pydantic+SQLAlchemy thin layer by the same author (`sqlmodel.tiangolo.com/`, `github.com/fastapi/sqlmodel/blob/main/README.md`); the official full-stack template wires FastAPI + SQLModel + PostgreSQL + Docker Compose + Pytest + JWT + Mailpit with `backend/app/{models,api,crud}.py` and `uv sync`/`fastapi dev` workflow (`github.com/fastapi/full-stack-fastapi-template`, `fastapi.tiangolo.com/project-generation/`, `development.md`, `backend/README.md`).

Data-science / inference-ready guidance (primary): MLflow Model = directory + `MLmodel` file with named flavors; `python_function` flavor is the universal serving interface (`mlflow.pyfunc.load_model`, `mlflow models serve` as REST); signatures stored in the `MLmodel` file are enforced by serving tools; batch via Spark UDF or SageMaker/ACI/AKS/Databricks endpoints. Sources: `mlflow.org/docs/latest/models` (and `/docs/2.0.0/models.html`), `mlflow.org/docs/latest/rest-api.html`. Inference-ready rule: notebooks never ship to the app; training notebooks export a versioned artifact (`data-science/models/<name>/<version>/`) with `MLmodel` + signature + example input; the API depends only on the artifact's schema, never on notebook code. For V1 (manual ledger, no ML), the integration point is a stub contract (see scaffold) so ticket 07 has a seam without building inference now.

Resulting minimal mono-repo shape (single repo, no meta-build tool in V1; Gradle + uv/pip + notebooks coexist by directory convention):

- `apps/android/` — native Kotlin app (Compose, ViewModel+UDF, Room). Single Gradle module in V1; split to `:feature:*` + `:core:ui` only when the modularization pitfalls bite.
- `services/api/` — FastAPI service (SQLModel models, `/api/v1/*` routes, Alembic migrations, Pytest). Postgres via Docker Compose `db` service in dev.
- `packages/contracts/` — shared ledger schema + invariants (Pydantic models / JSON Schema / OpenAPI fragment). Single source of truth imported by API tests, Android fake-data builders, and notebook fixtures. Prevents the three tracks drifting on field names.
- `data-science/notebooks/` (exploration, `*.ipynb` git-stripped or Jupytext-paired) + `data-science/models/` (MLflow-format exports only) + `data-science/fixtures/` (anonymized ledger CSVs with the same columns as `packages/contracts`).
- `docs/adr/` + `docs/knowledge/` + `CONTEXT.md` + `AGENTS.md` (+ nested per-tree `AGENTS.md`).
- `.scratch/wellfin-foundation/` — interim map/tickets/research until ticket 06 migrates to GitHub Issues.

Cross-tree coupling rule: `apps/android ↔ services/api` communicate only through versioned HTTP contracts (`/api/v1/*`); `services/api ↔ data-science` communicate only through the model-artifact directory + a stub `POST /v1/inference/categorize` echoing its input schema in V1. No direct imports across trees.

## 6. Adopt-over-build scaffold candidates — recommend, do not lock

Backend — three candidates surveyed against primary docs:

| Candidate | What it is (primary) | Fit for append-only EGP ledger tracer | Verdict |
|---|---|---|---|
| **FastAPI + Postgres (self-run, Docker Compose)** | Official template: FastAPI + SQLModel + Postgres + Docker + Pytest + JWT (`github.com/fastapi/full-stack-fastapi-template`). SQLModel tutorial is the documented ORM path (`fastapi.tiangolo.com/tutorial/sql-databases`). | Best control over append-only invariants (DB CHECK constraints, immutable `ledger_entries`, derived balances as views), zero vendor lock, matches backend skill in trio, portable to any host. Costs: team owns auth/backups. | **Recommended default for V1.** Start with only the `db` + `backend` slices of the template; defer Traefik/React/email. |
| **Supabase (managed Postgres)** | Postgres core (unabstracted) + PostgREST/GraphQL API + GoTrue Auth + Realtime + Studio; architecture page lists each component with licenses (`supabase.com/docs/guides/getting-started/architecture`). Row-Level Security is the authorization primitive: enable RLS per table, write per-operation SQL policies, test with `supabase test db` (`supabase.com/docs/guides/database/postgres/row-level-security`, `.../row-level-security.md`). Self-host via Docker Compose if needed (`supabase.com/docs/guides/self-hosting`). | Fastest tracer (auth + RLS + instant REST without writing CRUD), still real Postgres so append-only design ports cleanly. Costs: managed-service dependence, RLS learning curve, Egypt-latency/billing unknowns. | **Approved alternative.** Because both options are Postgres, keep migrations plain SQL/Alembic so a later move either way is cheap. Do not lock in this ticket. |
| **Firebase Firestore** | Document NoSQL with realtime listeners + offline persistence on Android/Apple/Web (`firebase.google.com/docs/firestore`, `.../manage-data/enable-offline`); hard limits (1 MiB/doc, 40k index entries/doc, composite-index caps — `firebase.google.com/docs/firestore/quotas`). | Wrong data model for a ledger: weak joins (only recently approaching parity via pipelines), document-size/index caps fight append-only audit tables, relational integrity lives in app code instead of the DB. Offline is nice but Room already covers V1 offline. | **Not recommended for the ledger core.** Revisit only for chat/presence-style features, never for money movement. |

Mobile — two candidates:

| Candidate | What it is (primary) | Fit for V1 Egypt/English manual ledger | Verdict |
|---|---|---|---|
| **Native Kotlin + Jetpack Compose + Room** | Officially recommended toolkit and architecture (Compose for UI, ViewModel+UDF, repositories, Room for offline cache — `developer.android.com/topic/architecture*`, `training/data-storage/room/`). | Matches the trio's Android member, best offline-first story, no JS bridge, Play-store native. Costs: Android-only (iOS later = second app). | **Recommended for V1.** |
| **Expo (React Native framework)** | Official RN framework per Meta/RN team; CNG generates `android/`+`ios/` on demand (`npx expo prebuild`), eject removed since SDK 46, Hermes + New Architecture (Fabric/TurboModules) by default; 100+ maintained native modules; EAS for CI/build/submit (`docs.expo.dev/llms.txt`, `docs.expo.dev/workflow/overview`, `docs.expo.dev/bare/overview`, `expo.dev/`). | Buys iOS/web reuse + OTA updates, but adds JS toolchain + EAS dependence for a V1 that is explicitly Egypt/English/Android-tracer-first with a native Android dev on the team. | **Deferred.** Revisit if an iOS or web client becomes in-scope; CNG means the door stays open without committing now. |

Data-science: **notebooks + scikit-learn + MLflow pyfunc export**. No custom serving framework, no training inside the API process. V1 ships zero models; the scaffold reserves `data-science/models/` + the inference stub so ticket 07 can define categorization/forecast contracts against the append-only ledger (ML-ready by construction: immutable entries, stable IDs, derived balances reproducible from history).

## 7. Where guardrails may resurface (noted, not imposed)

User declined extra safety guardrails at map time (map Notes + Out-of-scope). Do not add any in this ticket. Flagged resurface points for later maps: chatbot "no personalized financial advice" boundary (currently out of scope — educational Q/A only); auth/multi-user + backup/privacy story once the ledger leaves single-device; Arabic UI/records roadmap post-V1; reminder/ringtone data retention; payee-autocomplete data sourcing and PII. Each gets its own decision ticket + ADR when it graduates; none block the V1 tracer.

## 8. Minimal recommended scaffold (dirs + docs + guardrail checklist)

Recommended tree (create in ticket 06; shown here for decision, not built in this ticket):

```text
/  (repo root)
├── AGENTS.md                      # records-English-only rule, session load order, per-tree pointers
├── CONTEXT.md                     # fintech glossary only (term, definition, NOT, V1 note)
├── docs/
│   ├── adr/
│   │   ├── 0000-template.md       # Nygard/MADR: Title, Status, Context, Decision, Consequences
│   │   └── 0001-append-only-ledger.md  # via ticket 04 (example, not decided here)
│   └── knowledge/
│       ├── inbox/
│       │   ├── INDEX.md               # inbox only (max ~10), with Expires + Promotes-to
│       │   └── YYYY-MM-DD-slug.md     # dated concept notes (Status/Expires/Promotes-to header)
├── apps/
│   └── android/                   # native Kotlin+Compose+Room; single Gradle module in V1
│       └── AGENTS.md
├── services/
│   └── api/                       # FastAPI+SQLModel; trimmed full-stack-fastapi-template slice
│       ├── app/{models.py,api/,crud.py}
│       ├── tests/                 # ledger-invariant tests (no negative balances by edit, etc.)
│       └── AGENTS.md
├── packages/
│   └── contracts/                 # shared ledger JSON Schema + Pydantic models + OpenAPI fragment
├── data-science/
│   ├── notebooks/                 # exploration only; never imported by the API
│   ├── models/<name>/<version>/   # MLflow-format exports (MLmodel + signature) — empty in V1
│   ├── fixtures/                  # anonymized ledger CSVs matching packages/contracts
│   └── AGENTS.md
├── scripts/
│   └── check-english-only.sh      # Arabic-block detector for pre-commit + CI
├── .pre-commit-config.yaml        # trailing-whitespace, end-of-file-fixer + local english-only hook
└── .scratch/wellfin-foundation/   # map + issues/ + research/ (interim; ticket 06 migrates to GitHub)
```

Trimmed `services/api` starter (adopt, do not rebuild): copy `backend/{app,tests,scripts}` + `compose.yml db service` from `fastapi/full-stack-fastapi-template`; drop React frontend, Traefik, Mailpit extras for V1; keep `uv sync`, `bash scripts/prestart.sh`, `fastapi dev`, `bash scripts/test.sh` workflow (`development.md`, `backend/README.md`).

V1 API surface (stub in scaffold, built in ticket 05): `POST /api/v1/accounts`, `POST /api/v1/entries` (inflow/outflow/transfer, append-only), `GET /api/v1/accounts/{id}/balance` (derived), `GET /api/v1/history`, `GET /api/v1/summary`, `POST /v1/inference/categorize` (V1: schema-echo stub for ticket 07).

Guardrail checklist (every PR / every agent session):

- [ ] Records in English? (`scripts/check-english-only.sh` green; Arabic only as cited parenthetical)
- [ ] Glossary terms match `CONTEXT.md`? (no synonym drift; new term → learning note, not silent rename)
- [ ] ADR added or linked for any hard-to-reverse choice? (Nygard shape, numbered, never rewrite accepted ADRs)
- [ ] `docs/knowledge/inbox/INDEX.md` inbox ≤ 10, none past `Expires:`?
- [ ] Ledger append-only preserved? (no UPDATE/DELETE on entries; corrections are reversing entries; balances derived)
- [ ] V1 scope kept? (Egypt/EGP/English/manual-entry only; no sync, no iOS, no chatbot advice, no auto-plans)
- [ ] Adopt-over-build respected? (mature lib used or justification recorded; cross-tree contracts versioned)
- [ ] Session loaded only the grounding set (§4)? (no full notebooks/models/history dumps in context)
- [ ] No new product safety guardrails smuggled in? (resurface points go to future tickets, §7)

## Sources (primary only)

- Repo: `docs/agents/domain.md`, `AGENTS.md`, `.scratch/wellfin-foundation/map.md`, `.agents/skills/ask-matt/SKILL.md`, `.agents/skills/research/SKILL.md`.
- ADRs: Nygard template (`github.com/jamesmh/architecture_decision_record/.../adr_template_by_michael_nygard.md`); GDS Way (`gds-way.digital.cabinet-office.gov.uk/standards/architecture-decisions.html`); MADR paper (`ceur-ws.org/Vol-2072/paper9.pdf`); Red Hat ADR explainer (`redhat.com/en/blog/architecture-decision-records`); GOV.UK ADR Framework (`gov.uk/government/publications/architectural-decision-record-framework`).
- Docs shape: Diataxis (`diataxis.fr/`, `diataxis.fr/start-here`, `diataxis.fr/map`, `diataxis.fr/reference`, `diataxis.fr/reference-explanation`).
- Agent grounding: AGENTS.md standard (`agents.md/`, `github.com/agentsmd/agents.md`); Codex project scope (`developers.openai.com/codex/guides/agents-md`); OpenCode hierarchy (`github.com/IA-Generative/opencode-setup/blob/main/config/agents-md.md`); hooks mechanism (`pre-commit.com/`, `github.com/pre-commit/pre-commit-hooks`).
- Android: app architecture (`developer.android.com/topic/architecture`, `.../ui-layer`, `.../data-layer`); modularization (`developer.android.com/topic/modularization`, `.../modularization/patterns`); Room (`developer.android.com/training/data-storage/room/index.html`).
- Backend: FastAPI SQL tutorial (`fastapi.tiangolo.com/tutorial/sql-databases`); SQLModel (`sqlmodel.tiangolo.com/`); full-stack template (`github.com/fastapi/full-stack-fastapi-template`, `fastapi.tiangolo.com/project-generation/`, `development.md`, `backend/README.md`); Supabase architecture (`supabase.com/docs/guides/getting-started/architecture`), RLS (`supabase.com/docs/guides/database/postgres/row-level-security`), self-hosting (`supabase.com/docs/guides/self-hosting`); Firestore (`firebase.google.com/docs/firestore`, `.../manage-data/enable-offline`, `.../firestore/quotas`).
- Mobile alt: Expo (`docs.expo.dev/llms.txt`, `docs.expo.dev/workflow/overview`, `docs.expo.dev/bare/overview`, `expo.dev/`).
- DS/inference: MLflow Models (`mlflow.org/docs/latest/models`, `mlflow.org/docs/2.0.0/models.html`), REST API (`mlflow.org/docs/latest/rest-api.html`).
