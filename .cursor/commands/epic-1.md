# /epic-1 — Epic Plan

**Invoked by**: Cursor User with epic ID (from Jira) or feature brief  
**Chains to**: /epic-2 (auto)

`/epic-1` has two entry modes, determined by what the user provides. Both modes converge on the same output: a reviewed, validated `epic-plan.md` with ordered stories and cross-story contracts.

**Mode detection**: If the user provides a Jira epic ID with child stories, use Mode B. If the user provides a brief or idea, use Mode A. If the Jira epic exists but has zero child stories, treat it as Mode A with the epic description as the brief.

---

## Mode A — Discovery (new epic from idea/brief)

### Load

product-manager + architect skills

### Phase 1 — Discovery

PM enters discovery interview at epic scope. This is broader than a story interview — focuses on the full problem space, phased delivery, and user journey coverage. Very verbose output explaining each step for non-developer personas. Confirm understanding before proceeding.

### Phase 2 — Research

Load vision.md, architecture.md, all impl-log indexes. Query Jira via MCP for related epics, existing stories, and backlog overlap.

### Phase 3 — Decompose

Architect + Dev Lead break epic into stories:

- Story dependency ordering
- Cross-story interface contracts (API shapes, schemas spanning stories)
- Parallelization guidance (which stories can run concurrently, which are sequential)

### Phase 4 — Write

Create `docs/epics/$EPIC_ID/epic-plan.md` with:

- Epic Summary
- Story Breakdown with dependency ordering
- Cross-Story Contracts
- Acceptance Criteria (epic-level)

Set status: Ready for Review.

### Auto-chain

/epic-2 $EPIC_ID

---

## Mode B — Import (existing epic from Jira with defined stories)

When the user provides a Jira epic ID that already has child stories, the workflow shifts from "plan from scratch" to "import, review, and validate."

### Load

product-manager + architect skills

### Phase 1 — Import

Pull the epic and all child stories from Jira via MCP. For each story, capture:

- Title, description, acceptance criteria
- Story points (if set), labels, status, assignee
- Linked issues and dependencies

### Phase 2 — Present

Display the full imported epic structure to the user in a readable format:

- Epic summary and acceptance criteria
- Story inventory table (ID, title, status, completeness assessment)
- For each story: how well-defined is it? (has acceptance criteria? has technical detail? has clear scope?)
- Flag any stories that appear thin, ambiguous, or missing acceptance criteria
- Flag any missing cross-story contracts or implicit dependencies the Jira stories do not make explicit

### Phase 3 — Research and Alignment

Load vision.md, architecture.md, all impl-log indexes. PM and Architect assess:

- Does this epic align with the project vision and architecture?
- Are there backlog conflicts or overlap with existing/completed work?
- Are there architectural concerns the Jira stories did not address?
- Are there gaps in the story set? (missing stories needed to complete the epic)

### Phase 4 — Write

Create `docs/epics/$EPIC_ID/epic-plan.md` with:

- Epic Summary (from Jira, enhanced with alignment assessment)
- Story Breakdown (from Jira, with dependency ordering added by Architect)
- Cross-Story Contracts (extracted from story details or flagged as missing)
- Gap Analysis (stories that should exist but do not)
- Per-story readiness assessment (ready for `/plan-1` vs. needs refinement first)

Set status: Ready for Review.

### Auto-chain

/epic-2 $EPIC_ID
