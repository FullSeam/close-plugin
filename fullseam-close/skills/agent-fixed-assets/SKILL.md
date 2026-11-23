---
name: agent-fixed-assets
description: Set up an agent for fixed assets — the roll-forward of cost and accumulated depreciation, the month's additions and disposals, the depreciation entry, tied to the books. Read with `agent-setup` when someone wants an agent for fixed assets or depreciation.
---

# A fixed assets agent

`agent-setup` is how any agent is set up. This is what fixed asset work has in
common wherever it is done. What is particular to the company goes in its
notes and in the agent's instructions, not here.

## The work

Each month, for each entity:

- Start from last month's fixed asset register or roll-forward.
- Add the month's additions: purchases capitalized, and construction in
  progress placed in service.
- Take off disposals, with their cost, accumulated depreciation and any gain
  or loss.
- Take each asset's depreciation for the month.
- Tie cost and accumulated depreciation, by class, to their accounts in the
  books.
- Where the team books depreciation, draft the entry: the expense debited,
  accumulated depreciation credited.

## Look before asking

- The asset, accumulated depreciation, depreciation expense and construction
  in progress accounts (`books_accounts`), their balances over the last few
  months (`books_balances`), and last month's entries to them
  (`books_entries`).
- Whether depreciation is one entry a month someone books, or posted by the
  ledger's own fixed asset module. That decides whether the agent drafts an
  entry or proves the ledger's.
- The work as FullSeam holds it: the task or rec, its last finished files
  (`task_get(view=prior)`, `rec_get`), who prepared and reviewed it.
- The company's notes under `policies/fixed-assets`.

## What to establish

Work each out from last month's register and entries, then confirm them
together (`agent-setup`). Ask cold only what they do not show.

1. **The register.** Where last month's is, and which copy was reviewed. The
   agent rolls it forward and does not redesign it.
2. **Capitalization threshold.** From the smallest item capitalized and the
   larger ones expensed to the same kinds of account.
3. **Classes and useful lives.** From each asset's cost, life and monthly
   depreciation on the register.
4. **Method and convention.** Straight-line or other; the first month
   depreciated (the month placed in service, the month after, half a month),
   from when depreciation started on last month's additions.
5. **Additions.** Where they come from: bills coded to the asset accounts, a
   list someone keeps, construction in progress. What decides an item is
   placed in service.
6. **Disposals.** How they are learned of, and how a gain or loss is booked.
7. **Where it goes.** Where the finished register is saved and named, which
   task or rec it is attached to, who reviews it.

## The shape it ends in

- A script kept with the agent that rolls last month's register forward,
  changing only what changes each month and leaving formulas as formulas.
- Checks it reports, each with its figures:
  - cost and accumulated depreciation by class equal the books';
  - the month's depreciation equals what the ledger posted, or the entry
    drafted;
  - every debit to an asset account this month is an addition on the
    register, or said not to be;
  - no asset depreciated past its cost or its life.
- Instructions that say when to stop and ask: an item whose class or life
  cannot be told; a purchase near the threshold; a disposal nobody confirmed;
  depreciation in the books the register cannot explain. A difference is a
  finding, never a plug.

## Testing it

Test it on the last month the team finished. Differences on fixed assets
usually come from the first month's convention, a life read differently, an
addition placed in service a month apart, or rounding in an asset's last
month. Say which each one is.
