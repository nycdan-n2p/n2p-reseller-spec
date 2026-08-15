# Feature Specification: White Label Reseller Portal

**Feature Branch**: `001-reseller-portal`

**Created**: 2026-08-11

**Status**: Draft

**Input**: net2phone White Label program — a portal that lets a reseller sell, deliver, manage, and
invoice UCaaS/CCE (and later AI + SIP) to their own customers under their own brand, with net2phone
invisible behind the curtain. Derived from the Scoping Framework v0.7, the PO interview Q&A, the
reseller and engineering calls, the NetSapiens/Apollo competitor parity scan, and the working prototype.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Onboard and provision a net-new customer (Priority: P1) 🎯 MVP

A reseller signs a new business customer, spins up their account on a pod, applies their own brand,
and gets the customer making and receiving calls — guided, in about 20 minutes, with net2phone
nowhere visible.

**Why this priority**: This is the 90-day soft-launch milestone and the minimum *sellable* product.
Without "log in → create account → make/receive calls under my brand," there is no program.

**Independent Test**: From an empty reseller account, complete brand setup, create one customer,
provision a seat + DID, register a phone, and place a two-way call — all without contacting support
and without any net2phone branding appearing to the customer.

**Acceptance Scenarios**:

1. **Given** a signed reseller with brand (logo, color, domain) configured, **When** they run the
   new-customer wizard, **Then** a tenant is created on a pod and reachable at the reseller's domain.
2. **Given** a provisioned customer, **When** a user places and receives a call, **Then** the call
   completes and appears in that customer's call history.
3. **Given** a reseller viewing any customer, **When** they choose "view as customer," **Then** a
   banner is shown and the session is logged.

---

### User Story 2 - Administer a customer's phone system (Priority: P1)

Drilling into a customer, the reseller (or the customer, if self-service is enabled) manages
extensions, dial plans and permissions, auto attendants, call queues/ring groups, time-frame
schedules, music on hold, conferences, and reviews call history — full parity with incumbent portals.

**Why this priority**: Parity gate. Competitors' entire product is this surface; a reseller cannot
run a customer without it. It is the "Manage" pillar.

**Independent Test**: For one customer, create an extension with a device and calling permission,
build an auto attendant on a schedule, add a ring group, set a holiday schedule that routes all
queues to voicemail, and confirm each change takes effect and appears in call history.

**Acceptance Scenarios**:

1. **Given** a customer, **When** the reseller adds an extension and assigns a device with a dial
   permission (e.g., US + Canada, international off), **Then** calls honor that permission.
2. **Given** a holiday, **When** the reseller adds one holiday schedule, **Then** it applies to
   every attendant and queue for that customer in a single action.
3. **Given** MOH is unset for a customer, **When** the reseller enables it, **Then** it inherits the
   brand-default playlist and messages unless overridden.

---

### User Story 3 - Wholesale billing, pooled minutes, and per-customer usage (Priority: P1)

The reseller sees one consolidated wholesale statement from net2phone (taxes filed), bills their own
customers under their brand, draws minutes from a shared pool across all customers, and can see usage
broken down by service, by customer.

**Why this priority**: "Invoice" pillar and a competitive differentiator (per-customer usage
visibility). Onboarding/quotes break billing on "day two," so it must be near-concurrent with US1.

**Independent Test**: Generate a reseller statement showing MRC + metered usage + taxes; confirm
minutes draw from the shared pool (no per-seat credit limit); open the usage-by-service-by-customer
view and reconcile a customer's minutes against their call history.

**Acceptance Scenarios**:

1. **Given** metered usage across customers, **When** the cycle closes, **Then** minutes are drawn
   from the reseller's shared pool and only pool overage is charged.
2. **Given** a reseller invoice, **When** it is generated, **Then** it is aggregate (not per-customer)
   with taxes calculated and filed by net2phone, and shows nothing net2phone-branded to end customers.
3. **Given** an account, **When** the reseller opens usage-by-customer, **Then** minutes, SMS/MMS, AI,
   and DIDs are shown per customer and reconcile to CDRs.

---

### User Story 4 - Protect accounts: fraud, spend caps, E911 and 10DLC compliance (Priority: P2)

The reseller sees fraud events, per-account velocity/spend caps that auto-throttle, and a compliance
surface for E911 (dispatchable location, notify contact) and 10DLC registration, with the assistant
able to remediate.

**Why this priority**: Experienced resellers ask about fraud first; E911/10DLC are legal exposure.
High business value, but the account must exist (US1) before it can be protected.

**Independent Test**: Trip an international-velocity rule on a test account and confirm auto-throttle
at the cap; flag a device missing an E911 address and resolve it; submit a 10DLC campaign and see
status.

**Acceptance Scenarios**:

1. **Given** an account with an international spend cap, **When** unusual traffic exceeds it, **Then**
   traffic is throttled and the reseller is alerted with a one-click freeze and "fix it for me."
2. **Given** a device without a dispatchable E911 address, **When** compliance runs, **Then** it is
   flagged as at-risk and can be set/validated.

---

### User Story 5 - Migrate a customer from a competitor (Priority: P2)

The reseller imports a customer from a competitor export (NetSapiens, RingCentral, 8x8) or CSV — maps
extensions, numbers, and devices — dry-runs, then schedules a cutover with rollback.

**Why this priority**: Most white-label deals are flips. Sequenced as a fast-follow after the net-new
soft launch, but the import primitive is core, not phase-2.

**Independent Test**: Import a sample competitor export of N endpoints, resolve mapping errors in a
dry-run, and complete a cutover on a scheduled window with a rollback path available.

**Acceptance Scenarios**:

1. **Given** a competitor export, **When** the reseller imports it, **Then** fields are mapped, a
   dry-run reports conflicts, and nothing changes until the reseller confirms.
2. **Given** a validated migration, **When** cutover runs, **Then** endpoints move on schedule and a
   rollback is available.

---

### Roadmap stories (post-MVP; specified later)

- **AI agents (hard launch, ~Q3)** — deploy AI agent templates to a customer, bundled with Integrate.
  `[NEEDS CLARIFICATION: AI in MVP (business) vs Q3 (engineering) — resolve launch sequencing]`
- **SIP trunking (fast-follow)** — trunks to premise PBXs; `[NEEDS CLARIFICATION: not in first
  release per engineering, but bread-and-butter per business — confirm phase]`
- **Automations / Integrate as a billable platform** — no-code workflows, charge per API task.
- **Mobile management app** — grey-label; full private app as a premium fee.

### Edge Cases

- A reseller's end customer does not pay: the reseller suspends the *individual* customer (not all);
  the reseller remains responsible to net2phone (they bill in advance).
- A reseller does not pay net2phone: suspend the reseller's admin access until paid; do not silently
  drop end-customer service.
- A reseller changes their branded domain: certificates must regenerate; limit or wildcard to control
  cost — never absorbed.
- A "view-as-customer" misconfiguration shows the wrong brand: acceptable as a display error, but MUST
  never leak another tenant's data (single auth server, single database, tenant-scoped).
- International/premium destinations, toll-free, and long-distance usage must be measurable and
  bucketed for billing even when the end customer is sold "unlimited."

## Requirements *(mandatory)*

### Functional Requirements

**Tenancy, provisioning, branding**
- **FR-001**: System MUST let a reseller create, configure, and manage many customer tenants and
  switch/drill into any one, with a customer selector or per-customer entry point.
- **FR-002**: System MUST provision a new customer onto a pod (account, seats, DIDs) via a guided
  wizard in roughly 20 minutes, and support cloning a prior customer as a template/blueprint.
- **FR-003**: System MUST apply reseller branding (logo, color, product name, domain) as a theme that
  every application reads; theming is keyed to **tenant, not domain**, and inherits brand defaults
  unless overridden.
- **FR-004**: System MUST route a reseller's customers on the reseller's own domain via CNAME to a
  static backend host; pre-login branding resolves by DNS, post-login by JWT/tenant.
- **FR-005**: System MUST grey-label net2phone everywhere an end customer can see (mobile app,
  integrations, networking, invoices, status page); full white-label of mobile/integrations is a
  premium capability.

**Phone-system administration (per customer)**
- **FR-006**: System MUST manage extensions/users with site, department, dial plan, and calling
  permission (domestic/international/premium), plus device assignment and BLF/key layout.
- **FR-007**: System MUST manage auto attendants (IVR with schedule + greeting), call queues/ring
  groups (ring-all, round-robin, linear, longest-idle; overflow to voicemail/queue/AI), time-frame
  schedules, music on hold (with inherited messages), and audio conference bridges.
- **FR-008**: System MUST provide a schedule action that applies a holiday/closure to all attendants
  and queues for a customer in one operation.
- **FR-009**: System MUST provide per-customer call history/CDR with search, filter, and export.

**Inventory**
- **FR-010**: System MUST manage DIDs (assign, port, treatment/destination/notes, CNAM), SMS numbers
  (10DLC), phone hardware (device inventory, zero-touch provisioning, firmware, offline status), and
  E911 (endpoints, dispatchable addresses, notify contacts).

**Bulk / migration**
- **FR-011**: Users MUST be able to bulk import and export (users, numbers, devices) from CSV or a
  competitor export, with field mapping, dry-run/conflict report, confirm-to-apply, and rollback.

**Billing & tax**
- **FR-012**: System MUST bill the reseller a single aggregate wholesale statement (a-la-carte: seats,
  DIDs, E911, minutes; plus subscription fees and minimum commitments), with MRC in arrears and
  metered usage; the reseller bills their end customers under their brand, in advance.
- **FR-013**: Minutes MUST be a shared pool across all of a reseller's customers with overage pricing;
  there is NO per-seat credit limit. Usage must be measurable per service (minutes, SMS/MMS, fax, AI,
  toll-free, international) and viewable by customer.
- **FR-014**: System MUST provide tax handling so resellers can bill their customers correctly by
  service address; net2phone calculates, collects, and files so resellers do not register in 50
  states. `[NEEDS CLARIFICATION: build vs. integrate a tax engine (CCH today; ShoreTax/SureTax/Rev.io
  in market) — decide after the ShoreTax evaluation]`
- **FR-015**: System MUST support the pooled/PRC-style prepaid balance, an adjustment/usage file to
  the reseller, dunning/collections under the reseller's policy, and individual-customer vs.
  reseller-admin suspension logic.

**Safety & compliance**
- **FR-016**: System MUST enforce per-account fraud velocity and spend caps with auto-throttle,
  freeze, and assistant-assisted remediation, and log all view-as-customer sessions.
- **FR-017**: System MUST support 10DLC registration status and E911 (Kari's Law / RAY BAUM's Act)
  dispatchable-location and notify-contact compliance, and STIR/SHAKEN attestation reporting.

**Support, integrations, assistant**
- **FR-018**: System MUST provide reseller-portal ticketing that escalates to net2phone Tier 3 (Tier 1
  handled by the reseller), syncs to Salesforce, and surfaces reseller-created accounts in CS tooling;
  SLA target aligned to 99.999%+.
- **FR-019**: System MUST expose integrations (native marketplace apps, grey-labeled) and Integrate as
  a billable platform (charge per API task), with provisioning/usage/billing APIs and webhooks.
- **FR-020**: System SHOULD provide an AI assistant that can act on reseller accounts (not just
  answer), that explains its plan, asks before acting, reports progress, and records changes.

**Roadmap requirements** *(specified in later iterations)*
- **FR-021**: System MUST support AI agent templates deployable per customer, bundled with Integrate.
  `[NEEDS CLARIFICATION: MVP vs ~Q3 hard launch]`
- **FR-022**: System MUST support SIP trunking to premise PBXs. `[NEEDS CLARIFICATION: fast-follow /
  not first release]`

**Telco platform & extended offer** *(each tagged **[Portal]** = reseller-facing screen, or
**[Platform]** = network-layer capability behind the offer that may only need a status/config surface)*

- **FR-023 [Platform]**: The offer MUST run on an SBC providing **least-cost routing (LCR)** and call
  **rating**; the portal surfaces routing profiles and rate/tax status, not day-to-day operation.
- **FR-024 [Platform]**: System MUST provide **carrier integration/sync** (carrier connections, number
  order/port sync, RESPORG for toll-free); surfaced as a carrier-connections/status panel.
- **FR-025 [Portal]**: System MUST show **Robocall Mitigation Database (RMD)** registration status
  alongside STIR/SHAKEN attestation; the platform performs the RMD filing.
- **FR-026 [Portal]**: System MUST provide **call recording** management with **transcription and
  sentiment analysis**, per customer, with retention and grey-labeled storage.
- **FR-027 [Portal]**: System MUST support **fax** as an eFax portal and **ATA** (analog telephone
  adapter) device provisioning for analog/fax endpoints.
- **FR-028 [Portal]**: System MUST provide a **Store** — a reseller storefront for hardware and add-ons,
  optionally exposed to the reseller's own customers for self-service purchase.
- **FR-029 [Portal, roadmap]**: System SHOULD provide **Microsoft Teams / TeamMate** integration for UC
  clients. `[NEEDS CLARIFICATION: roadmap target ~Q1 2027]`
- **FR-030**: For every capability above, the design MUST record whether it is a **reseller-facing
  screen** or a **platform-layer** function the reseller never directly operates, so scope stays honest.

### Key Entities *(data)*

- **Reseller**: brand (logo/color/name/domain), pricing/catalog (SKUs, MSRP, margins), minute pool,
  prepaid balance, team & roles, integrations, policies (fraud caps, dunning, MFA).
- **Customer (Tenant)**: belongs to a reseller; provisioned on a pod; brand theme (inherited);
  package; sites; departments; feature toggles.
- **Extension/User**: dial plan, calling permission, site/department, device, BLF keys, voicemail.
- **Device**: MAC, model, firmware, registration status, assigned user, E911 address, keys/BLF.
- **Number/DID**: treatment, destination, CNAM, toll-free flag, port state; SMS number + 10DLC campaign.
- **E911 record**: endpoint, dispatchable address, notify contact, compliance status.
- **Call-routing objects**: auto attendant, queue/ring group, schedule/time-frame, MOH, conference.
- **Package/SKU**: cost, MSRP, margin, included services, minimum commitment.
- **Usage/CDR**: per-call detail; per-service, per-customer aggregates (minutes/SMS/AI/DIDs).
- **Invoice/Statement**: reseller wholesale statement; taxes/regulatory fees; adjustment file.
- **Fraud event**, **Ticket**, **AI Agent**, **Automation**, **Audit/View-as log**.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A new reseller can take a net-new customer from empty to a completed two-way call in
  under ~20 minutes, guided, with zero support contact.
- **SC-002**: 100% of the incumbent-portal "Manage" surface (extensions/dial-plan/permission,
  attendants, queues, schedules, MOH, conferences, call history) and Inventory (DID, hardware, E911,
  SMS/10DLC) is present — measured against the parity matrix (target: no "Gap" rows remaining at MVP
  scope).
- **SC-003**: A reseller can bulk-import a competitor export of at least a few hundred endpoints, clear
  mapping conflicts in a dry-run, and cut over on a scheduled window with rollback.
- **SC-004**: Fraud auto-throttle engages at the configured cap on 100% of accounts; measurable loss
  prevented is reported.
- **SC-005**: A reseller can reconcile any customer's monthly minutes against CDRs, and minutes draw
  from the shared pool with no per-seat credit limit.
- **SC-006**: No end-customer-visible surface (app, invoice, integration, status page) reveals
  net2phone in grey-label mode (audited).
- **SC-007**: Reseller-reported time-to-resolution improves because Tier-1 self-service diagnostics
  and assistant remediation deflect a measurable share of tickets before Tier-2/3.

## Assumptions

- Launch sequence is **soft launch (net-new, UCaaS + CCE)** first, **SIP** and **AI + Integrate** as
  fast-follow / ~Q3 hard launch — pending the open decisions above.
- Unite + CCE (inside Unite) are the day-one product bundle; hardware marketplace, COACH, and a
  reseller mobile-management app are later phases.
- Resellers typically operate in one or two states, so tax granularity can be lenient (tax at the
  reseller/service-address level, not per-end-user across 50 states).
- The reseller portal reuses net2phone's PAM / partner-portal / CS-tool stack knowledge for back-end
  and APIs; the UI is a new product surface.
- Multi-tenant pods are under evaluation (per-reseller config vs. versioned standard pod); today pods
  are single-tenant. `[NEEDS CLARIFICATION: multi-tenant pod model]`
- Delivered by a single dedicated team (DSM + PO) for ≥ 2 quarters; competing S3 priorities
  (CyberSource payments recode, Argentina/Navigator EOL) must be de-conflicted.
