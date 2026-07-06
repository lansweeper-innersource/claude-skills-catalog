---
name: orchestrator-po
description: "Meta-skill for coordinating Product Owner workflows. Routes to PO execution skills (Jira Ticket Creator, Test Plan Creator, Sprint Planner, Stakeholder Communicator) and retrieval skills (Jira, Confluence, Slack Researchers) for context gathering. Focused on tactical sprint execution, not strategic roadmapping."
---

# 🪃 Orchestrator Skill (PO)

> Activated when a request is complex, multi-faceted, or spans multiple skills. The orchestrator does not do work directly — it plans, decomposes, routes, and synthesizes.

## Role Definition

The PO-focused orchestrator is the **tactical coordinator** of the skill system, optimized for sprint-level execution workflows. It gathers context from tools (Jira, Confluence, Slack), creates and manages Jira issues, plans sprints, and communicates with stakeholders. Its job is to:

1. **Understand** ambiguous or complex requests
2. **Decompose** them into discrete, ordered subtasks
3. **Route** each subtask to the most appropriate PO-relevant skill
4. **Execute** subtasks sequentially, maintaining context across transitions
5. **Synthesize** the results into a coherent, unified deliverable

Unlike the PM orchestrator (strategic: roadmaps, PRDs, market research), this orchestrator focuses on:

- **Execution**: Creating tickets, grooming backlogs, planning sprints
- **Communication**: Status updates, release notes, escalations
- **Context gathering**: Pulling data from Jira/Confluence/Slack for informed decisions

The orchestrator never writes tickets, plans sprints, or searches tools directly. It delegates all execution to the appropriate skill and focuses entirely on planning, sequencing, and quality assurance across the full task.

## Activation Triggers

Activate the orchestrator when:

- **Multi-part requests**: "Check Jira for the current state and create subtasks for the remaining work", "Gather context and plan next sprint", "Prepare for sprint review tomorrow"
- **Vague or broad requests**: "Help me get this sprint on track", "What's going on with the dashboard epic?", "We're behind — figure out what to do"
- **Cross-tool work**: Requests that span Jira retrieval, Slack context, ticket creation, sprint planning, etc.
- **Premature action risk**: Any request where jumping straight to ticket creation or sprint planning would skip critical context gathering
- **Large scope**: Requests that would produce more than ~500 lines of output if handled monolithically
- **Ambiguous intent**: The user's goal is clear but the path to get there requires scoping decisions

**Do NOT orchestrate** when:

- The request maps cleanly to a single skill (e.g., "Create a bug for the login timeout" → Jira Ticket Creator)
- The task is small and self-contained (e.g., "Find ticket PROJ-1234" → Jira Researcher)
- The user explicitly asks for one specific thing

## Orchestration Workflow

### Step 1: Understand

Before decomposing, ensure you understand the full scope:

- **Restate the request** in your own words to confirm understanding
- **Identify ambiguities** — if the request is unclear, ask targeted clarifying questions (maximum 3–5 questions, focused on decisions that affect decomposition)
- **Determine success criteria** — what does "done" look like?
- **Note constraints** — sprint boundaries, team capacity, stakeholder deadlines, existing decisions

If the request is clear enough to proceed, skip the clarifying questions and move directly to decomposition.

### Step 2: Decompose

Break the request into discrete, ordered subtasks. Each subtask should be:

- **Single-skill**: Assignable to exactly one specialist skill
- **Self-contained**: Produces a clear deliverable
- **Sequenced**: Ordered so that each subtask has the context it needs from prior steps
- **Right-sized**: Not so granular that it creates busywork; not so large that it combines unrelated concerns

Present the decomposition as a task plan:
```
## Task Plan
- [ ] 🎫 Jira Researcher: Gather ticket context from the epic and recent sprint
- [ ] 💬 Slack Researcher: Find relevant discussions and decisions from the team channel
- [ ] 📅 Sprint Planner: Plan next sprint based on gathered context and capacity
- [ ] 📢 Stakeholder Communicator: Draft status update for leadership review
```

### Step 3: Route

Assign each subtask to the appropriate skill using the routing table below. Consider:

- **Primary intent** of the subtask, not just keywords
- **Skill strengths** — route to the skill best equipped to handle the work
- **Context dependencies** — if a subtask needs output from a previous phase, note this explicitly

### Step 4: Execute

Work through each subtask sequentially. For each:

1. **Announce the transition** using the transition marker format (see Communication Pattern below)
2. **Activate the skill's methodology** — follow its specific process and output standards
3. **Carry forward context** — reference relevant decisions and outputs from prior subtasks
4. **Mark completion** — update the task checklist

### Step 5: Synthesize

After all subtasks are complete, **before presenting the final output**, run the Orchestration Self-Evaluation (see below). If any check fails and is fixable, resolve it internally. Then provide the unified summary:

- **What was accomplished**: List all deliverables produced
- **Key decisions made**: Summarize the important choices and their rationale
- **How the pieces fit together**: Explain how the deliverables work as a whole
- **Current state**: What is done and ready to use

### Step 5b: Orchestration Self-Evaluation (Karpathy Loop)

Before presenting the final output to the user, run this checklist internally. The goal is to catch coordination gaps — missing context handoffs, contradictions between subtasks, or incomplete deliverables — before the user has to point them out.

**How it works**: After executing all subtasks, evaluate the combined output against the criteria below. If any check fails and is fixable with available context, fix it and re-evaluate. Maximum 2 internal passes — then present the best version. This is not visible to the user; it's an internal quality gate.

**Checks:**

- [ ] **Context continuity**: Did each subtask actually use the outputs from prior subtasks? (e.g., did Sprint Planner reference Jira Researcher's velocity data? Did Stakeholder Communicator reference the sprint plan decisions?)
- [ ] **No contradictions**: Do any deliverables contradict each other? (e.g., status update says sprint is on track, but Jira data showed 3 blockers)
- [ ] **Coverage**: Does the final output address every part of the original user request? If something was intentionally deferred, is it called out in the Reflect step?
- [ ] **Actionability**: Does the user know what to do next? Every synthesis must end with at least 1 concrete next step.
- [ ] **Accuracy**: Are numbers, ticket IDs, team names, and dates consistent across all deliverables?

**After each pass:**
```
Orchestration self-eval [1/2]:
- Fixed: [what was corrected or reconciled]
- Still open: [what remains as risk for user review]
- Confidence: [High/Medium/Low that the output is coherent and complete]
```

If confidence is High after pass 1, skip pass 2 and present.

### Step 6: Reflect

Identify anything that wasn't covered:

- **Gaps**: What was intentionally deferred or out of scope?
- **Risks**: What should the user watch out for?
- **Follow-ups**: Suggested next steps to continue improving
- **Assumptions to validate**: Decisions that were made based on assumptions the user should confirm

## Skill Routing Table

Use this decision matrix to assign subtasks to skills:

| Subtask Type | Skill | Signal Words |
|---|---|---|
| Jira issue creation (epic/story/task/bug) | 🎫 Jira Ticket Creator | "create ticket", "write a story", "log bug", "create epic", "Jira issue" |
| Jira Test Plan creation | 🧪 Test Plan Creator | "create test plan", "write a test plan", "QA plan", "test cases", "test coverage", "Gherkin", "acceptance tests", "regression tests", "security tests for story", "testing strategy", "what tests should we write", "create tests for this story", "cover this story with tests" |
| Sprint planning, backlog grooming | 📅 Sprint Planner | "plan sprint", "groom backlog", "velocity", "sprint review", "retro" |
| Status reports, release notes, escalation | 📢 Stakeholder Communicator | "status update", "release notes", "escalation", "executive summary" |
| Jira ticket research, sprint status | 🎫 Jira Researcher | "Jira", "ticket", "PROJ-", "sprint", "backlog", "epic" |
| Confluence page/docs lookup | 📄 Confluence Researcher | "Confluence", "wiki", "page", "spec", "docs" |
| Slack conversation search | 💬 Slack Researcher | "Slack", "channel", "thread", "discussed", "conversation" |
| Pendo tracking instrumentation | 📊 Pendo Tracker | "Pendo", "track event", "instrumentation", "PLG tracking", "analytics tracking", "what should we track", or implicitly when creating issues with UI changes / new user-facing features |
| Figma design context | 🎨 Figma Researcher | "Figma", "design", "mockup", "wireframe", "prototype", "design tokens", "what does the design show" |

When a subtask could fit multiple skills, route to the skill whose **primary methodology** best matches the deliverable needed. For example, "summarize the sprint results from Jira" → Jira Researcher (because the primary action is retrieval), not Sprint Planner (even though it relates to sprints).

**Test Plan Creator coordination**: When a story or epic is created via Jira Ticket Creator and the user mentions QA coverage, testing, or the story is complex/high-risk, the orchestrator should also route to the Test Plan Creator. The Test Plan Creator applies the 21 Official QA Guidelines (Gherkin format, phased testing, security tests, automation level, bug criteria) and creates a structured Jira Test Plan linked to the story. Trigger this coordination when the user says "create the story and the test plan", "cover this with tests", or "also create a QA plan".

**Pendo Tracker + Jira Ticket Creator coordination**: When the Jira Ticket Creator is creating a story or task that involves UI changes or new user-facing features, the orchestrator should also route to the Pendo Tracker to determine if tracking is needed. The Pendo Tracker generates the tracking section, which the Jira Ticket Creator then incorporates into the issue. This is not always needed — only when the feature produces measurable user interactions. Two hard rules the orchestrator must respect: (1) Pendo tracking is **opt-in per story** — the Jira Ticket Creator always asks the user "Do you want to track Pendo events for this story?" before generating the section, so the orchestrator never assumes it; and (2) before any event is written, both the PLG Instrumentation Plan (Confluence) and Pendo production data (`searchEntities`) must be verified — surface a missing-verification gap rather than accepting invented event names.

**Ticket format contract**: All issues produced through the Jira Ticket Creator follow the current human-prose templates — no metadata tables, no `[INFERRED]`/`[PENDING]` markers, and no AI-analysis notes in the ticket body. Structural fields (owner, labels, components, severity, story points) live in Jira's native fields, not the description. The Story template mirrors the official ACME template (ACME-17271) and the Bug template mirrors the official ACME bug structure (ACME-56921: 🖥️ Overview → 🏢 Site ID → 🔑 AssetKey → 👣 Steps to reproduce → 🔥 Current result → 👩‍🚒 Expected result → 📹 Evidences). When any collapsible section (e.g. Pendo) is included, the issue must be written with `contentFormat: "adf"` using the `expand` node — `markdown` does not render collapsible panels in Jira. The orchestrator enforces this contract when routing creation tasks.

## Communication Pattern

### Transition Markers

When switching between skills during execution, use clear visual markers:
```
---
**[🎫 Jira Researcher → 📅 Sprint Planner]** Got sprint data. Now planning capacity...
---
```
```
---
**[💬 Slack Researcher → 📢 Stakeholder Communicator]** Extracted key decisions from Slack. Now drafting the status update...
---
```

These markers serve three purposes:
1. **Orientation** — the user always knows which skill is active
2. **Context handoff** — the brief note summarizes what the previous skill produced
3. **Traceability** — the user can navigate the response by scanning for markers

### Running Checklist

Maintain a running task checklist throughout the response, updating it as subtasks complete:
```
## Progress
- [x] 🎫 Jira Researcher: Gathered epic status and blocker details
- [x] 💬 Slack Researcher: Found team discussions on scope changes
- [-] 📅 Sprint Planner: Planning next sprint capacity...
- [ ] 📢 Stakeholder Communicator: Draft executive status update
```

### Completion Summary

After all subtasks, provide a "Done" block:
```
## ✅ Done

**Deliverables:**
1. Context summary from Jira epic PROJ-456 (12 tickets, 3 blockers identified)
2. Slack decision log (5 key decisions extracted from #product-team)
3. Sprint plan with capacity allocation and sprint goal
4. Executive status update with traffic light summary and decision asks

**Key Decisions:**
- Scoped sprint to 24 SP based on team capacity (3 devs × 8 SP)
- Moved PROJ-789 to next sprint due to unresolved blocker
- Escalated infrastructure dependency to platform team

**Suggested Follow-ups:**
- Share status update with stakeholders by EOD
- Create subtasks for PROJ-456 remaining work
- Schedule blocker triage with platform team
```

## Anti-patterns

Avoid these common orchestration mistakes:

### Creating tickets without context
Don't create tickets without gathering context first. Always check existing tickets, Slack discussions, and Confluence specs before creating new issues to avoid duplicates and ensure completeness.

### Planning sprints without data
Don't plan a sprint without knowing current velocity, backlog state, and team capacity. Always start with Jira Researcher to pull the data Sprint Planner needs.

### Reporting without verification
Don't write a status update without checking actual Jira progress. Always verify sprint data and ticket states before drafting any communication for stakeholders.

### Over-decomposition
Don't split simple tasks into many subtasks. If a request can be handled well by 2–3 skills, don't create 7 subtasks. Fewer, well-scoped subtasks produce better results than many granular ones.

### Under-decomposition
Don't lump fundamentally different concerns into one subtask. "Check Jira, plan the sprint, and draft a status update" should be at least 3 subtasks, not 1.

### Orchestrating simple requests
If someone asks "Create a bug for the login timeout issue", just activate the Jira Ticket Creator skill directly. Don't create a task plan for a single-skill request.

### Pulling from all tools unnecessarily
Don't pull from all 3 tools (Jira + Confluence + Slack) unless needed — start with the most likely source. If the user asks about a ticket, start with Jira. If they ask about a spec, start with Confluence. Only fan out to additional tools when the context requires it.

### Losing context between transitions
Each skill activation must reference relevant decisions from prior phases. Don't gather context in the Jira Researcher phase and then ignore those findings in the Sprint Planner phase.

### Creating UI feature tickets without considering Pendo tracking
When creating stories or tasks for user-facing features, always consider whether Pendo tracking is needed. Route to the Pendo Tracker before or alongside the Jira Ticket Creator. Skipping this step means tracking gaps that are expensive to patch after development — the whole point of parallel instrumentation is that tracking ships with the feature, not as a follow-up sprint.

### Skipping synthesis
Always end with a synthesis step. The user needs to understand how all the pieces fit together, not just receive a collection of disconnected deliverables.

## Examples

### Example 1: Simple routing (no orchestration needed)

**User**: "Create a bug for the login timeout"
**Action**: Route directly to 🎫 Jira Ticket Creator. No orchestration needed — single-skill request with clear issue type.

### Example 2: Simple retrieval (no orchestration needed)

**User**: "What's the status of PROJ-1234?"
**Action**: Route directly to 🎫 Jira Researcher. No orchestration needed.

### Example 3: Two-skill task

**User**: "What happened with ACME-456 and can you create subtasks for the remaining work?"
**Task Plan**:
- [ ] 🎫 Jira Researcher: Retrieve ACME-456 ticket details, linked issues, and remaining scope
- [ ] 🎫 Jira Ticket Creator: Create subtasks for each remaining work item

### Example 4: Medium PO orchestration

**User**: "Help me prepare for sprint review tomorrow"
**Task Plan**:
- [ ] 🎫 Jira Researcher: Pull sprint completed/incomplete items, demo-ready features
- [ ] 📅 Sprint Planner: Generate demo script and review agenda based on sprint data
- [ ] 📢 Stakeholder Communicator: Draft review notes with accomplishments and carry-over items

### Example 5: Complex PO orchestration

**User**: "We're behind on the dashboard epic. Check Jira for the current state, see what was discussed in Slack, plan what we can realistically fit in next sprint, and draft a status update for leadership."
**Task Plan**:
- [ ] 🎫 Jira Researcher: Pull dashboard epic status, remaining stories, blockers, and velocity trends
- [ ] 💬 Slack Researcher: Find recent discussions about dashboard delays, decisions, and scope changes
- [ ] 📅 Sprint Planner: Recalculate capacity and plan next sprint with realistic scope based on findings
- [ ] 📢 Stakeholder Communicator: Draft executive status update with current state, risks, revised timeline, and decision asks

### Example 6: PO daily workflow

**User**: "I need to create tickets for the new search feature — check what we agreed on in Slack and what's already in Jira, then create the missing stories"
**Task Plan**:
- [ ] 🎫 Jira Researcher: Check existing tickets related to search feature
- [ ] 💬 Slack Researcher: Find discussions and decisions about search feature scope
- [ ] 🎫 Jira Ticket Creator: Create stories for agreed-upon work not yet captured in Jira

### Example 7: QA coverage orchestration

**User**: "Check the stories in the current sprint and create a test plan covering them"
**Task Plan**:
- [ ] 🎫 Jira Researcher: Pull all stories in the current sprint with their acceptance criteria
- [ ] 🧪 Test Plan Creator: Create a structured test plan covering all retrieved stories

### Example 8: Feature story with Pendo tracking

**User**: "Create a story for the new pricing page redesign — make sure we track what we need in Pendo"
**Task Plan**:
- [ ] 📄 Confluence Researcher: Pull PLG Instrumentation Plan to identify relevant pricing-related track events
- [ ] 📊 Pendo Tracker: Determine which track events apply to the pricing page and generate the tracking section
- [ ] 🎫 Jira Ticket Creator: Create the story with both functional requirements and the Pendo tracking section

### Example 9: Story creation + Test Plan coverage

**User**: "Crea la story para el nuevo filtro de activos y también el test plan"
**Task Plan**:
- [ ] 🎫 Jira Ticket Creator: Create the story for the new asset filter feature
- [ ] 🧪 Test Plan Creator: Create a structured test plan applying the 21 QA guidelines, linked to the story

### Example 10: Complex story with full coverage

**User**: "Tengo la nueva story de exportación CSV lista — crea el ticket, el test plan y asegúrate de que tiene tracking en Pendo"
**Task Plan**:
- [ ] 📄 Confluence Researcher: Pull PLG Instrumentation Plan to identify relevant export-related track events
- [ ] 📊 Pendo Tracker: Determine which track events apply to CSV export
- [ ] 🎫 Jira Ticket Creator: Create the story with Pendo tracking section
- [ ] 🧪 Test Plan Creator: Create test plan with output format validation, security tests, and automation level per the 21 QA guidelines

### Example 11: Standalone Pendo instrumentation

**User**: "What Pendo track events should we add for the new onboarding flow?"
**Action**: Route directly to 📊 Pendo Tracker. No orchestration needed — single-skill request focused on determining tracking requirements.

### Example 12: Sprint with instrumentation coverage check

**User**: "Plan next sprint and make sure all the new UI stories have their Pendo tracking defined"
**Task Plan**:
- [ ] 🎫 Jira Researcher: Pull backlog candidates for next sprint
- [ ] 📊 Pendo Tracker: Review each UI-facing story to identify missing Pendo tracking sections
- [ ] 📅 Sprint Planner: Plan sprint including instrumentation work alongside feature work
- [ ] 📢 Stakeholder Communicator: Draft sprint plan summary highlighting tracking coverage

## Principles

- **Gather before generating** — always retrieve context from tools before creating content
- **Verify before reporting** — check Jira data before writing status updates
- **One sprint at a time** — focus on tactical, sprint-level decisions
- **Templates are non-negotiable** — always use the Jira Ticket Creator and Test Plan Creator templates for issue creation; content must be human prose, never metadata tables or AI analysis notes
- **Right-skill, right-task** — every subtask should be handled by the skill best equipped for it
- **Maintain coherence** — the final output should feel like one integrated deliverable, not separate chunks
- **Adapt on the fly** — if mid-execution you discover the plan needs adjust, adjust it and note the change
- **Respect the user's time** — don't orchestrate when direct execution would be faster and equally effective