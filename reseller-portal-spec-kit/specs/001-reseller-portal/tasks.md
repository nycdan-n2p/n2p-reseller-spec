---
description: "Task list for White Label Reseller Portal"
---

# Tasks: White Label Reseller Portal

**Input**: Design documents from `specs/001-reseller-portal/`

**Prerequisites**: plan.md (required), spec.md (user stories), research.md, data-model.md, contracts/

**Organization**: Grouped by user story so each is independently implementable, testable, and demoable.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: can run in parallel (different files/services, no dependency)
- **[Story]**: US1–US5 traceability
- Paths follow the `frontend/` + `backend/` structure in plan.md

---

## Phase 1: Setup (Shared Infrastructure)

- [ ] T001 Scaffold `frontend/` reseller console (shell, nav, topbar, command palette) from the approved prototype
- [ ] T002 Stand up `backend/` service skeleton over the existing platform (routing, auth wiring to the single auth server)
- [ ] T003 [P] Configure linting, formatting, CI, and a parity-audit check against the parity matrix

---

## Phase 2: Foundational (Blocking Prerequisites)

**⚠️ CRITICAL**: no user story can begin until this phase is complete.

- [ ] T004 Tenant model + tenant-scoped data access (single DB, no cross-tenant leakage) in `backend/src/tenant/`
- [ ] T005 [P] Theme engine: JSON theme config keyed to tenant, brand-default inheritance; loader in `frontend/src/shell/`
- [ ] T006 [P] Domain routing: CNAME → static host; pre-login DNS branding, post-login JWT/tenant resolution
- [ ] T007 Pod provisioning primitive (create account/seat/DID on a pod) in `backend/src/provisioning/`
- [ ] T008 [P] Impersonation ("view as customer") with banner + audit log in `backend/src/support/`
- [ ] T009 Error handling, logging, and role/permission (RBAC) framework
- [ ] T010 [NEEDS CLARIFICATION] Multi-tenant pod spike — per-reseller config vs. versioned standard pod

**Checkpoint**: foundation ready — user stories can proceed.

---

## Phase 3: User Story 1 — Onboard & provision a net-new customer (P1) 🎯 MVP

**Goal**: empty reseller → branded customer making/receiving calls, guided, ~20 min.

**Independent Test**: brand setup → new-customer wizard → seat + DID + device → two-way call, no support, no net2phone visible.

- [ ] T011 [P] [US1] Brand settings UI (logo, color, product name, domain) writing tenant theme — `frontend/src/views/settings`
- [ ] T012 [US1] New-customer wizard (account → pod → seats → DID) — `frontend/src/views/customers` + `backend/src/provisioning`
- [ ] T013 [P] [US1] Customer blueprints/clone-from-existing template
- [ ] T014 [US1] Device registration + zero-touch provisioning hook — `backend/src/inventory`
- [ ] T015 [US1] Place/receive call path verified; call appears in customer CDR
- [ ] T016 [US1] Dashboard: revenue/prepaid summary, expandable customer rows, day-one setup meter

**Checkpoint**: US1 fully functional — the soft-launch MVP milestone.

---

## Phase 4: User Story 2 — Administer a customer's phone system (P1, parity)

**Goal**: full per-customer PBX admin as a drill-down (extensions, attendants, queues, schedules, MOH, conferences, call history).

**Independent Test**: create extension+device+permission, build attendant on a schedule, add ring group, one-action holiday schedule, verify in CDR.

- [ ] T017 [US2] Customer-detail drill-down shell (from customer row / view-as) — `frontend/src/views/customer-detail`
- [ ] T018 [P] [US2] Extensions/users: site, department, dial plan, calling permission, device, BLF — `backend/src/telephony`
- [ ] T019 [P] [US2] Auto attendants (visual menu builder, schedule, greeting)
- [ ] T020 [P] [US2] Call queues / ring groups (ring strategies, overflow targets)
- [ ] T021 [P] [US2] Time-frame schedules + one-action holiday push to all attendants/queues
- [ ] T022 [P] [US2] Music on hold (playlist + messages, brand-default inheritance)
- [ ] T023 [P] [US2] Conferences (audio bridges)
- [ ] T024 [US2] Per-customer call history / CDR (search, filter, export)
- [ ] T025 [P] [US2] Per-customer feature toggles (short list) + Numbers/DID panel

**Checkpoint**: parity "Manage" surface complete — no open Gap rows for telephony.

---

## Phase 5: User Story 3 — Wholesale billing, pooled minutes, usage by customer (P1)

**Goal**: aggregate wholesale statement + shared minute pool + tax filed + usage by service by customer.

**Independent Test**: generate statement (MRC + metered + taxes); confirm pool draw (no per-seat limit); reconcile a customer's minutes to CDRs.

- [ ] T026 [US3] Billing engine: a-la-carte (seats/DID/E911/minutes) + subscription fees + minimum commitments — `backend/src/billing`
- [ ] T027 [US3] Shared minute pool + overage; remove any per-seat credit-limit concept
- [ ] T028 [US3] Arrears MRC to reseller; reseller-bills-in-advance model; prepaid balance / PRC-style
- [ ] T029 [US3] [NEEDS CLARIFICATION] Tax engine integration (CCH vs ShoreTax/SureTax/Rev.io) + service-address tax data
- [ ] T030 [P] [US3] Usage by service, by customer (minutes/SMS/MMS/AI/DIDs) + adjustment/usage file export
- [ ] T031 [P] [US3] Billing UI: minute pool, usage-by-customer, 30-day seat tracker, dunning under reseller policy

**Checkpoint**: reseller can invoice and reconcile; margins visible.

---

## Phase 6: User Story 4 — Fraud, spend caps, E911 & 10DLC compliance (P2)

**Goal**: protect every account; assist compliance.

**Independent Test**: trip an intl-velocity rule → auto-throttle at cap; flag+resolve a device missing E911; submit 10DLC and see status.

- [ ] T032 [US4] Fraud detection rules + per-account velocity/spend caps + auto-throttle/freeze — `backend/src/risk`
- [ ] T033 [P] [US4] Risk UI: events, per-account limits, "fix it for me" remediation
- [ ] T034 [P] [US4] E911 compliance (dispatchable location, notify contact) — `backend/src/inventory` + Compliance UI
- [ ] T035 [P] [US4] 10DLC registration status + guided remediation; STIR/SHAKEN attestation reporting

**Checkpoint**: 100% of accounts capped; compliance surfaces live.

---

## Phase 7: User Story 5 — Migrate a customer from a competitor (P2, fast-follow)

**Goal**: import from competitor export/CSV, map, dry-run, cutover with rollback.

**Independent Test**: import sample export → clear conflicts in dry-run → scheduled cutover with rollback.

- [ ] T036 [US5] Import: CSV + competitor export parsers (NetSapiens first) + field mapping — `backend/src/migration`
- [ ] T037 [US5] Dry-run/conflict report; confirm-to-apply
- [ ] T038 [US5] Cutover scheduling + rollback; migration project tracker in Pipeline UI

**Checkpoint**: a real flip can be executed end to end.

---

## Phase 8: Polish & Cross-Cutting

- [ ] T039 [P] Support: portal ticketing → Tier-3 escalation, Salesforce sync, CS-tool account visibility
- [ ] T040 [P] Grey-label audit: no net2phone visible on any end-customer surface
- [ ] T041 [P] Reports (margin, seat utilization, AI/uptime, benchmarks) + scheduled reports
- [ ] T042 Accessibility/contrast for reseller brand colors; dark mode
- [ ] T043 Run quickstart.md validation and the full parity audit

---

## Phase 9: Telco Platform Layer & Extended Offer

**Split**: [Portal] = build a reseller-facing screen · [Platform] = network-layer capability behind the offer (portal shows status/config only).

- [ ] T044 [Platform] SBC least-cost routing (LCR) + call rating engine; portal surfaces routing profiles + rate/tax status — `backend/src/billing` + Infrastructure "Carrier & routing" tab
- [ ] T045 [Platform] Carrier integration/sync (connections, number order/port sync, toll-free RESPORG) — carrier-connections/status panel
- [ ] T046 [Portal] Robocall Mitigation Database (RMD) status card in Compliance (platform files); pair with STIR/SHAKEN
- [ ] T047 [Portal] Call recording management + transcription + sentiment analysis, per customer, with retention (grey-labeled storage)
- [ ] T048 [Portal] Fax: eFax portal + ATA (analog adapter) device provisioning in Inventory
- [ ] T049 [Portal] Store: reseller storefront for hardware/add-ons, optional customer-facing self-serve
- [ ] T050 [Portal, roadmap] Microsoft Teams / TeamMate integration tile + connect flow

**Checkpoint**: extended-offer surfaces present; platform-layer items have a status/config surface even where the reseller does not operate them directly.

## Roadmap (post-MVP, separate spec iterations)

- [ ] R01 [NEEDS CLARIFICATION] AI agents + Integrate (hard launch ~Q3) — templates, deploy per customer, billable API tasks
- [ ] R02 [NEEDS CLARIFICATION] SIP trunking to premise PBXs (fast-follow / not first release)
- [ ] R03 Automations / Integrate as a billable platform (no-code workflows)
- [ ] R04 Reseller mobile-management app (grey-label; full private app = premium fee)
- [ ] R05 Hardware marketplace (day-2); COACH (day-3)

---

## Dependencies & Execution Order

- **Setup (P1)** → **Foundational (P2, blocks all)** → **User Stories (P3+)** → **Polish**.
- US1 is the MVP; US2 and US3 are P1 and run alongside/after US1 (billing must be near-concurrent).
- US4 and US5 depend only on Foundational; can run in parallel with staffing.
- Resolve `[NEEDS CLARIFICATION]` (AI timing, tax engine, multi-tenant pods, front-end framework) in
  `research.md` before the affected tasks (T010, T029, R01, R02).

## Notes

- Independent, testable slices — stop at any checkpoint to demo.
- `[NEEDS CLARIFICATION]` items are the open decisions from the Scoping Framework §11/§15; do not
  hard-commit those tasks until resolved.
- Guard the single team from the competing S3 priorities (CyberSource recode, Argentina/Navigator EOL).
