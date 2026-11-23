---
name: agent-prepaids
description: Set up an agent for prepaid expenses — the schedule of what was paid ahead, rolled forward each month and tied to the books, and the amortization entry where the team books one. Read with `agent-setup` when someone wants an agent for their prepaids.
---

# A prepaids agent

`agent-setup` is how any agent is set up. This is what prepaid work has in
common wherever it is done, so the questions start from it and the agent ends
in the same shape. What is particular to the company goes in its notes and in
the agent's instructions, not here.

## The work

Each month, for each entity's prepaid account:

- Start from last month's finished schedule.
- Add what was paid ahead this month: the debits to the account.
- Take each item's amortization for the month over its service period.
- Take off what is fully amortized.
- Tie the schedule's total to the account's balance in the books.
- Where the team books the amortization, draft the entry: the expense debited,
  the prepaid account credited.

## Look before asking

Find what is already there before the first question, and say it back:

- The prepaid accounts of each entity (`books_accounts`), their balances over
  the last few months (`books_balances`), and last month's entries to them
  (`books_entries`).
- Whether the credits are one entry a month that someone books, or many the
  ledger posts from its own amortization schedules. That decides whether the
  agent drafts an entry.
- The work as FullSeam holds it: a task or a reconciliation on those accounts,
  its last finished files (`task_get(view=prior)`, `rec_get`), and who prepared
  and reviewed it. Where that work already has an agent, change that one.
- The company's notes under `policies/prepaids` and `chart`.

## What to establish

Work each out from last month's schedule and entries, then confirm them
together (`agent-setup`). Ask cold only what they do not show.

1. **The schedule.** Where last month's is, and which copy was reviewed. It is
   the starting point and the format: the agent rolls it forward and does not
   redesign it.
2. **Who books the amortization.** Someone by journal entry, or the ledger from
   its own schedules. Where someone does, ask whether they want the agent to
   draft the entry too (`work-journal-entry`). Where the ledger does, the agent
   proves the ledger's amortization against the schedule and drafts nothing.
3. **New items.** How a new prepaid is found (a debit to the account, a bill
   coded to it, a list from payables) and where its service period comes from:
   the invoice, the contract or policy, or the ledger's amortization on the bill
   line. The agent never guesses a period.
4. **The rules.** The threshold below which a payment is expensed; when an
   item's first month is (the month service starts, the month after, or by
   day); where rounding goes. Each is a note under `policies/prepaids`.
5. **Standing differences.** A difference between the schedule and the books
   that is accepted month to month: its amount and why. A check passes at that
   difference, not within a tolerance made up for the agent.
6. **Typed lines.** Items carried as a typed amount instead of a computed row,
   and who decides when one comes off.
7. **Where it goes.** Where the finished schedule is saved and what it is named,
   which task or reconciliation it is attached to, and who reviews it.

## The shape it ends in

- A script kept with the agent that rolls last month's file forward. It changes
  only the cells that change each month and leaves formulas as formulas.
- Checks the script reports, each with its figures:
  - the schedule's total less the books' balance is the standing difference;
  - the month's amortization equals what the ledger posted, or the entry
    drafted;
  - every debit to the account this month is on the schedule;
  - no item is amortized past its cost or past the end of its service period.
- Instructions that say when to stop and ask: a period that cannot be found; a
  debit that is not a prepaid (a bill that will not be paid, a miscoding);
  amortization in the books the schedule cannot explain; an item coming off
  that nobody has decided on. A difference is a finding, never a plug.
- Each entity's schedule is its own, in its own currency.

## Testing it

Test it on the last month the team finished (`agent-setup`). Differences on
prepaids usually come from a period read differently, the first-month rule,
rounding in the last month, or an item added a month late. Say which each one
is.
