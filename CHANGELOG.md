# Changelog — Lansweeper PO Skills

All notable changes between the **OLD** and **NEW** skill packages are documented here.
This release represents a shift in authoring philosophy for the core skills: from
metadata-heavy templates with AI-generated annotations toward human-readable content
grounded in verified, real-world data.

---

## [Unreleased] — OLD → NEW

### Summary

| Skill | Old | New | Status |
|---|---|---|---|
| `jira-ticket-creator` | 752 lines | 592 lines | **Rewritten** |
| `test-plan-creator` | 191 lines | 437 lines | **Rewritten** |
| `orchestrator-po` | 298 lines | 344 lines | **Extended** |
| `pendo-tracker` | — | 214 lines | **Added** |
| `chat-export` | 139 lines | — | **Removed** |
| `technical-writer` | 289 lines | — | **Removed** |
| `confluence-researcher` | 193 lines | 193 lines | Unchanged |
| `figma-researcher` | 267 lines | 267 lines | Unchanged |
| `jira-researcher` | 206 lines | 206 lines | Unchanged |
| `slack-researcher` | 215 lines | 215 lines | Unchanged |
| `sprint-planner` | 267 lines | 267 lines | Unchanged |
| `stakeholder-communicator` | 241 lines | 241 lines | Unchanged |

---

### Added

#### `pendo-tracker` (new skill)

A dedicated, reusable skill for Pendo instrumentation that was previously embedded
as loose guidance inside `jira-ticket-creator`.

- **Dual mandatory verification** before any event name is written:
  - **Check A — PLG Instrumentation Plan** (Confluence pageId `5518229668`): the
    intent and naming spec.
  - **Check B — Pendo production data** (via `searchEntities` MCP tool): the ground
    truth of what actually exists live.
- **Event status cross-referencing** — every event is labelled as one of:
  confirmed active / planned-not-implemented / undocumented-in-Pendo / proposed-new.
  No event may be presented as confirmed unless it was found in Pendo.
- **Light vs Full format** selection driven by issue type (functional story with
  tracking vs. dedicated instrumentation story, per ACME-50950).
- **PLG stage reference** (Onboarding / Adoption / Engagement / Retention / Expansion)
  and a 6-step workflow ending in a validation gate.
- Documented integration contract with `jira-ticket-creator`.

### Changed

#### `jira-ticket-creator` (rewritten)

The most significant change in the release. Core philosophy moved from
"metadata templates with AI annotations" to "human-readable prose."

- **Removed all metadata tables** from Epic / Story / Task / Bug / Vulnerability
  templates (Epic ID, Owner, Tech Lead, QA Lead, Labels, Components, Status,
  Priority, Sprint, etc.). This data is now left to Jira's native fields instead of
  being duplicated inside the description, where it would drift out of sync.
- **Removed "AI Analysis Notes"** sections from every template. Tickets no longer
  expose that they were machine-generated (assumptions, confidence level, root-cause
  guesses, etc.).
- **Removed `[INFERRED]` / `[PENDING]` markers** from final output. Replaced by the
  new rule: *"Omit, don't fill N/A"* — inapplicable fields are dropped entirely
  rather than shown as placeholders.
- **Story template now mirrors the official ACME project template (ACME-17271):**
  opens directly with the "As a / I want / so that" user story statement (no header
  above it), followed by Functional Acceptance Criteria (bullets, not tables), and
  Optional Requirements (Technical Acceptance Criteria, Design (UX), Security and
  Compliance, Others) that are only included when confirmed relevant.
- **Bug template restructured to match the official ACME bug structure (ACME-56921):**
  🖥️ Overview → 🏢 Site ID (Cloud only) → 🔑 AssetKey (Cloud only) → 👣 Steps to
  reproduce (last step is always 🐛) → 🔥 Current result → 👩‍🚒 Expected result →
  📹 Evidences. Removed the old Severity / Environment / Browser / Reproduction-rate
  block and the evidence table.
- **Vulnerability template condensed:** the multi-table Metadata / Traceability /
  Severity-classification blocks were collapsed into a single prose "Classification"
  section (Severity, CVE, CWE, OWASP, CVSS, Affected Versions, Discovery Method).
- **Pendo tracking is now explicit opt-in per story** — the assistant must always
  ask *"Do you want to track Pendo events for this story?"* before generating the
  section, even when UI changes are obvious. The decision belongs to the user.
- **Pendo dual-verification requirement** added (mirrors the new `pendo-tracker`
  skill): both Confluence and Pendo must be queried live before populating events.
- **Feature Flag handling clarified:** for Epics/Tasks the flag table is only
  included when a flag is specified (otherwise omitted); Stories reference flags
  contextually in the FAC/TAC rather than in a dedicated table.
- **Pendo section rendering fixed:** must be wrapped in a collapsible block, and a
  new **ADF section** documents that `contentFormat: "adf"` with the `expand` node
  type is required — the `markdown` format does not render `<details><summary>` in
  Jira (it appears as raw HTML).
- **Self-Evaluation Checklist rewritten** to enforce the new rules: no visible
  placeholders, no metadata tables, ACs must describe how the user accesses the
  feature, Pendo only present if the user opted in, live verification confirmed, etc.

#### `test-plan-creator` (rewritten)

Expanded from a generic template into a rigorous, guideline-driven QA skill.

- **Codified the 21 Official QA Guidelines** defined by the Lansweeper QA team:
  explain the user story, cite every test source, Gherkin format, corner cases,
  negative tests, feature-flag awareness, technical-comment integration, no
  unfounded assumptions, preconditions/test data, post-action validation, explicit
  regressions, automation level, final matrix offer, security tests, output-format
  validation, verification commands, functional-area IDs, phased testing,
  bug-reporting criteria, cross-platform/browser testing, and sibling-tool
  consistency.
- **Mandatory Gherkin format** — all tests written as Feature / Scenario / Given /
  When / Then, with "The user" as the subject.
- **Source-required rule** — every test must cite its origin (AC line, tech comment,
  TL note, prior bug, known restriction); undocumented behaviors may not be invented.
- **Phased testing model (A→G):** Quick validation → Core functionality → Detailed
  results → Output validation → Advanced modes → Cross-platform/environment → Edge
  cases, resilience & security.
- **Role-based template columns** (Dev 1 / Dev 2 / QE / PO) and an Environments ×
  Roles matrix (STAG / PROD).
- **Minimum test counts** table by complexity tier (Standard vs Complex, >10 sibling
  stories), covering functional / corner / negative / regression / security.
- **Karpathy Self-Evaluation Loop** added before preview presentation.
- **Security tests** are now mandatory whenever credentials, tokens, or PII are
  handled.

#### `orchestrator-po` (extended)

- **Added routing for the new `pendo-tracker` skill**, including trigger keywords and
  implicit routing when creating UI/user-facing issues.
- **Added Test Plan Creator coordination** — routes to test planning when a story is
  complex/high-risk or the user asks for QA coverage ("create the story and the test
  plan", "cover this with tests").
- **Added Pendo + Ticket Creator coordination** — parallel instrumentation so
  tracking ships with the feature rather than as a follow-up sprint.
- **New anti-pattern documented:** creating UI feature tickets without considering
  Pendo tracking.
- **New coordination examples** (feature story with Pendo, story + test plan, complex
  story with full coverage, standalone Pendo, sprint with instrumentation coverage).
- Updated the skill name in the routing table from "Jira Test Plan Creator" to
  "Test Plan Creator" and enriched its trigger keywords (Gherkin, acceptance/
  regression/security tests, etc.).
- Reinforced the "templates are non-negotiable" rule to add "content must be human
  prose, never metadata tables or AI analysis notes."

### Removed

- **`chat-export`** — removed from the PO package (general-purpose utility, not part
  of the PO workflow suite).
- **`technical-writer`** — removed from the PO package (general documentation skill,
  not part of the PO workflow suite).

### Unchanged

The following skills are byte-for-byte identical between the two packages:
`confluence-researcher`, `figma-researcher`, `jira-researcher`, `slack-researcher`,
`sprint-planner`, `stakeholder-communicator`.

---

### Migration notes

- **Native Jira fields:** the fields dropped from ticket descriptions (owner,
  labels, components, severity, environment, story points as text, etc.) should now
  be populated via Jira's native fields, not the description body.
- **Bug severity/environment:** the old Bug template's Severity / Environment /
  Browser / Reproduction-rate block is gone. If any downstream process parsed those
  from the description, migrate it to native fields.
- **ADF requirement:** any automation creating issues with a Pendo section must send
  `contentFormat: "adf"` using the `expand` node — `markdown` will not render the
  collapsible panel.
- **Removed skills:** confirm `chat-export` and `technical-writer` were intentionally
  dropped before publishing; re-add them to the repo if they are still needed
  elsewhere.
- **QA guideline ownership:** the 21 QA guidelines are now hard-coded in
  `test-plan-creator`; assign an owner so the skill is updated when the QA team
  revises them.

### Ordering nit (non-blocking)

In `orchestrator-po`, the new coordination examples appear out of numerical order
(8, 11, 12, 9, 10). Purely cosmetic — consider renumbering on the next edit.
