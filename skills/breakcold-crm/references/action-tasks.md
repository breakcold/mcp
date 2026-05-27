# Action 1 — Multichannel auto-task creation

Create follow-up tasks for contacts where the user is losing momentum, across email, LinkedIn, WhatsApp, Telegram, and meetings. Every task links back to the actual conversation so the user opens the task and lands inside the thread.

## When this triggers

User says things like: "follow up with the cold ones", "create tasks for people who went quiet", "ping anyone I haven't heard from in two weeks", "set up daily follow-ups", "task me on the deals that lost momentum".

## What "losing momentum" means

There is no universal definition. Ask the user once, in plain language:

> What counts as losing momentum for you? Should I create a follow-up task when there's been no reply for **3 days**, **7 days**, **14 days**, or something else?

Cache the answer for the session. Default if the user can't decide: **7 days**.

The reference is the **last inbound or outbound message** across any linked conversation channel — email, LinkedIn, WhatsApp, Telegram, meeting note. **Use the most recent across all channels**, not just one.

## Assignee — ask, default to the requesting user

Ask the user once who should own these tasks:

> Who should own these follow-up tasks? **You** (default), **{Member A}**, **{Member B}**, or **the existing record owner** on each?

Defaults if the user doesn't answer or says "you decide":

- **The requesting user** (the one who triggered the workflow). This is the default and the safest fallback — they're the one running the workflow and the one who will see the tasks in their list. Use `CURRENT_USER_ID` from the boot sequence (see `SKILL.md` § Session-start orientation).
- Single-member workspace: that one member (same person as the requesting user).
- Only fall back to "the existing record owner" if the user explicitly chooses it.

**Never** create tasks with a null/empty assignee. Unassigned tasks vanish from everyone's queue. If `CURRENT_USER_ID` is somehow still unknown when the first task fires (rare), create one task with the cleanest fallback you have, read the user's ID from the create response, and use it for all subsequent tasks in the batch — see `fundamentals.md` § "Resolving the current user ID".

## Playbook — exact tool sequence

For a **one-shot run** (not a routine yet):

1. **Discover** (if not cached):
   - `workspaces_list` → workspace ID.
   - `crm_objects_list` → IDs for Person, Company, Deal (and any custom records the user wants in scope).
   - `crm_fields_list` for those objects → field IDs.
   - Member list — get from `capabilities_list` or workspace context.

2. **Define the cohort.** Decide which records to consider. By default:
   - The object type is **Person** (most users follow up with people). If the user manages deals or companies primarily, run on the relevant object instead — ask once if unclear.
   - **Exclude** records in pipeline stages that mean "lost", "closed lost", "rejected", "do not contact" — read those from the stage options. (When in doubt about a custom stage name, check `crm_field_options_list` and judge from the label; if unclear, ask.)
   - **Include** all other records that have at least one linked conversation.

3. **For each candidate record**, fetch in this order — and **batch in parallel up to the rate limit**:
   - `inbox_conversations_list` (record-scoped) → all linked conversations.
   - For the **most recent conversation** (across all channels), `inbox_messages_list` with a small page → just enough to find the timestamp of the most recent message.
   - Compute days since last message. If ≥ the user's threshold, the record qualifies.

4. **Idempotency check.** Call `tasks_list` on the record. **Skip** if any open task on this record has:
   - A title that contains "follow up", "ping", "check in", or similar (case-insensitive), **and**
   - A due date within the next 7 days.

5. **Create the task.** Call `tasks_create` with:
   - **record_id:** the record being followed up on.
   - **title:** follow the pattern below — see "Task title pattern" later in this file for full rules.
     ```
     Follow up {first name} - {2-6 word topic}
     ```
     Examples:
     - `Follow up Katy - Pricing concerns around tokens`
     - `Follow up Sarah - Q4 contract terms`
     - `Follow up Mike - SOC 2 questions`
   - **due_date:** today (or next business day if today is weekend; do not auto-schedule weeks out).
   - **assigneeUserIds:** array containing the cached `CURRENT_USER_ID` (or whoever was selected during setup). **Never null, never empty.**
   - **conversation/thread link:** if the API accepts a conversation reference, include the conversation ID from step 3 so the task deep-links into the thread when opened. If only a record link is supported, the conversation must already be linked to the record (which it is, since you found it via `inbox_conversations_list`), and that's enough — opening the task takes the user to the record where the conversation lives.

6. **Breadcrumb.** Call `custom_activities_create` on the record with prefix:
   ```
   [breakcold-crm:auto-tasks {YYYY-MM-DD}] Follow-up task created; last activity {N} days ago ({channel}).
   ```

7. **Repeat** for the cohort, respecting rate limits (see `fundamentals.md`).

8. **Summarize** to the user in one short message:
   > Created **{N}** follow-up tasks across **{N}** contacts. Quietest thread: **{name}** at **{N} days**. Skipped **{M}** records that already had open follow-up tasks.

## Edge cases

- **No conversations on a record.** Skip — can't follow up on a thread that doesn't exist. (Don't create a "cold outreach" task unless the user explicitly asks for that — different action.)
- **Multiple conversations, multiple channels.** Use the **most recent message timestamp across all channels** to compute "days quiet". Derive the topic from the messages in that most-recent thread — that's where the live context is.
- **The most recent message is from the user (outbound).** That's *also* "losing momentum" — the user reached out and got nothing back. Still create the task; the topic comes from what the user asked or proposed in that unanswered message (e.g., `Follow up Katy - Demo scheduling`, `Follow up Mike - Proposal review`).
- **The most recent message is from the contact (inbound) and the user hasn't replied.** That's a "you owe them a reply" situation, which is more urgent. Use the `Reply needed` prefix — see "Special case — pending replies" in the Task title pattern section. Set due date to **today** with high priority if the API supports it.
- **Record is in a "won" or "closed" stage.** Skip unless the user explicitly asks to follow up with closed customers (different intent — that's usually an upsell or check-in routine).
- **Record has no owner and the workspace is multi-member.** Use the assignee chosen in setup (default: the requesting user). Don't silently leave it unassigned.
- **Conversation is a group thread (multiple participants).** The task still links to the record; if multiple records share the same conversation, create one task per record only if the user wants distinct follow-ups — otherwise create one task on the most relevant record (usually the deal, falling back to the primary contact).
- **The contact opted out / unsubscribed.** If a custom field or status marks this (look for "unsubscribed", "DNC", "do not contact"), skip and breadcrumb it.

## Schedule it as a routine

If the user wants this on a schedule, hand off to `routines.md` for the universal setup. The one-confirmation message becomes:

> I'll create follow-up tasks **{frequency}** for contacts where the last conversation activity is older than **{threshold}** days, assigned to **{assignee}**. Want a **{matching cadence}** email recap of what got created?

Default frequency: **daily**. Acceptable range: daily–weekly. Anything more frequent triggers the cost warning from `routines.md`.

## Task title pattern

Every follow-up task title follows this pattern:

```
Follow up {first name} - {2-6 word topic}
```

Examples that work:
- `Follow up Katy - Pricing concerns around tokens`
- `Follow up Sarah - Q4 contract terms`
- `Follow up Mike - Demo follow through`
- `Follow up Jen - SOC 2 questions`
- `Follow up Ahmed - Custom domain setup`

Examples that don't (avoid these):
- ❌ `Follow up` — no name, no topic.
- ❌ `Follow up Katy` — no topic.
- ❌ `Follow up Katy - email thread went quiet (9 days)` — channel + duration is implementation detail, not topic.
- ❌ `Follow up Katy - Discussion about various topics` — generic filler; says nothing.
- ❌ `Follow up Katy - Pricing` — too thin, expand to a real 2-6 words.

### How to derive the topic

1. Pull the **last 3-5 messages** in the most recent conversation thread on this record (across whichever channel is most active).
2. Identify the **dominant subject** — what they were actually discussing in concrete nouns. "Pricing for the enterprise tier" → "Pricing concerns" or "Enterprise tier pricing" depending on the angle the user was wrestling with.
3. Compress to **2-6 words**.
4. **Avoid empty connector words:** "discussion", "questions about", "topic of", "various things". The 2-6 words should be **concrete nouns** doing the work.
5. **Pick the angle that matters to the user**, not the literal subject line. If the customer's email subject was "Re: Re: Re: Fwd: Quick question" but the actual content is about contract length, the topic is "contract length" — read the body, not the subject.

### When you can't derive a clear topic

Very short conversations (one-line "hey are you free?" with no reply) won't yield a topic. Fall back:

```
Follow up {first name} - {channel} thread ({N} days)
```

E.g., `Follow up Katy - email thread (9 days)`. This is the **only** acceptable fallback. Don't ship `Follow up - email thread went quiet (9 days)` (the old pattern) — at minimum, include the person's name.

### Language

**Match the user's working language.** If the conversation and the user's other workspace content are in French, write the title in French:

- `Relancer Katy - Préoccupations sur les tokens`
- `Relancer Sarah - Termes du contrat Q4`

Same for Spanish (`Seguimiento ...`), German (`Nachfassen ...`), Portuguese (`Acompanhar ...`), etc. Mirror the user's language for the prefix and the topic; keep the dash separator. See `SKILL.md` § Universal rules → "Mirror the user's language" for the broader rule.

### User override

If the user explicitly requested a different naming pattern at workflow start ("Use 'Reach out' instead of 'Follow up'", "Don't include the name", "Use the format X"), follow their pattern instead. They're the boss; the pattern above is the default, not a constraint.

### Special case — pending replies (user owes the contact)

When the most recent message is from the contact (inbound) and the user hasn't replied, the task is more urgent and the framing changes — the user owes a reply, not a follow-up. Use:

```
Reply needed - {first name} - {2-6 word topic}
```

Examples:
- `Reply needed - Katy - Pricing concerns around tokens`
- `Reply needed - Sarah - Question about onboarding`

Set the due date to today and bump priority if the API supports it.

### Length sanity

Keep the whole title under ~70 characters so it reads cleanly in the Breakcold task list. If your topic compression pushes the title past that, tighten further — usually the topic was still too verbose.
