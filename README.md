# White Label Reseller Portal — Spec Kit

A [Spec Kit](https://github.com/github/spec-kit)–style spec-driven-development package for the
net2phone White Label Reseller Portal. It captures the **what/why** (spec), the **how** (plan), and
the **work** (tasks), governed by a **constitution**, in the format spec-kit's `/speckit.*` commands
produce and consume.

## Structure

```text
reseller-portal-spec-kit/
├── memory/
│   └── constitution.md        # governing principles (the Constitution Check gate)
├── specs/
│   └── 001-reseller-portal/
│       ├── spec.md            # prioritized user stories, functional requirements, entities, success criteria
│       ├── plan.md            # tech context, architecture, project structure, complexity
│       └── tasks.md           # tasks grouped by user story (US1–US5) + roadmap
└── README.md
```

## How it maps to the spec-kit workflow

| Spec Kit command | Artifact here |
|---|---|
| `/speckit.constitution` | `memory/constitution.md` |
| `/speckit.specify` | `specs/001-reseller-portal/spec.md` |
| `/speckit.plan` | `specs/001-reseller-portal/plan.md` |
| `/speckit.tasks` | `specs/001-reseller-portal/tasks.md` |
| `/speckit.clarify` | the `[NEEDS CLARIFICATION]` markers (AI timing, tax engine, multi-tenant pods, front-end framework, SIP sequencing) |
| `/speckit.implement` | execute `tasks.md` (US1 = soft-launch MVP first) |

To use with the CLI: `specify init reseller-portal --integration copilot`, then drop these files into
the generated `memory/` and `specs/001-reseller-portal/` folders (or paste each file's content into
the matching `/speckit.*` command).

## Source material (what this kit was distilled from)

- **Scoping Framework v0.7** — wedge, launch phasing (soft → SIP fast-follow → AI hard launch),
  billing model, labeling, integrations/mobile, architecture, team & delivery, risks, open decisions.
- **PO Interview Q&A (consolidated)** — billing/tax detail, pooled minutes, MRC timing, onboarding
  minimums, mobile/integration expectations.
- **Reseller + engineering call notes** — the three boxed products (Unite, CCE, AI Agent), SIP as
  fast-follow (Arunim), multi-tenant pods, call-traces service, dedicated-team construct.
- **Competitor parity scan (NetSapiens/Apollo)** + the parity matrix — the phone-system + inventory +
  E911 + import/export surface that MVP must reach.
- **The working prototype** (`reseller-portal-prototype.html`) — the approved UI, with the Phone
  system drill-down and billing/usage additions merged in.

## Open decisions (resolve in `research.md` / `/speckit.clarify`)

1. **AI in the MVP** (business) vs **~Q3 hard launch** (engineering).
2. **SIP trunking** — fast-follow vs. not-in-first-release.
3. **Tax engine** — build vs. integrate (CCH today; ShoreTax/SureTax/Rev.io).
4. **Multi-tenant pods** — per-reseller config vs. versioned standard pod.
5. **Production front-end framework** for the console.

## MVP definition (soft launch, ~2 quarters)

User Story 1 (provision + brand a net-new customer → live calls) is the milestone. User Stories 2
(phone-system parity) and 3 (wholesale billing + pooled minutes) are P1 and land with/just after it.
Migration (US5), fraud/compliance (US4), then AI + Integrate and SIP follow.
