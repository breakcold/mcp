# Action 2 — Auto-movement of contacts in the pipeline

Automatically advance contacts (or companies, or deals) through the user's Kanban-style pipeline based on what's actually happening in their conversations and the rest of the record.

## When this triggers

User says things like: "advance my pipeline", "move contacts forward based on activity", "update my Kanban", "auto-progress deals", "keep my pipeline in sync with my inbox".

## What to ask first — and what not to

### Always ask once (if ambiguous)

**Which object's pipeline?** Some users run pipelines on Person, others on Company, others on Deal — or several at once. If the user has stage-like fields on multiple objects:

> Should I auto-progress people, companies, or deals? You can pick more than one.

If only one object has a clear pipeline field, pick it silently.

### Don't ask about obvious stages

If the pipeline reads cleanly (e.g., `New → Contacted → Qualified → Proposal → Negotiation → Won` or `Lost`), don't ask the user what each stage means. Operate from common sense.

### Ask only on **unclear or non-standard terminology**

If a stage uses internal-jargon names you can't infer ("Wave 1", "Tier B Q3", "Bucket Charlie"):

> Quick check on the pipeline: what does "**{stage name}**" mean for you? Is it earlier or later than "**{adjacent stage}**"?

Ask in one batched message if multiple stages are unclear. Don't ask one at a time.

### Cache the stage order

After clarification (or your own inference), cache the **ordered list** of stage option IDs. Also flag any stages that are terminal or off-flow:
- **Won / Closed Won** — terminal positive.
- **Lost / Closed Lost / Rejected** — terminal negative.
- **On Hold / Paused / Stalled** — off-flow side-bucket.

These are not part of the forward direction.

## The directionality rule — never move backwards by default

**Pipelines flow forward.** A contact in "Interested" cannot be auto-moved back to "Cold Lead", even if their recent behavior looks lukewarm. The reasoning: auto-movement should only ever **reflect new positive signal**. Disengagement is handled by tasks (Action 1), not by reversing pipelines.

Exceptions:

- **Re-opening after Lost.** If a contact is in a terminal "Lost" stage and sends a new positive message, that's a signal worth surfacing — but don't auto-move them. Instead, create a `custom_activities_create` flagging it for the user, and surface it in the run summary:
  > **{Name}** is marked Lost but replied today — worth a look.
- **Explicit user instruction.** "Move all my Negotiation deals back to Proposal" is fine because the user said so.

## What signals advance a contact

A move forward needs **positive signal in the source data**. Examples:

| Source signal                                                                 | Suggests moving toward…             |
|-------------------------------------------------------------------------------|-------------------------------------|
| First reply received on cold outreach                                         | Cold → Contacted / Engaged          |
| Reply expresses interest, asks for more info, agrees to a call                | Contacted → Qualified / Interested  |
| Meeting happened (calendar event in linked conversation)                      | Qualified → Discovery / Demo done   |
| Proposal sent / link to proposal in outbound message                          | → Proposal                          |
| Pricing discussion, redlining, contract language                              | → Negotiation                       |
| "We're in" / "send the contract" / signed PDF                                 | → Won                               |
| "Not the right fit" / "going with someone else" / "we'll pass"                | → Lost                              |
| Reply asks to reschedule, deprioritize, "check back in Q4"                    | → On Hold                           |

These are **heuristics, not rules**. Use the actual message content from `inbox_messages_list` plus any record notes / custom activities to decide. When the signal is ambiguous or weak, **do not move** — keep the contact where they are and write a breadcrumb explaining the uncertainty.

## Playbook — exact tool sequence

For a one-shot run (then see "Schedule it" below):

1. **Discover** (if not cached):
   - Workspace, target object(s), the pipeline-stage field on each, full option list with order.
   - Member list (for owner attribution in breadcrumbs).

2. **Define the cohort.** All records on the target object **except** those in terminal stages (Won, Lost) unless the user explicitly includes them.

3. **For each candidate, gather signal from the record itself** — batch in parallel up to the rate limit:
   - `inbox_conversations_list` (record-scoped) → conversations linked **directly to this record**.
   - `inbox_messages_list` for the **most recent conversation(s)** (capped — usually 1–3 conversations per record is enough). Pull only the last 5–10 messages per conversation; you don't need the full history.
   - `notes_list` on the record → recent notes (last 5).
   - The record's own field values from `records_get` (already cached if you just did a list).

3b. **For Deal and Company records, also gather signal from linked People.** This step is **non-negotiable** when operating on Deals or Companies — at the end of the day, conversations happen with **people**, not with deals. Deals usually have zero conversations attached directly; their linked Contacts have all the conversation activity. Skipping this step is the most common false-negative in the skill: an active deal whose linked people are actively replying gets concluded "no signal" because the agent only looked at the deal itself.

   For each Deal or Company in the cohort:
   - Read the record's relation fields and find linked **Person** records. Use `crm_fields_list` to identify relation fields if not cached; use the field values from step 3 to get the linked IDs.
   - For each linked Person (cap at the **5 most recently updated** if there are many):
     - `inbox_conversations_list` on that Person.
     - `inbox_messages_list` for the most recent thread → last 5–10 messages.
   - **Aggregate the signal across all linked People** for this Deal/Company:
     - Most recent message timestamp (across all people on the deal).
     - Who sent it (inbound from one of the people, or outbound from the user to one of them).
     - Substance of the message (reply expressing interest, scheduling, pricing discussion, etc.).
   - For Companies specifically: also check linked Deals' direct conversations if any exist.

   The aggregated signal then flows into the decision in step 4.

4. **Self-check before deciding — exhaust signal sources.** Before assigning STAY / MOVE / FLAG to a record, verify:
   - ✓ Checked the record's own conversations (step 3).
   - ✓ If the record is a Deal or Company, checked conversations on all linked People (step 3b).
   - ✓ Checked recent notes for context.
   - ✓ Checked `custom_activities` for prior breadcrumbs (so you don't re-litigate a decision the previous run already made).

   If you find yourself concluding "no signal" on a Deal that the user has touched in the last week, or a Company with active linked People, **that's a hint you didn't exhaust the signal sources**. Re-check before writing the decision. The cost of a missed step here is producing a wrong-feeling automated update that breaks the user's trust.

5. **Decide the target stage.** Apply the heuristics in "What signals advance a contact" above to the **aggregated** signal (from steps 3 + 3b). For each record, output one of:
   - **MOVE** → target stage ID.
   - **STAY** → current stage.
   - **FLAG** → keep the record where it is, but write a custom activity surfacing the signal for human review (used for re-opening Lost deals, ambiguous wins, etc.).

6. **Apply the directionality rule.** A MOVE is only valid if the target stage index ≥ current stage index in the cached ordering. If not, convert it to a FLAG.

7. **Apply moves.** For each MOVE:
   - `records_update` on the stage field with the new option ID.
   - `custom_activities_create` with prefix:
     ```
     [breakcold-crm:auto-pipeline {YYYY-MM-DD}] Moved {From} → {To}. Signal: {one-line reason quoting the trigger, naming the person and channel where the signal came from}.
     ```
     This is the shared memory between runs — the next run will see why the previous run moved this contact and won't second-guess.

8. **Apply flags.** For each FLAG, write the custom activity only (no field change). Roll these into the run summary so the user can review.

9. **Summarize**:
   > Moved **{N}** contacts forward, flagged **{M}** for review, left **{K}** unchanged. Biggest movers: **{name}** ({From} → {To}), **{name}** ({From} → {To})…

## Optional but valuable: the breadcrumb-as-memory pattern

Always write a `custom_activities_create` for every decision (MOVE, STAY-with-strong-signal, FLAG). The user said: *"if you believe that creating a custom activity with explanations for both the user and yourself as mini extended context memory can help you for future updates and let the user know why you did what you did, then do it without asking."*

Do it. It's the difference between an automation that gets smarter every run and one that re-litigates the same decisions.

## Edge cases

- **Record has no pipeline stage set at all.** Treat as "new / unassigned". If signal is positive, move to the first non-empty forward stage (usually "Contacted" or "Engaged"). If no signal, leave it but breadcrumb that the stage is empty.
- **Two pipelines on the same object.** Rare but possible (e.g., a Person object with both a sales stage and a partnership stage). Ask the user once which to operate on, then cache the answer.
- **Stage option deleted between runs.** A record may sit on an option ID that no longer exists. Treat as "unknown stage" and surface in the run summary; don't fail silently.
- **Custom object pipelines.** Run the same logic on custom objects with a clear single-select stage field — no special casing required. If the custom object is a "parent of People" (like a project or campaign), apply step 3b — check conversations on linked People.
- **A contact replies after being moved to Won.** Don't move — they're already at the top. Surface in the summary if the reply contains a complaint or churn signal (e.g., "cancel", "refund", "unhappy") so the user can intervene.
- **A contact replies after being moved to Lost.** See the re-opening rule above — flag, don't move.

## Schedule it as a routine

Hand off to `routines.md`. The confirmation message becomes:

> I'll review the **{object(s)}** pipeline **{frequency}** and move contacts forward based on activity. I'll never move anyone backwards — disengagement gets handled by follow-up tasks instead. Want a **{matching cadence}** email recap?

Default frequency: **daily**. Acceptable: daily to every 3 days. More frequent than daily triggers the cost warning from `routines.md`.

## How this plays with Action 1 (follow-up tasks)

These two actions are **complementary**:

- Action 1 (tasks) handles contacts who are **falling out of momentum** — generates work for the user.
- Action 2 (pipeline) handles contacts who are **gaining momentum** — keeps the visual state of the CRM honest.

Run them in this order if both are scheduled the same day: **pipeline first, tasks second**. That way the task pass sees the freshest stage data (e.g., a contact just moved to Won won't get a "follow up" task created for them).
