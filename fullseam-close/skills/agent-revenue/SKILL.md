---
name: agent-revenue
description: Set up an agent for revenue and deferred revenue — the month's revenue worked out from what was billed and delivered, the deferred balance rolled forward, and the entry, tied to the books. Read with `agent-setup` when someone wants an agent for revenue recognition or deferred revenue.
---

# A revenue agent

`agent-setup` is how any agent is set up. This is what revenue work has in
common wherever it is done. What is particular to the company goes in its
notes and in the agent's instructions, not here.

## The work

Each month:

- Take what was billed and what was delivered, from the billing system or the
  contracts.
- Work out the month's revenue for each contract or obligation.
- Roll the deferred revenue balance forward: billed, less recognized.
- Draft the entry where the team books one, and tie revenue, deferred revenue
  and unbilled receivables to the books.

## Look before asking

- The revenue, deferred revenue and unbilled accounts (`books_accounts`),
  their balances over the last few months (`books_balances`), and last
  month's entries (`books_entries`).
- Whether revenue is recognized by an entry someone books, or posted by the
  billing system or the ledger.
- The billing source the company is connected to.
- The work as FullSeam holds it: the task or rec, its last finished files
  (`task_get(view=prior)`, `rec_get`), who prepared and reviewed it.
- The company's notes under `policies/revenue`.

## What to establish

Work each out from the last few months' schedules and entries, then confirm
them together (`agent-setup`). Ask cold only what they do not show.

1. **The schedule.** Where last month's is, and which copy was reviewed.
2. **What is sold, and when it is earned.** Subscriptions over their term,
   services as delivered, one-time fees at a point. From how last month's
   schedule spread each kind.
3. **The convention.** A term that starts mid-month: by day, the full month,
   or from the next.
4. **The source of truth.** The billing system, the contracts, or a sheet
   someone keeps; and what decides when they disagree.
5. **Where it goes.** Where the schedule is saved and named, which task or rec
   it is attached to, who reviews it.

## The shape it ends in

- A schedule rolled from last month's, with each contract's
  revenue for the month.
- Checks it reports, each with its figures:
  - deferred revenue opening, plus billed, less recognized, equals closing,
    and equals the books;
  - the month's revenue equals what was posted, or the entry drafted;
  - every invoice billed this month is on the schedule.
- Instructions that say when to stop and ask: a contract whose term or price
  cannot be found; a credit or refund; billing that disagrees with the
  contract. A difference is a finding, never a plug.

## Testing it

Test it on the last month the team finished. Differences on revenue usually
come from a mid-month start read differently, a contract changed partway, or
a credit counted in a different month. Say which each one is.
