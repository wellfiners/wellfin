# Finance models and libraries survey

Status: inbox
Expires: 2026-12-12
Promotes to: ledger-core ADR (map #1, ticket #5) + `CONTEXT.md` transaction-model terms

- Map: https://github.com/wellfiners/wellfin/issues/1
- Ticket: https://github.com/wellfiners/wellfin/issues/3 (`wayfinder:research` — review this note before closing it)
- Method: `research` skill (primary sources only, one cited Markdown file).
- Standing rules honored: all records in English; adopt-over-build (if a mature library satisfies the need, heavily prefer adoption); V1 = Egypt/EGP/English-only, manual entry, append-only ledger with derived balances; no remote/GitHub actions (local files only).
- V1 implication: model for import/multi-currency/fuzzy/autocomplete compatibility now, build only what V1 manual ledger needs.

## 1. Transaction / budget models (Firefly III, Actual Budget, YNAB)

**Firefly III (primary: docs + repo).** Money always moves A -> B. User-facing types are `withdrawal` (asset -> expense), `deposit` (revenue -> asset), `transfer` (asset -> asset, or liability -> liability), plus system types `opening balance`, `reconciliation`, `liability credit`. Storage is double-entry: one journal holds two transactions (source -X, destination +X). Splits are supported with constraints: withdrawal splits only destination legs; deposit splits end in one asset account; transfer splits share source+destination. Sources: `docs.firefly-iii.org/explanation/financial-concepts/transactions/`, `docs.firefly-iii.org/references/firefly-iii/transaction-types/`, `docs.firefly-iii.org/references/firefly-iii/account-types/`, repo `github.com/firefly-iii/firefly-iii`.

**Actual Budget (primary: API reference).** Core objects: `Account {id, name, offbudget, closed}`, `Transaction {id, account, date, amount, payee, category, notes, cleared}`, `Category` nested under `CategoryGroup` with `is_income` flag (single income group). Amounts stored as integer minor units (cents; negative = expense). Balance = sum of transaction amounts. Splits via `is_parent: true` on parent + `subtransactions[]` with `is_child: true` (each child needs at least `amount`, `account`, `date`, `parent_id`). Separate `importTransactions` (runs rules + dedup via `imported_id`) vs `addTransactions` (raw insert). Sources: `actualbudget.org/docs/api/`, `github.com/actualbudget/actual/blob/master/packages/docs/docs/api/reference.md`.

**YNAB model (primary: API).** `Transaction {id, account_id, date, amount, payee_id/payee_name, category_id, memo, cleared, approved, flag_color, import_id, subtransactions[]}`. Split = `category_id: null` + `subtransactions[]`; split-category name renders as `Split`. Amounts in milliunits (1000 = 1 currency unit). `import_id` gives idempotent import (409 on duplicate). Delta sync via `server_knowledge` / `last_knowledge_of_server`. Sources: `api.ynab.com/` (Authentication, Data Formats, Delta Requests), `api.ynab.com/v1` endpoints, OpenAPI spec `github.com/ynab/ynab-sdk-python/blob/1.8.0/open_api_spec.yaml`.

**Takeaway for Wellfin V1:** adopt the shared vocabulary (Account, Transaction/Ledger Entry, Payee, Category/CategoryGroup, Split, Transfer vs Withdrawal/Deposit, `import_id`/`memo`/`cleared`), but do not embed any of these apps as a dependency — they are full apps/servers, not ledger libraries.

## 2. Import shapes (deferred post-V1, reserve fields now)

- **ISO 20022 / CAMT (primary: `iso20022.org`, `iso20022.org/iso-20022-message-definitions`).** XML message repository; bank-statement family is `camt.052` (report), `camt.053` (statement), `camt.054` (notification). Each entry carries `EndToEndId`, `MndtId`, `Acct`, `Amt`, `CdtDbtInd`, `BookgDt`. Firefly III Data Importer already documents `Import a CAMT.05x file` (`docs.firefly-iii.org/tutorials/data-importer/camt/`).
- **OFX (primary: `ofx.net`, OFX Banking Specification v2.3).** SGML/XML request-response with `OFXHEADER`, signon + bank/credit-card message sets; statement transaction carries `FITID` (stable bank id), `DTPOSTED`, `TRNAMT`, `NAME`/`MEMO`. Spec mirrors: `ofx.net`, `docs.rs/crate/ofx-rs/latest/source/spec/OFX_Banking_2.3.pdf`.
- **QIF (primary: Intuit spec via archive `web.intuit.com/support/quicken/docs/d_qif.html`).** Line-coded text (`D` date, `T` amount, `P` payee, `M` memo, `L` category/transfer/class, `S/E/$` split lines, `^` end-of-entry; headers `!Type:Bank/Cash/CCard/...`). Ambiguous encodings/dates, no currency field — legacy only.

**Takeaway:** build nothing for import in V1, but keep columns `external_id` (FITID / EndToEndId / import_id), `raw_source` (OFX/QIF/CAMT enum, nullable), `imported_at`, and `payee_name_raw` from day one so post-V1 import is non-breaking.

## 3. Ledger pattern: single-entry UX over double-entry-capable storage

- **Beancount (primary: `beancount.github.io/docs/`, repo `github.com/beancount/beancount`).** Python double-entry text ledger; every transaction balances to zero; five roots `Assets/Liabilities/Income/Expenses/Equity`; `balance`/`pad` directives for assertions; query via `bean-query`.
- **hledger / Ledger (primary: `hledger.org/manual.html`, `ledger-cli.org`).** Haskell/C++ plain-text double-entry journals; `LEDGER_FILE` journal, `-s/--strict` balance/assertion checks, `print/register/balance/balancesheet/incomestatement` reports, CSV import via rules files, JSON/SQL/CSV export.
- **Postgres pattern (derived from above, no single canonical lib).** Append-only `ledger_entries` (no UPDATE/DELETE; corrections are reversing entries), balances as `SUM()` views, `CHECK` constraints for invariants, `pybookkeeping`-style single-purpose libs are immature — avoid.

**Takeaway:** adopt the *pattern*, not the binary. V1 builds a minimal Postgres append-only table with derived balances; keep beancount/hledger as offline validator/doc reference, not a runtime dependency.

## 4. Other libs

- **Interest/amortization (primary: `numpy.org/numpy-financial/`, `numpy.org/numpy-financial/latest/pmt.html`, `ipmt.html`, repo `github.com/numpy/numpy-financial`).** Drop-in NumPy-TV M functions `pmt/ppmt/ipmt/fv/pv/nper/irr/npv/rate` with documented equation `fv + pv*(1+rate)**nper + pmt*(1+rate*when)/rate*((1+rate)**nper-1) == 0`. Mature, tested.
- **Money/currency (primary: `javamoney.github.io/api.html`, JSR-354 `jcp.org/en/jsr/detail?id=354`, Python Dinero `wilfredinni.github.io/dinero/` + `pypi.org/project/dinero/`, ISO 4217 `iso.org/iso-4217-currency-codes.html`).** Rules: never float for money; store integer minor units (Actual = cents, YNAB = milliunits); `Decimal` for math; ISO 4217 code column (`EGP`, 2 decimals). JVM = JSR-354 Moneta; Python = Dinero; JS = Dinero.js equivalent when needed.
- **Fuzzy payee matching (primary: `rapidfuzz.github.io/RapidFuzz/`, repo `github.com/rapidfuzz/RapidFuzz`, `pypi.org/project/RapidFuzz/`; JS alt `fusejs.io`).** RapidFuzz = MIT, C++ Levenshtein/Jaro-Winkler/token ratios, drop-in `fuzzywuzzy` replacement with fixed `partial_ratio`. Deferred to post-V1 (V1 has no autocomplete); when needed, adopt — do not hand-roll edit distance. Note: Arabic-normalization layer needed later.
- **Categorization taxonomy (primary: `plaid.com/docs/api/products/transactions/`, `plaid.com/docs/transactions/pfc-migration/`, Enrich MCC note `plaid.com/docs/api/products/enrich/`).** Plaid Personal Finance Categories (`primary` + `detailed` + `confidence_level`, PFCv2 taxonomy CSV) is the best-maintained open-reference taxonomy; Merchant Category Codes are 4-digit ISO 18245 strings (beta coverage, card transactions). Use as reference shape + seed mapping, not as a runtime dependency.

## 5. Adopt vs build (required table)

| Need | Mature option | Recommendation | Why |
|---|---|---|---|
| Transaction/split/category/budget model | Firefly III journals + YNAB/Actual splits (`is_parent`/`subtransactions`, `import_id`) | **Adopt model, build tables** | Apps are not embeddable libs; their shapes are proven. Build minimal `accounts`, `transactions`, `splits`, `categories`, `payees` matching their field names so future migration/import is cheap. |
| Import compat (OFX/QIF/CAMT.053) | OFX spec (`FITID`), CAMT.053 (`EndToEndId`), QIF line spec, YNAB `import_id` | **Build nothing in V1; reserve fields** | V1 is manual-entry only. Keep `external_id`, `raw_source`, `payee_name_raw` nullable now; add parsers (`ofxparse`/`camt` libs) post-V1 without schema break. |
| Ledger engine (double vs single entry) | Beancount / hledger / Ledger journal + balance assertions | **Adopt pattern, build minimal Postgres ledger** | File-based CLIs are too heavy as V1 runtime deps. Build append-only `ledger_entries` (immutable, derived `SUM` balances, `CHECK`s); single-entry UX (inflow/outflow/transfer) over double-entry-capable rows. Use beancount only as offline cross-check. |
| Interest / amortization math | `numpy-financial` (`pmt`, `ppmt`, `ipmt`, `fv`, `pv`, `nper`, `irr`) | **Adopt** | Tested TVM implementation with documented equation; hand-rolling invites rounding/period errors. Non-Python stacks port formulas but test against `numpy-financial` vectors. |
| Money / currency (EGP-only V1) | Integer minor units + `Decimal`; Dinero (Py) / JSR-354 Moneta (JVM) / ISO 4217 | **Adopt convention + thin value object** | Store `amount_piastres INTEGER` + `currency CHAR(3) DEFAULT 'EGP'`; no floats. Keep currency + `exchange_rates` stub for post-V1 multi-currency. Full JSR-354 only when JVM stack is chosen. |
| Fuzzy payee matching / did-you-mean | RapidFuzz (Py) / Fuse.js (JS) | **Adopt when needed, deferred in V1** | MIT, fast, correct edge cases (`partial_ratio` fixes). V1 defers autocomplete per map; post-V1 adopt with threshold + Arabic normalization instead of custom Levenshtein. |
| Categorization taxonomy | Plaid PFC (`primary`/`detailed`) + ISO 18245 MCC | **Adopt shape, build small V1 seed** | Plaid taxonomy is the most maintained reference; MCC coverage is beta. Ship ~15–20 Egypt-relevant categories in V1 mapped to PFC `detailed` values; vendor nothing heavy. Inference (ticket 07) consumes the same labels. |

## 6. Minimal V1 consequences (for ticket 04)

1. Tables: `accounts`, `categories` (+groups), `payees`, `transactions` (+`splits` or leg rows), all append-only with `import_id`/`external_id` reserved.
2. Money: integer piastres + `EGP` default; `Decimal` in code; no float column.
3. No import/fuzzy/category-ML code in V1 — only the columns and label mapping that keep those doors open.

## Sources (primary only)

- Firefly III: `docs.firefly-iii.org/explanation/financial-concepts/transactions/`, `docs.firefly-iii.org/references/firefly-iii/transaction-types/`, `docs.firefly-iii.org/references/firefly-iii/account-types/`, `docs.firefly-iii.org/tutorials/data-importer/camt/`, `github.com/firefly-iii/firefly-iii`.
- Actual Budget: `actualbudget.org/docs/api/`, `github.com/actualbudget/actual/blob/master/packages/docs/docs/api/reference.md`.
- YNAB: `api.ynab.com/`, `api.ynab.com/v1`, `github.com/ynab/ynab-sdk-python/blob/1.8.0/open_api_spec.yaml`.
- ISO 20022: `iso20022.org/iso-20022-message-definitions`.
- OFX: `ofx.net`, OFX Banking Specification v2.3.
- QIF: Intuit QIF spec (`web.intuit.com/support/quicken/docs/d_qif.html` via web archive).
- Beancount: `beancount.github.io/docs/`, `github.com/beancount/beancount`.
- hledger/Ledger: `hledger.org/manual.html`, `ledger-cli.org`.
- numpy-financial: `numpy.org/numpy-financial/`, `numpy.org/numpy-financial/latest/pmt.html`, `numpy.org/numpy-financial/latest/ipmt.html`, `github.com/numpy/numpy-financial`.
- Money: `javamoney.github.io/api.html`, `jcp.org/en/jsr/detail?id=354`, `wilfredinni.github.io/dinero/`, `pypi.org/project/dinero/`, `iso.org/iso-4217-currency-codes.html`.
- Fuzzy: `rapidfuzz.github.io/RapidFuzz/`, `github.com/rapidfuzz/RapidFuzz`, `pypi.org/project/RapidFuzz/`, `fusejs.io`.
- Taxonomy: `plaid.com/docs/api/products/transactions/`, `plaid.com/docs/transactions/pfc-migration/`, `plaid.com/docs/api/products/enrich/`.
