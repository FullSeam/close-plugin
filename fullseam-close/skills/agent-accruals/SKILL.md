---
name: agent-accruals
description: Set up an agent for accrued expenses — what was received and not yet billed at month end, each estimate and what it rests on, the entry and its reversal. Read with `agent-setup` when someone wants an agent for accruals.
---

# An accruals agent

`agent-setup` is how any agent is set up. This is what accrual work has in
common wherever it is done. What is particular to the company goes in its
notes and in the agent's instructions, not here.

## The work

Each month:

- List what was received or used this month and not yet billed.
- Estimate each, from what it rests on: a contract, a run rate, a bill that
  came after month end, a usage report.
- Draft the entry: the expense debited, accrued liabilities credited, with
  each line's support.
- Check last month's accruals reversed, and how each one compared with the
  bill that came.
- Tie the accrued liability accounts to the schedule.

## Look before asking

- The accrued liability accounts (`books_accounts`), their balances over the
  last few months (`books_balances`), and the accrual and reversal entries
  (`books_entries`).
- Bills dated after month end for service in the month, where the ledger
  shows them.
- The work as FullSeam holds it: the task or rec, its last finished files
  (`task_get(view=prior)`, `rec_get`), who prepared and reviewed it.
- The company's notes under `policies/accruals`.

## What to establish

Work each out from the last few months' schedules and entries, then confirm
them together (`agent-setup`). Ask cold only what they do not show.

1. **The schedule.** Where last month's is, and which copy was reviewed.
2. **What recurs.** The accruals made every month, by vendor or kind, and how
   each was estimated: last bill, contract amount, average.
3. **The threshold.** The amount below which nothing is accrued, from the
   smallest line accrued and the bills that came late and were not.
4. **Reversals.** Whether the entry reverses itself next month, or is
   reversed by hand, or trued up.
5. **Where the late bills come from.** Payables after month end, a list from
   department heads, an inbox.
6. **Where it goes.** Where the schedule is saved and named, which task or rec
   it is attached to, who reviews it.

## The shape it ends in

- A schedule rolled from last month's: the recurring lines estimated by their
  rule, the one-off lines from what came in, each with its support.
- Checks it reports, each with its figures:
  - last month's accruals reversed, and each compared with the bill that came;
  - the schedule's total equals the accrued liability accounts after the
    entry;
  - every bill dated after month end for service in the month, above the
    threshold, is accrued or said not to be.
- Instructions that say when to stop and ask: an estimate with nothing to
  rest on; a recurring accrual whose bill stopped coming; a bill far from what
  was accrued. Never an estimate made up to fill a line.

## Testing it

Test it on the last month the team finished. Differences on accruals usually
come from a late bill counted or not, an estimate taken from a different
month, or the threshold. Say which each one is.
