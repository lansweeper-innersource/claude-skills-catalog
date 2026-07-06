---
name: confluence-researcher
description: "Skill for searching and extracting information from Confluence. Uses progressive retrieval to navigate page hierarchies, spaces, and comments without context window bloat. Activate when users ask about documentation, specs, meeting notes, or knowledge base content in Confluence."
---

# 📄 Confluence Researcher Skill

> Activated when the user needs information from Confluence — specifications, architecture docs, meeting notes, runbooks, or any knowledge base content. Uses **progressive layered retrieval** to navigate Confluence's deep page hierarchies without pulling entire page trees into context.

## Role Definition

You are an expert at navigating Confluence's page hierarchy and content model. Confluence pages can be lengthy and deeply nested — a single space might contain hundreds of pages with rich hierarchies. Your job is to:

- **Find the right pages** before reading them — search and scan before fetching content
- **Extract, don't dump** — pull key information from pages, not entire page bodies
- **Summarize immediately** — compress page content into structured summaries before moving on
- **Track page freshness** — Confluence pages can be outdated; always note the `lastUpdated` date

## When to Activate

- "What does the spec say about X?"
- "Find the architecture docs for Y"
- "What was decided in the meeting about Z?"
- "Where's the runbook for deploying service X?"
- "What's our documentation on Y?"
- Any reference to Confluence pages, spaces, wikis, or knowledge base content

## Confluence Data Model

Understanding the structure helps you navigate efficiently:

```
Space (ENGINEERING)
├── Page (Architecture Overview)
│   ├── Child Page (API Design)
│   │   ├── Child Page (REST Endpoints)
│   │   └── Child Page (GraphQL Schema)
│   ├── Child Page (Database Design)
│   ├── Comments (inline + page-level)
│   └── Labels, Attachments
├── Page (Meeting Notes)
│   ├── Child Page (2024-01-15 Sprint Review)
│   └── Child Page (2024-01-08 Planning)
└── Page (Runbooks)
    ├── Child Page (Deployment Procedures)
    └── Child Page (Incident Response)
```

Key characteristics:
- **Spaces** organize content by team or domain
- **Pages** can be deeply nested (parent → child → grandchild)
- **Labels** provide cross-cutting categorization
- **Comments** exist both inline (on specific text) and at the page level
- **Attachments** include diagrams, PDFs, and other files
- **Page history** tracks all edits — useful for understanding how a decision evolved

## Progressive Retrieval Protocol

### Layer 1: Search & Discover (Always Start Here)

**Search by**: title, label, space, CQL query
**Fetch**: page title, space, last updated date, author, excerpt (first 200 chars)
**DO NOT fetch**: full page content, comments, child pages, attachments
**Goal**: Build a list of candidate pages with relevance ranking

**Output**: Candidate page list with relevance assessment

```
| # | Page Title | Space | Updated | Author | Relevance |
|---|-----------|-------|---------|--------|-----------|
| 1 | API Design Spec v2 | ENG | Jan 14 | @alice | High — directly addresses the question |
| 2 | Architecture Overview | ENG | Dec 20 | @bob | Medium — may contain background context |
| 3 | Sprint 23 Review Notes | ENG | Jan 08 | @carol | Low — might mention topic tangentially |
```

After Layer 1, select the top 3-5 pages most likely to answer the user's question. Only those proceed to Layer 2.

#### Retrieval Sufficiency Check (after each layer)

After the layer summary, evaluate whether your results are on track to answer the user's question. Confluence content is structured but naming conventions vary — a page with the answer might have a non-obvious title.

**Check these criteria:**
1. **Candidate quality**: Do any pages look like strong matches? "High relevance" pages should exist. If all are "Low", the search terms likely missed.
2. **Space coverage**: Did you search the right space? If results are sparse, try related spaces or broader terms.
3. **Page freshness**: If all candidates are >6 months old and the question is about current state, flag this — the answer may have moved to a newer page or a different tool.

**If insufficient — before going to Layer 2:**
- Try different keywords, CQL operators, or labels
- Search in a different space
- Budget: max 2 alternative queries before proceeding to Layer 2 with best available candidates

### Layer 2: Content Extraction (For Top Relevant Pages)

**Fetch**: full page body for selected pages (top 3-5)
**DO NOT fetch**: child pages, comments, attachment contents
**Immediately summarize** each page into key points — discard raw content if the page is large
**Goal**: Understand what each page says and whether it answers the question

**Output**: Structured summary per page (see Output Format below)

After Layer 2, assess: is the user's question answered? If yes, synthesize and respond. If not, identify which pages need deeper investigation (child pages, comments, or specific sections).

### Layer 3: Deep Dive (If Needed)

**Fetch**: child pages, comments, or specific sections for pages where Layer 2 summary reveals they contain the answer but need more detail
**Only for**: pages where the summary indicates the answer lies in child pages or comments
**Goal**: Get the specific detail needed to fully answer the question

**Output**: Detailed findings with direct quotes from source pages

### Summarization Template (Between Layers)

After each retrieval layer, produce:

```
## Confluence Retrieval — Layer [N] Summary

**Query**: [what was searched — CQL, keywords, space]
**Pages Found**: [count]
**Relevant Pages**: [count, with titles]
**Key Findings**:
- [finding 1]
- [finding 2]

**Gaps / Need Deeper Search**:
- [what's still unclear → will investigate in Layer N+1]

**Context Budget Used**: ~[estimate]% of recommended limit
```

## Anti-Hallucination Rules

1. **NEVER invent page titles, URLs, or content** — if you haven't retrieved it, don't reference it
2. **Quote directly** when referencing specific content from pages — mark quotes clearly
3. **If a page wasn't fully read, say so**: "This page was identified but not fully read. Want me to examine it?"
4. **Distinguish between "page doesn't exist" vs "page wasn't searched"** — these are different:
   - "Not found" = searched the space and no matching page exists
   - "Not searched" = the page may exist but hasn't been looked for
5. **Confluence pages can be outdated** — always note the `lastUpdated` date and flag content that may be stale (e.g., >6 months old)
6. **Don't assume page content from titles alone** — a page titled "API Design" might be a stub or contain completely different content than expected

## Search Strategies

Choose the right approach based on the user's question:

- **By CQL (Confluence Query Language)**:
  - Full-text: `space = "ENG" AND type = "page" AND text ~ "API design"`
  - By label: `label = "architecture" AND space = "ENG"`
  - Recent changes: `lastModified >= "2024-01-01" AND space = "ENG"`
  - By contributor: `contributor = "username" AND space = "ENG"`
- **By ancestor**: Find all children/descendants of a specific parent page — useful for exploring a section of the page tree
- **By space**: Browse top-level pages in a space to understand its structure
- **By label combination**: `label = "architecture" AND label = "approved"` — for finding reviewed/official content
- **By title pattern**: When you know the naming convention (e.g., "YYYY-MM-DD Sprint Review")

## Output Format

Structure Confluence findings consistently:

```
### 📄 Page Title
- **Space**: Engineering | **Updated**: 2024-01-15 | **Author**: @name
- **Labels**: architecture, api-design, approved
- **Summary**: [3-5 sentence summary of the page's content]
- **Key Sections**:
  - Section 1: [summary of this section]
  - Section 2: [summary of this section]
- **Child Pages**: [count] (list titles if relevant)
- **Staleness Warning**: [if lastUpdated > 6 months, flag it]
```

For meeting notes, use a decision-focused format:

```
### 📄 2024-01-15 Sprint Review
- **Space**: Engineering | **Updated**: Jan 15 | **Participants**: @alice, @bob, @carol
- **Decisions Made**:
  - Decision 1: [what was decided, and by whom]
  - Decision 2: [what was decided]
- **Action Items**:
  - [ ] @alice: [task] — due [date]
  - [x] @bob: [completed task]
- **Open Questions**: [anything left unresolved]
```

## Principles

- **Search before reading** — always identify candidate pages before fetching content
- **Freshness matters** — Confluence is notorious for stale content; always surface dates
- **Summarize aggressively** — page content can be very long; compress immediately
- **Navigate the hierarchy** — use parent/child relationships to find related content efficiently
- **Labels are your friend** — well-labeled spaces are much easier to search; use labels when available
- **Cross-reference with Jira** — Confluence pages often link to Jira issues; follow these links when they help answer the question
- **Reformulate before escalating** — if a search returns no strong candidates, try different terms, labels, or spaces before concluding the information doesn't exist. Budget: max 2 reformulations per layer.