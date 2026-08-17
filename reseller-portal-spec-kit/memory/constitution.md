# net2phone White Label Reseller Portal — Constitution

**Version**: 1.0.0 · **Ratified**: 2026-08-11 · **Status**: Active

These are the governing principles for the White Label Reseller Portal. Every spec, plan,
and task is checked against them (the "Constitution Check" gate). Principles are ordered;
when they conflict, the higher-numbered exception must be justified in Complexity Tracking.

## Principle 1 — The reseller is the customer

The product we are building is a *reseller enablement* platform, not an end-user PBX. Every
screen serves the CoreDial mental model resellers buy on: **Sell · Deliver · Manage · Invoice**.
End-customer telephony administration exists, but it lives one level down (a drill-down under
each customer), because the reseller's day-to-day is running a *business*, not a phone system.

## Principle 2 — Parity is table stakes; differentiation is the point

We are 20+ years late to white-label UC. We MUST match what incumbents (NetSapiens/Apollo,
SkySwitch, RingLogix, Viirtue) already give resellers — full phone-system admin, number/DID,
hardware, and E911 inventory, bulk import/export. We WIN on what they do badly: AI agents,
fraud/risk controls, migration ease, per-customer usage transparency, and a portal that is
not antiquated. Parity is necessary but never sufficient.

## Principle 3 — White-label by default, mask net2phone

Grey-label is the default: net2phone is invisible everywhere the end customer can see —
mobile app, integrations, networking, invoices, status pages, help center. Full white-label
of mobile and integrations is a premium, large-reseller option. Nothing an end customer
receives may reveal net2phone.

## Principle 4 — Wholesale economics: we don't eat costs

This is a wholesale business. Minutes are a **shared pool** across all of a reseller's
customers — there is no per-seat credit limit. We wholesale every unit (minutes, messages,
DIDs, AI). MRC is billed to the reseller in arrears; the reseller bills their end customer in
advance. Any per-domain cost we incur (e.g., TLS certificates) is recovered via a platform/
subscription fee, never absorbed.

## Principle 5 — Safety and compliance are non-negotiable

A compromised SIP account can generate tens of thousands of dollars of fraud overnight, and we
owe the wholesale cost regardless of collection. Fraud velocity caps, spend limits, and
auto-throttle apply to every account. E911 (Kari's Law / RAY BAUM's Act) and 10DLC are
compliance surfaces we assist with even though ownership sits with the customer. Every
"view-as-customer" session is logged; high-value assistant actions require approval.

## Principle 6 — Migration-first, but net-new leads the launch

Most white-label deals are flips, not greenfield, so bulk import (CSV / competitor export),
field mapping, dry-run, and rollback are core capabilities — not a phase-2 nicety. However,
the launch sequence builds for **net-new first** (soft launch: UCaaS + CCE), with migration
as a planned fast-follow. SIP trunking is a fast-follow, not first release.

## Principle 7 — AI-native and transparent

The assistant can *act* on the reseller's accounts, not just answer. But it explains its plan
before acting, asks for confirmation, reports progress, and keeps a record of what it changed.
Configuration inherits brand and package defaults unless explicitly overridden. AI Agent is the
commercial hook, sequenced with Integrate (target ~Q3 / hard launch) — see the open decision in
the spec.

## Principle 8 — One team, one surface

The program is delivered by a single dedicated team (single DSM, single PO) for at least two
quarters. The portal is one product with one navigation; per-customer telephony is a drill-down,
not a second app. No feature ships as a bolt-on that the end customer could tell apart.

## Governance

- This constitution supersedes ad-hoc preferences. Amendments require a version bump and a note
  in the spec's decision log.
- Every `plan.md` MUST include a Constitution Check gate and justify any violation in a
  Complexity Tracking table.
- Open commercial/architectural decisions (AI timing, tax engine build-vs-partner, mobile-app
  fee model, SIP sequencing) are tracked as `[NEEDS CLARIFICATION]` until resolved.
