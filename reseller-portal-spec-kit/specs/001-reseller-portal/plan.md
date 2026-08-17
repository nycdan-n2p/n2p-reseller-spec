# Implementation Plan: White Label Reseller Portal

**Branch**: `001-reseller-portal` | **Date**: 2026-08-11 | **Spec**: ./spec.md

**Input**: Feature specification from `specs/001-reseller-portal/spec.md`

## Summary

Deliver a single white-label reseller console (Sell · Deliver · Manage · Invoice) that lets a reseller
provision and brand net-new customers, administer each customer's phone system as a drill-down, bill
wholesale with pooled minutes and filed taxes, and protect accounts (fraud, E911, 10DLC) — with
net2phone grey-labeled behind the curtain. Net-new UCaaS + CCE is the soft-launch MVP; migration, SIP,
and AI + Integrate are fast-follow/hard-launch. Back end reuses PAM/partner-portal/CS-tool stack
knowledge (s3 team); the UI is a new product surface; theming is a tenant-keyed JSON config.

## Technical Context

**Language/Version**: Front end — HTML/CSS/JS SPA (prototype is vanilla + Tabler icons; production
framework NEEDS CLARIFICATION). Back end — existing net2phone stack (PAM/partner-portal/CS-tool) with
service APIs; .NET (Pods team) + Java (Platform team) services.

**Primary Dependencies**: Unite (UCaaS), CCE (contact center, inside Unite), Integrate (integrations +
AI agent, hard launch), Salesforce (ticketing sync + account visibility), a tax engine (CCH today;
ShoreTax/SureTax/Rev.io evaluated), Bandwidth and other carriers (numbers/porting), Stripe/ConnectWise/
QuickBooks (payments/billing sync).

**Storage**: Single shared database, tenant-scoped (single auth server, single DB — misconfiguration
may misbrand but MUST NOT leak data). Theme config stored as JSON keyed to tenant. Pods host customer
accounts (multi-tenant pod model under evaluation).

**Testing**: Contract tests for provisioning/billing/usage APIs; integration tests per user story
(provision→call, holiday-schedule→all-queues, import→dry-run→cutover, fraud-cap→throttle); parity
audit against the parity matrix.

**Target Platform**: Web (desktop-first reseller console) + grey-labeled end-customer apps (web,
mobile, Huddle video). Reseller domains via CNAME to static backend hosts.

**Project Type**: Web application (frontend reseller console + backend services over the existing
platform).

**Performance/Reliability Goals**: Platform SLA 99.999%+; provisioning a customer ≈ 20 min guided;
fraud auto-throttle in near-real-time at configured caps.

**Constraints**: Grey-label — no net2phone visible to end customers; wholesale — costs recovered, not
absorbed (platform/cert fee); pooled minutes (no per-seat credit limit); theming tenant-keyed, not
domain-keyed.

**Scale/Scope**: Reseller managing tens of customers and 1,000s of seats; program targets large
resellers (150–250 seat trials → 3–5k endpoint migrations over ~6 months). MVP soft launch in ~2
quarters (aggressive).

## Constitution Check

*GATE: must pass before design; re-check after Phase 1.*

| Principle | Gate | Status |
|---|---|---|
| P1 Reseller is the customer | Phone-system admin is a per-customer drill-down, not a top-level app | PASS (prototype) |
| P2 Parity is table stakes | Parity matrix has no open "Gap" at MVP scope (Inventory/E911, import/export closed) | IN PROGRESS |
| P3 White-label by default | No net2phone visible to end customers in grey-label | PASS by design |
| P4 Wholesale economics | Pooled minutes, no per-seat credit limit; cert/platform fee recovers cost | PASS (billing spec) |
| P5 Safety & compliance | Fraud caps on all accounts; E911/10DLC surfaces; view-as logged | PASS (spec) |
| P6 Migration-first, net-new leads | Import primitive is core; launch builds net-new first | PASS (sequencing) |
| P7 AI-native & transparent | Assistant acts, explains, asks, records; inherits defaults | PASS (prototype) |
| P8 One team, one surface | Single team/DSM/PO; one nav; drill-down not a second app | PASS |

**Open gates (tracked as [NEEDS CLARIFICATION])**: AI-in-MVP vs Q3; SIP first-release vs fast-follow;
tax engine build vs partner; multi-tenant pod model; production front-end framework.

## Project Structure

### Documentation (this feature)

```text
specs/001-reseller-portal/
├── spec.md              # Feature spec (what/why)
├── plan.md              # This file
├── tasks.md             # Task list (by user story)
├── research.md          # Phase 0 — resolve [NEEDS CLARIFICATION] (AI timing, tax, pods, framework)
├── data-model.md        # Phase 1 — entities from spec
├── quickstart.md        # Phase 1 — "provision first customer" validation script
└── contracts/           # Phase 1 — provisioning / billing / usage API contracts
```

### Source Code (repository root)

```text
frontend/                      # reseller console (the prototype → productionized)
├── src/
│   ├── shell/                 # nav, topbar, command palette, theming loader (reads tenant JSON)
│   ├── views/                 # dashboard, pipeline, customers, customer-detail (phone system),
│   │                          #   demo-lab, ai-agents, automations, infrastructure, billing,
│   │                          #   compliance, risk, reports, settings, help
│   ├── components/            # tables, cards, alerts, drill-down, expandable rows
│   └── services/              # API clients (provisioning, billing, usage, tenant/theme)
└── tests/

backend/                       # services over the existing platform (s3 + pods + platform teams)
├── src/
│   ├── tenant/                # tenant/theme config (JSON keyed to tenant), inheritance
│   ├── provisioning/          # account/seat/DID creation on a pod; wizard/blueprints
│   ├── telephony/             # extensions, attendants, queues, schedules, MOH, conferences, CDR
│   ├── inventory/             # DIDs/CNAM, SMS/10DLC, hardware, E911
│   ├── billing/               # a-la-carte + pooled minutes + arrears MRC + tax + adjustment file
│   ├── risk/                  # fraud velocity/spend caps, throttle/freeze
│   ├── migration/             # import (CSV/competitor), map, dry-run, cutover, rollback
│   ├── support/               # ticketing, Tier-3 escalation, Salesforce sync, impersonation log
│   └── integrations/          # Integrate (billable API tasks), marketplace apps, webhooks
└── tests/
```

**Structure Decision**: Web application (frontend + backend). The reseller console is a new UI product;
back-end capabilities are services over the existing net2phone platform (PAM/partner-portal/CS-tool).
Per-customer telephony is a drill-down view under `customers/{id}`, not a separate app.

## Architecture Decisions (locked)

- **Theme = JSON config keyed to tenant, not domain** — shared across net2phone and resellers
  (net2phone is itself a tenant) to avoid code sprawl; web app + settings first, then other apps.
- **Pre-login by DNS, post-login by JWT/tenant** — domains via CNAME to a static backend host; certs
  regenerate on domain change (cost recovered via platform fee).
- **Single auth server + single database, tenant-scoped** — misconfiguration may misbrand but must
  never leak another tenant's data.
- **Call traces / CDR extracted by a service** and surfaced in the portal per customer.
- **No prepackaged bundles** for the US market — soft limits + app access; resellers price their own
  SKUs/packages.

## Complexity Tracking

| Violation / Complexity | Why Needed | Simpler Alternative Rejected Because |
|---|---|---|
| Multi-tenant pods | Efficiency at reseller scale; run many resellers/customers per pod | Single-tenant-per-pod does not scale to the program's target volumes; spike in progress |
| Tax engine integration | Resellers rely on us for tax data / filing to avoid 50-state registration | Sending raw CDRs without tax data fails the "lowers friction" value; market expects a tax engine |
| Billing-on-behalf + pooled minutes | Differentiator that lands larger partners; matches wholesale model | Lightweight LD-file-only billing does not meet market parity (all competitors bundle billing+tax) |
