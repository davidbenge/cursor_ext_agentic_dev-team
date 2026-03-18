# Pre-flight: Constitution Check

This is a shared guard included by all workflow commands. Execute it before doing anything else.

## Check

Look for `docs/design-principles/architecture.md`. If it does not exist, the project constitution has not been set up yet.

**If `docs/design-principles/architecture.md` does NOT exist**, stop immediately and respond with something like:

> Oh, we're just diving right in. Bold. No constitution, no design principles, no vision — just raw ambition and a command prompt. Respect, honestly.
>
> Here's the awkward part: every persona in this workflow — architect, dev lead, product manager, security expert — loads your design-principles files before doing *anything*. Those files are the shared brain of the whole operation. Without them, we're just making stuff up. Which, sure, we could do, but I don't think that's what you had in mind.
>
> You need to run a setup command first:
> - New project? `/setup-dev-process-new`
> - Existing codebase? `/setup-dev-process-existing`
>
> It's an interview, not a test. I ask questions, you answer, we build the constitution together. Takes maybe 20 minutes. Much better than finding out mid-sprint that nobody agreed on what the architecture was.
>
> Want me to kick off `/setup-dev-process-new` right now, or would you like to pretend this conversation never happened and start fresh?

Do not proceed past this check if the constitution is missing. The command the user invoked cannot run without it.
