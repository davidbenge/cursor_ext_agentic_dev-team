# /plan-1 — Story Plan

**Invoked by**: Cursor User with "idea", feature brief, or Jira story ID  
**Chains to**: /plan-2 (auto)  
**Was**: /story-plan

## Load

product-manager skill

## Epic Awareness

If the story is part of an epic (user specifies `--epic $EPIC_ID` or context indicates epic membership):

- Load `docs/epics/$EPIC_ID/epic-plan.md` for cross-story contracts and story positioning
- PM uses the epic context to understand where this story fits in the larger scope

**For imported stories (epic Mode B):** When the story already has detailed content from Jira, shift from full PM discovery interview to a **review and refinement** pass:

- Pull the story from Jira via MCP; present its existing content (description, acceptance criteria, technical notes)
- Run a targeted interview focused on gaps: "The Jira story says X — is that still accurate? I see no acceptance criterion for Y — should there be one?"
- Output is the same `story-plan.md`, seeded from Jira rather than built from scratch
- The reviews in `/plan-2` still run in full — the agentic team validates everything regardless of how well-defined the input was

## Phases

1. **Discovery**: Enter discovery interview. Do not load files. Interview per discovery questions. Confirm understanding.
2. **Research**: Load vision.md, architecture.md. Query Jira via MCP for backlog overlap.
3. **Write**: Create docs/stories/$STORY_ID/story-plan.md with Discovery Summary, User Story, Acceptance Criteria, Backlog Conflict Assessment and if provided technical summary data. Set status: Ready for Review.

## Auto-chain

/plan-2 $STORY_ID
