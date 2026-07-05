# Harvey Singh — Duffel Platform Contribution Research

**Repository:** Duffel Platform (Elixir umbrella monorepo)
**Period analysed:** 25 February 2026 – 22 June 2026 (~4 months)
**Git identities:** `harvey-duffel <harvey@duffel.com>` (55 commits), `Harvey Singh <harvey@duffel.com>` (1 commit)
**Total non-merge commits:** 56
**Total lines added:** 14,639
**Total lines removed:** 1,288
**Net lines added:** 13,351
**Files changed:** 371
**Database migrations authored:** 7
**Pull requests merged:** 56 (every commit references a PR number)

---

## High-Level Summary

Harvey Singh joined the Duffel Platform repository in late February 2026 and quickly ramped into a core contributor on the Payments team. Over a 4-month period, he delivered six major initiatives spanning financial systems (profit sharing, commission estimates, reconciliation), data ingestion pipelines (CSV imports for three supplier formats), schema migrations, and extensive admin UI work in Jet (Duffel's internal tool built with Phoenix LiveView).

His work consistently spans the full stack of the Elixir umbrella: from database migrations in `duffel_core`, through domain logic in `duffel_payments` and `duffel_treasury`, to LiveView surfaces in `duffel_jet`, CLI operational tooling in `duffel_cli`, and public API controllers in `duffel_web`. The contribution trajectory shows steady month-over-month growth: 2 commits in February, 9 in March, 13 in April, 17 in May (peak), and 15 in June (through the 22nd).

---

## Commit Activity by Month

| Month | Commits | Lines Added | Lines Removed | Key Focus |
|-------|---------|-------------|---------------|-----------|
| Feb 2026 | 2 | 76 | 22 | Conferma sidebar, open cases count |
| Mar 2026 | 9 | 2,224 | 557 | Tokenised cards lifecycle, Conferma reconciliation platform |
| Apr 2026 | 13 | 3,417 | 419 | Commission estimates, Conferma completion, reconciliation improvements |
| May 2026 | 17 | 5,303 | 416 | Profit share system (full build), CSV imports, payout management |
| Jun 2026 | 15 | 3,619 | 374 | Profit share refinements, FX normalization, CLI tooling |

---

## Codebase Footprint by Umbrella App

| App | File Touches | Role in Architecture |
|-----|-------------|---------------------|
| `duffel_jet` | 148 | Internal admin tool (Phoenix LiveView). Harvey built extensive LiveView pages for profit sharing, reconciliation, and payout management. |
| `duffel_payments` | 92 | Core payments domain logic. Harvey authored the profit share orchestrator, commission estimate pipeline, payout workflows, and revenue discrepancy handling. |
| `duffel_stays` | 32 | Hotel/stays vertical. Harvey built and maintained CSV commission ingestion for Booking.com, Onyx, and Priceline suppliers. |
| `duffel_treasury` | 22 | Financial reconciliation. Harvey worked on Enett, ARC, BSP, and Conferma reconciliation logic, particularly around manual refund exclusions. |
| `duffel_core` | 16 | Shared core (migrations, schemas, behaviours). Harvey authored 7 database migrations and moved shared commission contracts here. |
| `duffel_cli` | 14 | CLI task runner. Harvey built operational tasks for profit share runs, payout deletion, commission clearing, and plan updates. |
| `duffel_web` | 9 | Public-facing API. Harvey worked on payment intent cancellation error mapping, manual refund controllers, and card passthrough support. |
| `duffel_identity` | 6 | Organisation/user management. Harvey added the tokenised cards mode column and updated organisation schemas. |
| `duffel_air` | 5 | Airline order management. Harvey updated order manual operations and reconciliation logic for Conferma card auto-resolution. |
| `duffel_white_label` | 1 | White-label frontend. Minor README update as part of tokenised cards work. |
| `duffel_seed` | 1 | Database seeding. Updated seed data for tokenised cards mode. |

---

## Most Frequently Modified Files

These files represent the core of Harvey's work and indicate where he has the deepest familiarity:

| File | Touches | Context |
|------|---------|---------|
| `config/config.exs` | 10 | Application configuration for new CLI tasks, Oban workers, and mock setup |
| `apps/duffel_jet/lib/jet/views/payments/profit_share_view.ex` | 8 | View helpers for the profit share admin pages |
| `apps/duffel_jet/test/jet/live/payments/profit_share/plan_show_test.exs` | 7 | Integration tests for the profit share plan show page |
| `apps/duffel_jet/lib/jet/router.ex` | 7 | Router configuration for new Jet pages and routes |
| `apps/duffel_jet/lib/jet/live/payments/profit_share/plan_show.ex` | 6 | LiveView module for profit share plan detail page |
| `apps/duffel_payments/lib/payments/profit_share/orchestrator.ex` | 5 | Core profit share calculation orchestrator |
| `apps/duffel_jet/lib/jet/authorization.ex` | 5 | Authorization configuration for new Jet routes and permissions |
| `apps/duffel_payments/lib/payments/profit_share/summary.ex` | 4 | Profit share run summary/result formatting |
| `apps/duffel_payments/lib/payments/profit_share/rule.ex` | 4 | Profit share rule schema and changesets |
| `apps/duffel_stays/lib/stays/commission/actuals/bookingcom.ex` | 4 | Booking.com CSV commission parser |

---

## Database Migrations Authored

Harvey authored 7 production database migrations, all following the project's convention of explicit `up`/`down` methods (no `change`):

1. **20260302** — `add_tokenised_cards_mode_to_organisations` — Added a new `tokenised_cards_mode` column to the `identity_organisations` table to replace the boolean `tokenised_cards_enabled` flag with a more flexible enum-style column supporting multiple modes.

2. **20260303** — `drop_tokenised_cards_enabled_column_from_organisations` — Safely dropped the legacy `tokenised_cards_enabled` column after the backfill task had migrated all existing organisations to the new `tokenised_cards_mode` column. This demonstrates proper schema lifecycle management: add new column, backfill, drop old column.

3. **20260410** — `create_payments_supplier_commission_estimates_table` — Created the `payments_supplier_commission_estimates` table to store supplier commission forecasts. This was the foundational schema for the entire commission estimates pipeline.

4. **20260514** — `add_lock_version_to_profit_share_tables` — Added `lock_version` columns to both `profit_share_plans` and `profit_share_rules` tables to support optimistic locking, preventing concurrent edits from silently overwriting each other in the admin UI.

5. **20260515** — `drop_lock_version_defaults_from_profit_share_tables` — Removed the database-level defaults on the `lock_version` columns, letting Ecto's optimistic locking mechanism manage the initial value instead. This was a follow-up fix after discovering that DB defaults conflicted with Ecto's expectations.

6. **20260603** — `make_state_nullable_on_stays_onyx_commission_payouts` — Made the `state` field nullable on the stays Onyx commission payouts table to accommodate CSV imports from Onyx that don't always include a state value.

7. **20260609** — `update_profit_share_effective_date_comments` — Removed stale column comments that referenced month-boundary constraints that were no longer enforced, after the system was updated to allow non-month-aligned effective dates.

---

## Major Projects — Detailed Breakdown

### 1. Profit Share System (Full Build)

**Scope:** ~18 commits, ~6,000+ lines added
**Apps touched:** `duffel_payments`, `duffel_jet`, `duffel_cli`, `duffel_core`
**Date range:** May 12 – June 22, 2026

This was Harvey's largest initiative and represents a complete feature build from database schema through business logic to admin UI and operational tooling. The profit share system enables Duffel to automatically calculate and manage revenue sharing with supplier partners based on configurable tiered pricing rules.

**What was built:**

- **Data model and schema:**
  - Profit share plans (with organisation association, effective dates, and soft-delete support)
  - Profit share rules (with tiered thresholds, percentage rates, and plan association)
  - Optimistic locking via `lock_version` columns to prevent concurrent edit conflicts
  - Two database migrations for lock version, one follow-up migration to fix DB-level defaults

- **Calculation engine (`duffel_payments`):**
  - `Payments.ProfitShare.Orchestrator` — Core orchestration module that runs profit share calculations for a given plan and time period
  - `Payments.ProfitShare.Calculation` — Individual calculation logic per actual
  - `Payments.ProfitShare.ConvertedCalculation` — FX normalization layer that replaced the previous `base_currency_mismatch` skip behaviour, enabling profit share to work across multi-currency actuals instead of silently skipping mismatched currencies
  - `Payments.ProfitShare.Summary` — Run result formatting, including out-of-scope actuals counts and expandable detail panels
  - `Payments.ProfitShare.RunInputs` — Input parameter handling for orchestrator runs
  - `Payments.ProfitShare.Orchestrator.Behaviour` — Behaviour definition for testability/mockability

- **Admin UI in Jet (Phoenix LiveView):**
  - `PlanIndex` — Index page listing all profit share plans with organisation names
  - `PlanShow` — Detail page showing plan configuration, associated rules, run history, and a plan-level run button with expandable result panels
  - `PlanNew` — Form for creating new profit share plans
  - `RuleShow` — Detail page for individual rules with tier thresholds formatted as percentages
  - `RuleEdit` — Edit form for modifying rules with optimistic locking conflict detection
  - `RuleNew` — Form for creating new rules
  - `PayoutsShow` — Payout detail page showing approval details and user-supplied paid_at dates
  - `ProfitShareView` — Shared view helpers for formatting thresholds, dates, run summaries, and out-of-scope counts
  - `RateHelpers` — Helper module for percentage/rate formatting

- **CLI operational tooling:**
  - `RunPlan` task — CLI command to trigger profit share runs for a specific month/year, enabling ops to run calculations without going through the UI
  - `UpdatePlanEffectiveFrom` task — CLI command to update a plan's effective_from date
  - `DeleteDraftsAndVoided` task — CLI command to clean up draft and voided customer payouts

- **Key design decisions:**
  - Allowed non-month-aligned effective dates on plans and rules (required updating validation logic across 16 files, removing previous month-boundary constraints)
  - Replaced the opaque run banner with an expandable result panel showing detailed calculation breakdowns
  - Used Ecto's built-in optimistic locking (`Ecto.Changeset.optimistic_lock/3`) rather than manual version checking
  - Surfaced out-of-scope actuals count in run output so operators can see how many actuals were excluded and why

**Commits (chronological):**

| Date | Commit | PR | Files | +/- | Description |
|------|--------|-----|-------|-----|-------------|
| May 12 | `0cbe8c2` | #27047 | 16 | +519/-2 | Add profit-share read surfaces in Jet — initial index, show, and rule show pages with rate helpers |
| May 14 | `c5e2340` | #27067 | 30 | +2,151/-34 | Add profit-share write surfaces in Jet — plan/rule new/edit forms, authorization, changeset helpers |
| May 14 | `10c0b8c` | #27138 | 1 | +33 | Add lock_version column to profit share plans and rules tables (migration) |
| May 15 | `8d78995` | #27141 | 12 | +191/-37 | Add optimistic locking to profit share plan and rule updates |
| May 15 | `41aef61` | #27151 | 1 | +23 | Drop DB-level default on profit share lock_version columns (migration) |
| May 15 | `f622f80` | #27152 | 3 | +37/-1 | Add organisation name to profitshare index view |
| May 20 | `d73858c` | #27225 | 16 | +1,021/-11 | Add plan-level run button to profit share plan show page |
| May 28 | `7fe9616` | #27401 | 4 | +51/-5 | Format tier thresholds as percentages on rule show page |
| Jun 2 | `80da12a` | #27384 | 9 | +422/-13 | Add Jet task to run profit-share plans for a specific month/year |
| Jun 9 | `5601ce1` | #27616 | 16 | +619/-87 | Allow non-month-aligned effective dates on profit-share plans and rules |
| Jun 9 | `28d2a7c` | #27634 | 1 | +13 | Add payments team ownership for payments paths in CLI and Jet (CODEOWNERS) |
| Jun 9 | `652c812` | #27643 | 1 | +47 | Remove stale month-boundary constraints from profit-share column comments (migration) |
| Jun 9 | `62bd526` | #27650 | 7 | +908/-13 | Replace opaque run banner with expandable result panel |
| Jun 10 | `cc2225a` | #27682 | 9 | +348/-79 | Replace base_currency_mismatch skip with FX normalization in profit-share calculation |
| Jun 11 | `5f03e37` | #27718 | 3 | +201 | Add CLI task to update profit share plan effective_from date |
| Jun 12 | `a48110c` | #27730 | 13 | +251/-35 | Surface out-of-scope actuals count in profit share run output |

---

### 2. Commission Estimates Pipeline

**Scope:** 6 commits, ~1,511 lines added
**Apps touched:** `duffel_payments`, `duffel_core`, `duffel_stays`
**Date range:** April 13 – April 20, 2026

Harvey designed and built the supplier commission estimates system from scratch. This system enables Duffel to ingest, store, and query commission forecasts from suppliers, establishing the data foundation for financial planning and reconciliation.

**What was built:**

- **Database schema:** Created the `payments_supplier_commission_estimates` table with a migration that defines the full column set for storing estimate records.

- **Cross-vertical contract pattern:**
  - `EstimateParams` — A typed struct defining the parameters that any vertical (air, stays, etc.) must provide when publishing a commission estimate
  - `EstimatePublisher` — A publisher module that receives estimate params and dispatches them to the ingestion pipeline
  - `EstimateRecordBehaviour` — A behaviour that verticals implement to define how their domain-specific records map to the common estimate format
  - `EstimateRecordNotConfigured` — A fallback module that raises a clear error when a vertical hasn't configured its estimate record mapping
  - This contract pattern was initially built in `duffel_payments` and then migrated to `duffel_core` so that any vertical app could use it without depending on payments

- **Ingestion pipeline (Oban background jobs):**
  - `IngestSupplierCommissionEstimates` — An Oban worker that receives batches of raw estimate data and fans out individual creation jobs
  - `CreateSupplierCommissionEstimate` — An Oban worker that processes individual estimates, handling upsert logic with conflict resolution so that re-ingesting the same data is idempotent

- **Estimate handler:** `EstimateHandler` — The core module that coordinates estimate creation, handling validation, deduplication, and database insertion with conflict-aware upsert strategies.

**Commits (chronological):**

| Date | Commit | PR | Files | +/- | Description |
|------|--------|-----|-------|-----|-------------|
| Apr 13 | `88efa02` | #26501 | 1 | +85 | Add `payments_supplier_commission_estimates` table (migration) |
| Apr 14 | `b0fe07a` | #26502 | 6 | +333 | Add estimate commission contract between verticals and payments |
| Apr 15 | `473e5fe` | #26551 | 15 | +879/-9 | Add commission estimate schema and ingestion workers (PAY-1966) |
| Apr 20 | `a72d445` | #26615 | 15 | +79/-53 | Migrate commission estimate contract to `duffel_core` |
| Apr 20 | `7a26390` | #26633 | 6 | +135/-66 | Upsert commission estimates and move publisher to duffel_core |

---

### 3. Conferma Virtual Card Reconciliation Platform

**Scope:** ~10 commits, ~2,722 lines added
**Apps touched:** `duffel_jet`, `duffel_treasury`, `duffel_payments`, `duffel_air`, `duffel_web`
**Date range:** February 25 – April 13, 2026

Harvey built the complete Conferma virtual card reconciliation workflow in Jet, from initial sidebar navigation through to case management with comments, manual reconciliation actions, and revenue discrepancy creation.

**What was built:**

- **Jet index page** (`SupplierOpenCases.Index`) — A LiveView page listing all Conferma open reconciliation cases, with total case count display and navigation. Includes shared helper module for common case formatting logic.

- **Jet show page** (`SupplierOpenCases.Show`) — A detailed case view with:
  - Comment thread for case-level discussion
  - Refresh button to re-sync case data from Conferma
  - Manual reconciliation action to mark cases as manually reconciled
  - Revenue discrepancy creation button that links to the existing discrepancy workflow

- **Auto-resolution of card IDs** — Updated the manual refund flow (spanning `duffel_air`, `duffel_jet`, and `duffel_web`) to automatically resolve the Conferma card ID when creating a manual refund, rather than requiring operators to look it up and enter it manually. This touched the `OrderManualOperation` schema, the `ManualRefund` context, and the LiveView create form.

- **Discrepancy fix** — Fixed a bug in discrepancy creation for Conferma cards that was causing errors. This required changes across 11 files including the revenue discrepancy LiveView, input helpers, and view modules.

- **Upload/reconciliation decoupling** — Separated the file upload flow from the reconciliation trigger on upload pages for ARC, BSP, Enett, and Conferma. Previously, uploading a file would automatically trigger reconciliation; this change made them independent actions so operators could upload without immediately reconciling.

- **Sidebar navigation** — Added Conferma-specific navigation items to the Jet sidebar layout and an Open Cases button for quick access.

**Commits (chronological):**

| Date | Commit | PR | Files | +/- | Description |
|------|--------|-----|-------|-----|-------------|
| Feb 25 | `e9c57f1` | — | 3 | +8/-2 | Add Conferma upload option to sidebar, update reconciliation status message |
| Feb 26 | `9669f22` | #25737 | 3 | +68/-20 | Added total cases count to open cases view |
| Mar 16 | `abca3e3` | #25970 | 11 | +416/-12 | Fix discrepancy creation for Conferma cards |
| Mar 19 | `ae0ab4c` | #26047 | 11 | +524/-197 | Auto-resolve Conferma card ID for manual refunds |
| Mar 30 | `32e7099` | #26236 | 7 | +744 | Add Conferma Jet index page |
| Mar 31 | `514257b` | #26274 | 7 | +561/-6 | Add SupplierOpenCases show page for Conferma open cases (PAY-1949) |
| Mar 31 | `7a2d950` | #26275 | 3 | +177/-3 | Add comments to Conferma SupplierOpenCases show page |
| Apr 2 | `3975e23` | #26353 | 3 | +462/-3 | Add refresh and manual reconciliation to Conferma open cases |
| Apr 8 | `1ba4245` | #26427 | 6 | +216/-2 | Add revenue discrepancy button to Conferma open cases show page |
| Apr 8 | `9b41387` | #26429 | 1 | +1 | Add Conferma Open Cases button to Jet sidebar |
| Apr 13 | `ebef243` | #26461 | 14 | +219/-131 | Decouple file upload from reconciliation on upload pages |

---

### 4. Stays Commission Actuals — CSV Ingestion Pipelines

**Scope:** ~8 commits, ~775 lines added
**Apps touched:** `duffel_stays`, `duffel_core`, `duffel_payments`, `duffel_treasury`
**Date range:** May 5 – June 16, 2026

Harvey built and hardened CSV import pipelines for three different supplier commission formats used in the stays (hotel) vertical. This work involved parsing real-world supplier data with all its inconsistencies and edge cases.

**What was built:**

- **Booking.com CSV parser hardening:**
  - Case-insensitive parsing of the `is_eligible` field — Booking.com sends this as both "True"/"False" and "TRUE"/"FALSE" inconsistently, so the parser was updated to handle any casing
  - Truncated datetime handling — Some Booking.com exports include datetimes with a `T` separator but truncated time components; added parsing logic to handle this format
  - UTF-8 BOM stripping — Windows-generated CSV files from Booking.com include a UTF-8 Byte Order Mark at the start of the file, which was causing the first column header to fail matching; added BOM detection and stripping across all three CSV parsers

- **Onyx CSV parser:**
  - Renamed `PAYMENTDATE` column mapping to `AGENTPAYMENTDATE` to match the actual column header in Onyx exports (the column was renamed on the Onyx side)
  - Made the `state` field optional in the CSV import, since not all Onyx exports include this column
  - Created a database migration to make the `state` column nullable in the underlying table

- **Priceline CSV parser:**
  - Fixed an off-by-one month bug in `commission_invoice_received_at` date calculation — the invoice received date was being set to the wrong month, which affected reconciliation matching

- **Commission actuals data model enhancement:**
  - Added `product` and `commissionable_amounts` fields to the stays commission actuals data model, spanning the `ActualParams` struct in `duffel_core`, the `Entry` schema in `duffel_payments`, and all three CSV parsers. This enabled more detailed financial reporting on commission data.

**Commits (chronological):**

| Date | Commit | PR | Files | +/- | Description |
|------|--------|-----|-------|-----|-------------|
| May 5 | `698869d` | #26864 | 13 | +561/-72 | Add product and commissionable amounts to stays commission actuals |
| May 6 | `8f0566b` | #26924 | 3 | +66/-43 | Parse Booking.com `is_eligible` case-insensitively |
| May 18 | `49f8806` | #27157 | 3 | +3/-3 | Strip UTF-8 BOM from commission CSV imports |
| May 18 | `8454d08` | #27177 | 2 | +18/-4 | Handle truncated T-separated datetime in Booking.com CSV import |
| May 20 | `e75c6a0` | #27239 | 4 | +8/-8 | Rename `PAYMENTDATE` to `AGENTPAYMENTDATE` in Onyx CSV parser |
| Jun 3 | `f91847f` | #27512 | 1 | +11 | Make onyx state field nullable (migration) |
| Jun 3 | `895e978` | #27511 | 2 | +48/-1 | Make state optional in Onyx commission actuals CSV import |
| Jun 16 | `4f1e88a` | #27796 | 3 | +53/-2 | Fix Priceline commission_invoice_received_at off by one month |

---

### 5. Tokenised Cards Mode Migration

**Scope:** 4 commits, ~452 lines added
**Apps touched:** `duffel_identity`, `duffel_core`, `duffel_cli`, `duffel_web`, `duffel_stays`, `duffel_jet`, `duffel_seed`, `duffel_white_label`
**Date range:** March 6 – March 9, 2026

Harvey managed the complete lifecycle of a schema migration, demonstrating proper zero-downtime migration practices: adding the new column, backfilling existing data, deploying the code change, and finally dropping the legacy column.

**What was built:**

- **Migration 1 — Add new column:** Added `tokenised_cards_mode` column to `identity_organisations` table. This is a more flexible replacement for the previous boolean `tokenised_cards_enabled` flag, supporting multiple card processing modes (e.g., enabled, disabled, passthrough).

- **Backfill task:** Created `BackfillTokenisedCardsMode` CLI task that reads all existing organisations with `tokenised_cards_enabled = true` and sets their `tokenised_cards_mode` to the appropriate value. This was deployed and run before the schema change to ensure data consistency.

- **Card passthrough test mode support:** Updated the `Organisation` schema to use the new `tokenised_cards_mode` field across the codebase. This required touching 21 files across 9 apps:
  - `duffel_identity` — Updated the organisation schema, factory, and query modules
  - `duffel_web` — Updated 3D Secure session and vault card controllers to check the new field
  - `duffel_stays` — Updated organisation profile logic
  - `duffel_jet` — Updated the organisation show template
  - `duffel_seed` — Updated seed data
  - `config/config.exs` — Configuration updates

- **Migration 2 — Drop legacy column:** After confirming the backfill was complete and all code was using the new column, safely dropped the legacy `tokenised_cards_enabled` column.

**Commits (chronological):**

| Date | Commit | PR | Files | +/- | Description |
|------|--------|-----|-------|-----|-------------|
| Mar 6 | `fdc151e` | #25832 | 1 | +21 | Add `tokenised_cards_mode` column to `identity_organisations` table |
| Mar 6 | `17a132b` | #25850 | 4 | +82 | Add `BackfillTokenisedCardsMode` task |
| Mar 6 | `1985757` | #25833 | 21 | +334/-146 | Add card passthrough test mode support |
| Mar 9 | `555e36e` | #25834 | 1 | +15 | Drop `tokenised_cards_enabled` column from `identity_organisations` |

---

### 6. Payout Management and Reconciliation Improvements

**Scope:** ~7 commits, ~921 lines added
**Apps touched:** `duffel_payments`, `duffel_jet`, `duffel_treasury`, `duffel_web`, `duffel_cli`
**Date range:** April 22 – June 22, 2026

Harvey made several improvements to the payout management workflow and reconciliation system, fixing bugs, adding operational features, and improving data accuracy.

**What was built:**

- **User-supplied `paid_at` dates:** Updated the payout "mark as paid" flow to accept a user-supplied `paid_at` date rather than defaulting to the current time. This enables backdating payouts to their actual payment date. Changed the `MarkPaid` module, the payouts context, the LiveView show page, and the admin UI template.

- **UTC timestamp fix:** Fixed a bug where `paid_at` was being stored as midnight (00:00:00 UTC) on the payment date, rather than the actual UTC timestamp of when the payment was recorded. This affected downstream reporting that relied on precise timestamps.

- **Payout approval details:** Added approval information (approver, approval date) to the payout show page in Jet, so operators can see who approved a payout and when.

- **Draft payout timeout fix:** Fixed a timeout issue when creating draft payouts for organisations with large numbers of commission actuals. The previous query was scanning too many rows; the fix optimised the query to avoid the timeout. This was a production bug affecting specific high-volume organisations.

- **Delete draft/voided payouts CLI task:** Built a CLI task to delete draft and voided customer payouts, enabling operators to clean up payouts that were created in error or are no longer needed.

- **Reconciliation improvements:**
  - Excluded unconfirmed manual refunds from Enett, ARC, and BSP reconciliation calculations. Previously, manual refunds that hadn't been confirmed were being included in reconciliation totals, skewing the numbers.
  - Kept unconfirmed manual refunds visible on the Jet reconciliation cases page (they should be visible for tracking but not included in calculations).
  - Consolidated reconciliation transaction permissions into a single permission, simplifying the authorization model.
  - Mapped payment intent cancel errors to typed responses in the public API, replacing generic 500 errors with structured error responses that clients can handle programmatically.

**Commits (chronological):**

| Date | Commit | PR | Files | +/- | Description |
|------|--------|-----|-------|-----|-------------|
| Apr 22 | `6235b06` | #26668 | 11 | +199/-12 | Exclude unconfirmed manual refunds from Enett, ARC, and BSP reconciliation |
| Apr 24 | `10a9a66` | #26703 | 6 | +166/-10 | Keep unconfirmed manual refunds visible on Jet reconciliation cases |
| Apr 29 | `d09cb12` | #26794 | 3 | +100/-1 | Map payment intent cancel errors to typed responses |
| May 5 | `42d443a` | #26865 | 3 | +16/-27 | Consolidate reconciliation transaction permissions into a single permission |
| May 28 | `f92d405` | #27406 | 8 | +169/-42 | Accept a user-supplied paid_at date when marking payouts paid |
| Jun 3 | `4eaa3ba` | #27445 | 9 | +100/-43 | Store `paid_at` as the actual UTC timestamp instead of midnight |
| Jun 5 | `52d7dfe` | #27436 | 4 | +149 | Show approval details on payout show page |
| Jun 15 | `cefbf27` | #27759 | 2 | +137/-51 | Fix draft payout creation timeout for orgs with large actual counts |
| Jun 22 | `51bf517` | #27844 | 3 | +326 | Add CLI task to delete draft and voided customer payouts |

---

### 7. Other Notable Work

**CLI task for clearing supplier commission payouts:**
- Built a CLI task (`ClearSupplierCommissionPayouts`) to delete stays raw commission payouts for a specific supplier, enabling data corrections when a supplier re-sends corrected commission data
- 2 commits: `f7ea5ef` (#26947, May 7) and `858e10f` (#26962, May 7)

**VAPID key generation and local dev secrets:**
- Added a script to generate VAPID keys for web push notifications
- Set up `.env.local` pattern for local development secrets (added to `.gitignore`)
- Updated `scripts/setup` and `config/runtime.exs`
- 1 commit: `e754441` (#26300, Apr 2), 6 files changed

**Payments team CODEOWNERS:**
- Added payments team ownership for payments-related paths in CLI and Jet to the GitHub CODEOWNERS file
- 1 commit: `28d2a7c` (#27634, Jun 9)

---

## Complete Commit Log (Chronological)

Every non-merge commit by Harvey Singh, with date, hash, PR number, file count, lines changed, and description:

### February 2026 (2 commits)

1. **Feb 25** — `e9c57f1` — 3 files, +8/-2
   Add Conferma upload option to sidebar, and update reconciliation status message

2. **Feb 26** — `9669f22` — #25737 — 3 files, +68/-20
   Added total cases count to open cases view

### March 2026 (9 commits)

3. **Mar 6** — `fdc151e` — #25832 — 1 file, +21
   Add `tokenised_cards_mode` column to `identity_organisations` table

4. **Mar 6** — `17a132b` — #25850 — 4 files, +82
   Add `BackfillTokenisedCardsMode` task to update `tokenised_cards_mode` for enabled organisations

5. **Mar 6** — `1985757` — #25833 — 21 files, +334/-146
   Add card passthrough test mode support

6. **Mar 9** — `555e36e` — #25834 — 1 file, +15
   Drop `tokenised_cards_enabled` column from `identity_organisations`

7. **Mar 16** — `abca3e3` — #25970 — 11 files, +416/-12
   Fix discrepancy creation for conferma cards

8. **Mar 19** — `ae0ab4c` — #26047 — 11 files, +524/-197
   Auto-resolve Conferma card ID for manual refunds

9. **Mar 30** — `32e7099` — #26236 — 7 files, +744
   Add Conferma Jet index page

10. **Mar 31** — `514257b` — #26274 — 7 files, +561/-6
    PAY-1949: Add SupplierOpenCases show page for Conferma open cases

11. **Mar 31** — `7a2d950` — #26275 — 3 files, +177/-3
    Add comments to Conferma SupplierOpenCases show page

### April 2026 (13 commits)

12. **Apr 2** — `3975e23` — #26353 — 3 files, +462/-3
    Add refresh and manual reconciliation to Conferma open cases

13. **Apr 2** — `e754441` — #26300 — 6 files, +66/-1
    Add VAPID key generation and .env.local for local dev secrets

14. **Apr 8** — `1ba4245` — #26427 — 6 files, +216/-2
    Add revenue discrepancy button to Conferma open cases show page

15. **Apr 8** — `9b41387` — #26429 — 1 file, +1
    Add Conferma Open Cases button to Jet sidebar

16. **Apr 13** — `ebef243` — #26461 — 14 files, +219/-131
    Decouple file upload from reconciliation on upload pages

17. **Apr 13** — `88efa02` — #26501 — 1 file, +85
    Add `payments_supplier_commission_estimates` table

18. **Apr 14** — `b0fe07a` — #26502 — 6 files, +333
    Add estimate commission contract between verticals and payments

19. **Apr 15** — `473e5fe` — #26551 — 15 files, +879/-9
    [PAY-1966] Add commission estimate schema and ingestion workers

20. **Apr 20** — `a72d445` — #26615 — 15 files, +79/-53
    Migrate commission estimate contract to `duffel_core`

21. **Apr 20** — `7a26390` — #26633 — 6 files, +135/-66
    Upsert commission estimates and move publisher to duffel_core

22. **Apr 22** — `6235b06` — #26668 — 11 files, +199/-12
    Exclude unconfirmed manual refunds from Enett, ARC, and BSP reconciliation

23. **Apr 24** — `10a9a66` — #26703 — 6 files, +166/-10
    Keep unconfirmed manual refunds visible on Jet reconciliation cases

24. **Apr 29** — `d09cb12` — #26794 — 3 files, +100/-1
    Map payment intent cancel errors to typed responses

### May 2026 (17 commits)

25. **May 5** — `698869d` — #26864 — 13 files, +561/-72
    Add product and commissionable amounts to stays commission actuals

26. **May 5** — `42d443a` — #26865 — 3 files, +16/-27
    Consolidate reconciliation transaction permissions into a single permission

27. **May 6** — `8f0566b` — #26924 — 3 files, +66/-43
    Parse Booking.com `is_eligible` case-insensitively

28. **May 7** — `f7ea5ef` — #26947 — 3 files, +248/-1
    Add CLI task to clear stays raw commission payouts by supplier

29. **May 7** — `858e10f` — #26962 — 1 file, +1
    Add `ClearSupplierCommissionPayouts` to CLI task list

30. **May 12** — `0cbe8c2` — #27047 — 16 files, +519/-2
    Add profit-share read surfaces in Jet

31. **May 14** — `c5e2340` — #27067 — 30 files, +2,151/-34
    Add profit-share write surfaces in Jet

32. **May 14** — `10c0b8c` — #27138 — 1 file, +33
    Add lock_version column to profit share plans and rules tables

33. **May 15** — `8d78995` — #27141 — 12 files, +191/-37
    Add optimistic locking to profit share plan and rule updates

34. **May 15** — `41aef61` — #27151 — 1 file, +23
    Drop DB-level default on profit share lock_version columns

35. **May 15** — `f622f80` — #27152 — 3 files, +37/-1
    Add organisation name to profitshare index view

36. **May 18** — `49f8806` — #27157 — 3 files, +3/-3
    Strip UTF-8 BOM from commission CSV imports

37. **May 18** — `8454d08` — #27177 — 2 files, +18/-4
    Handle truncated T-separated datetime in Booking.com CSV import

38. **May 20** — `d73858c` — #27225 — 16 files, +1,021/-11
    Add plan-level run button to profit share plan show page

39. **May 20** — `e75c6a0` — #27239 — 4 files, +8/-8
    Rename `PAYMENTDATE` to `AGENTPAYMENTDATE` in Onyx CSV parser

40. **May 28** — `7fe9616` — #27401 — 4 files, +51/-5
    Format tier thresholds as percentages on rule show page

41. **May 28** — `f92d405` — #27406 — 8 files, +169/-42
    Accept a user-supplied paid_at date when marking payouts paid

### June 2026 (15 commits)

42. **Jun 2** — `80da12a` — #27384 — 9 files, +422/-13
    Add Jet task to run profit-share plans for a specific month/year

43. **Jun 3** — `4eaa3ba` — #27445 — 9 files, +100/-43
    Store `paid_at` as the actual UTC timestamp instead of midnight

44. **Jun 3** — `f91847f` — #27512 — 1 file, +11
    Make onyx state field nullable

45. **Jun 3** — `895e978` — #27511 — 2 files, +48/-1
    Make state optional in Onyx commission actuals CSV import

46. **Jun 5** — `52d7dfe` — #27436 — 4 files, +149
    Show approval details on payout show page

47. **Jun 9** — `5601ce1` — #27616 — 16 files, +619/-87
    Allow non-month-aligned effective dates on profit-share plans and rules

48. **Jun 9** — `28d2a7c` — #27634 — 1 file, +13
    Add payments team ownership for payments paths in CLI and Jet

49. **Jun 9** — `652c812` — #27643 — 1 file, +47
    Remove stale month-boundary constraints from profit-share column comments

50. **Jun 9** — `62bd526` — #27650 — 7 files, +908/-13
    Replace opaque run banner with expandable result panel

51. **Jun 10** — `cc2225a` — #27682 — 9 files, +348/-79
    Replace base_currency_mismatch skip with FX normalization in profit-share calculation

52. **Jun 11** — `5f03e37` — #27718 — 3 files, +201
    Add CLI task to update profit share plan effective_from date

53. **Jun 12** — `a48110c` — #27730 — 13 files, +251/-35
    Surface out-of-scope actuals count in profit share run output

54. **Jun 15** — `cefbf27` — #27759 — 2 files, +137/-51
    Fix draft payout creation timeout for orgs with large actual counts

55. **Jun 16** — `4f1e88a` — #27796 — 3 files, +53/-2
    Fix Priceline commission_invoice_received_at off by one month

56. **Jun 22** — `51bf517` — #27844 — 3 files, +326
    Add CLI task to delete draft and voided customer payouts

---

## Technical Skills Demonstrated

### Elixir / OTP

- **Ecto:** Schema design, changesets with custom validation, optimistic locking (`Ecto.Changeset.optimistic_lock/3`), database migrations with explicit `up`/`down`, multi-step migration lifecycle (add column, backfill, drop old column), nullable column handling, upsert strategies with conflict resolution
- **Phoenix LiveView:** Built 10+ LiveView pages for the Jet admin tool, including index/show/edit/new patterns, live form handling, expandable result panels, and approval workflow displays
- **Phoenix Controllers:** Updated API controllers for payment intent cancellation and manual refund creation with proper error mapping
- **Oban:** Background job workers for commission estimate ingestion with fan-out pattern (one dispatcher job creates many worker jobs)
- **Behaviours:** Defined and implemented `EstimateRecordBehaviour` for cross-vertical contract patterns
- **Testing:** Consistent test coverage across all work — unit tests, integration tests, and LiveView integration tests. Every feature commit includes corresponding test files.

### System Design

- **Cross-vertical architecture:** Designed the commission estimates contract pattern that allows any vertical (air, stays) to publish estimates through a common interface, with the contract living in `duffel_core` for shared access
- **Optimistic locking:** Implemented concurrent edit protection for profit share plans and rules, including proper conflict detection and user-facing error handling in the admin UI
- **FX normalization:** Replaced a system that silently skipped multi-currency actuals with proper FX conversion, ensuring all profit share calculations work regardless of the source currency
- **Schema lifecycle management:** Demonstrated the full add/backfill/drop migration pattern for the tokenised cards column, which is the correct zero-downtime approach for column renames/replacements

### Data Engineering

- **CSV parsing:** Built and maintained three distinct CSV parsers for supplier commission data (Booking.com, Onyx, Priceline), handling real-world data quality issues including encoding problems (UTF-8 BOM), inconsistent casing, truncated timestamps, column renames, and nullable fields
- **Data pipeline design:** Designed the commission estimate ingestion pipeline with idempotent upsert logic, enabling safe re-ingestion of data

### Financial/Payment Systems

- **Profit share calculations:** Built the complete profit share calculation engine with tiered pricing rules, percentage-based rates, FX normalization, and multi-currency support
- **Commission management:** Built commission estimate ingestion, supplier commission actuals parsing, and payout management workflows
- **Reconciliation:** Worked across Enett, ARC, BSP, and Conferma reconciliation systems, with particular depth in the Conferma virtual card reconciliation workflow
- **Payout workflows:** Implemented payout approval displays, user-supplied payment dates, UTC timestamp corrections, and draft payout lifecycle management

### Operational Tooling

- **CLI tasks:** Built 5 CLI tasks for operational use:
  - `RunPlan` — Trigger profit share runs for specific periods
  - `UpdatePlanEffectiveFrom` — Update plan effective dates
  - `DeleteDraftsAndVoided` — Clean up draft/voided payouts
  - `ClearSupplierCommissionPayouts` — Clear stays commission payouts by supplier
  - `BackfillTokenisedCardsMode` — Data migration task for tokenised cards
- **CODEOWNERS:** Configured team ownership for payments paths
- **Developer tooling:** VAPID key generation script, `.env.local` pattern for local secrets

---

## Suggested CV Bullets

### Headline

Backend engineer at Duffel (travel fintech platform), building payment and financial infrastructure for a platform that processes airline and hotel bookings across 300+ suppliers globally.

### Feature Ownership

- Designed and built Duffel's profit-share system end-to-end in Elixir/Phoenix: tiered pricing calculation engine with FX normalization across multiple currencies, Ecto schemas with optimistic locking, Phoenix LiveView admin interface (10+ pages), CLI operational tooling, and 5 database migrations — enabling automated revenue sharing with supplier partners
- Built the supplier commission estimates pipeline from scratch: defined a cross-vertical contract pattern using Elixir behaviours, implemented Oban background job ingestion with fan-out processing, and designed conflict-aware upsert logic for idempotent data ingestion
- Built a virtual card reconciliation platform in Phoenix LiveView for Conferma supplier cards: case management with comments, manual reconciliation workflows, automatic card ID resolution for refunds, and revenue discrepancy creation — spanning 5 umbrella apps in the monorepo
- Engineered CSV commission ingestion pipelines for three hotel suppliers (Booking.com, Onyx, Priceline), handling real-world data quality issues including UTF-8 BOM encoding, inconsistent field casing, truncated timestamps, and cross-format column mapping

### Architecture and Design

- Designed a cross-vertical contract pattern for commission estimates using Elixir behaviours, initially built in the payments app then migrated to the shared core library, enabling any vertical (air, stays) to publish estimates through a common interface
- Managed zero-downtime schema migrations in a high-availability Elixir/Phoenix deployment: executed a full add-column/backfill/drop-legacy lifecycle for the tokenised cards feature across 9 umbrella apps and 21 files
- Replaced a system that silently skipped multi-currency profit share actuals with proper FX normalization, converting all amounts to a base currency for accurate tiered calculations

### Impact and Scale

- Authored 14,600+ lines of Elixir across 9 umbrella apps in 4 months, with 7 production database migrations and 56 pull requests merged
- Built 5 CLI operational tasks that enabled the payments team to self-serve profit share runs, payout management, and commission data corrections without requiring engineering intervention
- Fixed a production timeout in draft payout creation for high-volume organisations by optimising the underlying Ecto query

### Technical Breadth

- Worked across the full Elixir umbrella stack: database migrations (Ecto), domain logic (GenServer, Oban), admin UI (Phoenix LiveView), public API controllers (Phoenix), CLI tooling, and infrastructure (CODEOWNERS, dev scripts)
- Contributed to 9 of 21 umbrella apps in the monorepo, with heaviest concentration in payments domain logic and admin UI

---

## Data Accuracy Note

This analysis covers exactly 56 non-merge commits authored between February 25 and June 22, 2026, using the git email `harvey@duffel.com`. Commits were authored under two display names: `harvey-duffel` (55 commits) and `Harvey Singh` (1 commit). Commits by "John Peter Harvey" (`john@duffel.com`), a separate contributor, were explicitly excluded from this analysis. All PR numbers reference the Duffel Platform repository's pull request history.
