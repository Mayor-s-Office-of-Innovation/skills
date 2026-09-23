---
name: write-path-review
description: Check any change that creates, updates, deletes, imports, syncs, or migrates data for silent data-loss risks (overwrites, double-submits, half-finished writes, two users at once, unrecoverable deletes, unsafe migrations). Run BEFORE building anything that writes data, and again BEFORE opening a PR that touches a save, edit, delete, import, sync, or migration. Written for feature owners who are not engineers — every finding is a plain-language question they can answer.
user-invocable: true
---

# Write-path review

Features that write data usually look fine: the demo saves, the PR reads cleanly line by line. Data loss hides in the **flow** — the write runs twice, fails halfway, races another user, or clobbers fields it didn't mean to touch. Trace flows, not lines.

Pairs with `exposure-review` (who can reach the feature).

## Before building — the brief

**The agent does this, not the human.** Work out the answers yourself from the feature request and the codebase, one or two plain-language sentences each. Question 6 depends on how the owner wants mistakes handled, so label that answer as an assumption. Present all six to the owner and **stop until they confirm or correct them** before writing code. The owner's job is to react, not to fill in a form.

1. **What gets written, and where?** Every table, sheet, file, bucket, or external service.
2. **Does it change or delete existing data, or only add new?**
3. **What happens if it runs twice?** Double-click, refresh, retry after a timeout, overlapping scheduled runs.
4. **What happens if it fails halfway?** If it writes to more than one place, what is left when step 2 fails after step 1 succeeded?
5. **What if two people do it at the same time?** Does one change silently replace the other?
6. **Can it be undone?** Is the old value recoverable, and by whom?

If any answer is "data could be lost" or "not sure," change the design now.

## Before the PR — the review

1. **Inventory every write path** the diff adds or modifies: what is written, where, what triggers it, whether existing data is touched.
2. **Review in a fresh context.** Run the audit in a subagent given only the diff, the inventory, and the checklist below — not the conversation. The agent that built the feature will review it as working.
3. **Audit each write path**, gather findings, don't fix yet.
4. **Write the findings doc** and assign the routing label.
5. **Apply only on confirmation.** Switching an overwrite to a merge is itself a behavior change the owner must approve.
6. **Verify the failure case**, not the happy path: submit twice, kill it halfway, edit from two tabs.

### Checklist — ask for every write path

- **Overwrite vs. merge.** Does an update save the whole record or only the changed fields? A form showing 5 of 12 fields that saves the whole object wipes the other 7.
- **Running twice.** Every trigger eventually fires twice. Duplicates, stale overwrite, or nothing? Retries after a timeout are the sneaky case — the first attempt often succeeded.
- **Partial failure.** For any multi-step write, if step N fails, what is already written? Is there a rollback or cleanup, or is the record left half-done with no signal?
- **Two users at once.** Read-then-write without a version check means last write wins and the earlier edit vanishes with no error.
- **Deletes.** Hard or soft? Is there a restore path someone actually knows? What does a cascade take with it?
- **Migrations, imports, bulk jobs.** Snapshot first? Dry run? Row counts before and after that reconcile? Safe to re-run after failing partway?
- **Validation before the write.** Bad input saved silently is data loss too. Is it checked on the server before it is persisted?
- **Two sources of truth.** Same fact written to two places? Which wins when they disagree?

## Output

Write `<change-name>-write-review.md` with:

- **Inventory table:** `write path | destination | trigger | touches existing data? | safe to run twice?`
- **Findings table:** `# | finding | severity | status`, then for each: file:line, the concrete scenario a real user could cause, what is lost, proposed change.
- **Routing label** at the top:
  - `NEEDS TECHNICAL REVIEWER` — any Critical or High finding, or any migration, bulk job, or hard delete regardless of findings.
  - `OK TO MERGE WITH OWNER SIGN-OFF` — only Medium/Low, all discussed.

Phrase every finding so the owner can answer it: *"If someone submits this form twice, the second copy replaces the first. Is that OK?"* — not *"handler is non-idempotent."*

| Severity | Meaning |
|---|---|
| **Critical** | Existing data can be lost or silently changed under normal use |
| **High** | Existing data can be lost under plausible use (retry, refresh, two users, job overlap) |
| **Medium** | New data can be saved wrong or incomplete; nothing existing is harmed |
| **Low** | Hygiene |

"It worked when I tried it" tests the happy path only. When in doubt, route to a technical reviewer.
