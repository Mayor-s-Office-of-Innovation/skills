---
name: exposure-review
description: High-level check of any change that adds or alters something the outside world can reach — a page, route, API endpoint, form, upload, login or role change, or new stored personal data — for who can reach it, what they can do, and what abuse would cost. Run BEFORE building any new surface and again BEFORE opening a PR that adds one. Stays high level; pair it with lower-level security tooling. Written for feature owners who are not engineers.
user-invocable: true
---

# Exposure review

A feature can be correct line by line and still open a door, because the code did what it was asked and nobody asked *who* should be allowed to ask. This skill makes sure that question gets asked.

Stays high level. Lower-level checks (secrets in source, insecure patterns, dependency issues) belong to the security tooling that runs alongside this. Pairs with `write-path-review` (can it lose data).

## Before building — the brief

**The agent does this, not the human.** Work out the answers yourself from the feature request and the codebase, one or two plain-language sentences each. Questions 2 and 6 are judgment calls you can only assume, so label those answers as assumptions. Present all six to the owner and **stop until they confirm or correct them** before writing code. The owner's job is to react, not to fill in a form.

1. **What new things can the outside world reach?** Pages, endpoints, forms, uploads, webhooks.
2. **Who should be allowed to use each one?** Anyone on the internet / anyone logged in / a named role.
3. **What can they send or change?** And is it ever shown back to other people?
4. **What could they see or change that isn't theirs?**
5. **What new personal data is stored, and who can read it?**
6. **If someone abused this all day, what would it cost us?** Bills, spam in our name, leaked data, an outage.

If the answer to 2 is "anyone" for something that stores or changes data, change the design now.

## Before the PR — the review

1. **Inventory every new or changed surface** the diff adds: routes, forms, uploads, auth or role checks, new data fields.
2. **Review in a fresh context.** Run the audit in a subagent given only the diff, the inventory, and the checklist below — not the conversation. The agent that built the feature will review it as working.
3. **Audit each surface**, gather findings, don't fix yet.
4. **Write the findings doc** and assign the routing label.
5. **Apply only on confirmation.**
6. **Verify as a stranger:** try each surface with no session, with a different user's ID, with oversized or wrong-type input.

### Checklist — ask for every surface

- **Who can reach it, and where is that enforced?** Hidden in the UI is not enforced; the check must run on the server for every request.
- **What can they send?** Is size, type, and rate bounded? Is anything they send shown to other people?
- **Whose data can they touch?** An ID from the client is a guess anyone can change. Every read, update, and delete must confirm the record belongs to the caller. Lists must filter to the caller's own items.
- **What is exposed by default?** Responses returning more fields than the page uses, admin or debug routes left reachable, storage that is publicly listable.
- **Personal data.** Is each new sensitive field needed at all, kept out of logs and analytics, and shared with third parties only as far as required?
- **Abuse cost.** Unbounded uploads, emails, or expensive operations anyone can trigger.

## Output

Write `<change-name>-exposure-review.md` with:

- **Inventory table:** `surface | who should reach it | who actually can | touches others' data?`
- **Findings table:** `# | finding | severity | status`, then for each: file:line, what a stranger could do, what it costs us, proposed change.
- **Routing label** at the top:
  - `NEEDS TECHNICAL REVIEWER` — any Critical or High finding, or any change to login, roles, payments, or sensitive personal data regardless of findings.
  - `OK TO MERGE WITH OWNER SIGN-OFF` — only Medium/Low, all discussed.

Phrase every finding so the owner can answer it: *"Anyone with a link can currently see every record, not just their own. Should they be able to?"* — not *"missing ownership check on GET /records/:id."*

| Severity | Meaning |
|---|---|
| **Critical** | A stranger can read, change, or delete data without permission |
| **High** | A logged-in user can reach data or actions beyond their role |
| **Medium** | Abuse is possible but bounded (no rate limit, over-broad response) |
| **Low** | Hygiene |

"It works" says nothing about who it works for. When in doubt, route to a technical reviewer.
