---
name: work-journal-entry
description: Rules for work that ends in a journal entry or in the schedule behind one — accruals, prepaid and other amortization, depreciation, reclasses, payroll entries. Load when a task's type is je_creation, or when asked to book, accrue, amortize, depreciate or reclassify anything.
---

# Work that ends in a journal entry

The deliverable is a workbook a reviewer can trace from the entry back to what
it was built from. The entry is a sheet of that workbook, named `FullSeam JE`
and laid out as below. Uploading the workbook as the task's deliverable drafts
the entry on the task from that sheet, so the entry under review is the one in
its support and nobody retypes it. You post nothing. The reviewer's sign-off
approves the draft; how it then reaches the ledger is the ledger's
`entry_posting` on `company_get`:

- `off` — the person books the approved entry in the ledger and records its number.
- `by_hand` — the reviewer posts it from the entry's page.
- `on_approval` — the sign-off posts it.

That holds where `work` on `company_get` says entries are kept here with their
lines. Where it says they are recorded by their number, the sheet is not read:
the workbook is built the same way, and the person books the entry in the
ledger and records its number on the task.

## Where the figures come from

- **The balance the entry will change** — `books_balances` for the account, this
  month and last. That is what the schedule has to tie to. The month being
  closed is often in `not_posted`: its balance is last month's plus this
  month's entries to the account, from `books_entries` with the `month` and the
  `accounts`.
- **The month's entries** — `books_entries` for the month, with `task_id`, so
  the whole read is kept on the task and the reviewer can see what the work was
  built from. Say which accounts you read. An item that belongs in the work can
  be sitting in an account you did not read.
- **The document behind a line.** A row from `books_entries` carries the
  transaction's `type` and `id`. `books_read` with `read` `transaction` opens
  it, with the `documents` attached to it in the ledger; `read` `document` puts
  one on the task as a file to fetch and read. Where a term, a period or an
  amount matters to the entry, read it off the document and not off a memo.
  Fetching keeps the document on the task, so fetch one only where it bears on
  the entry; one that turned out not to is removed (`file_put(action=remove)`).
- **Last month's workbook** — `task_get(view=prior)`, then fetch the file itself
  from its `download_url`. Roll it forward. Rebuilding from nothing loses what
  the last preparer knew and the reviewer is used to.

Without **See ledger detail**, `books_entries` and `books_read` are not in your
list: ask the person for the ledger export and the documents, and say the
figures came from them.

## The workbook

- **What was read is on its own sheet, unchanged**, with where it came from and
  the date it was read.
- **Figures are formulas over that sheet**, not values worked out somewhere else
  and typed in. A reviewer clicks a cell and sees how it got there.
- **The entry is the `FullSeam JE` sheet**, in the layout under *The entry
  sheet*, each amount a formula pointing at the schedule.
- **Checks are cells that read zero.** The schedule's ending balance against the
  books' balance once the entry is booked; debits against credits. Name each
  check and its result in your account of the run.
- **Every formula cell carries its result.** A library that writes formulas
  without computing them leaves cells that read as blank to anyone without
  Excel, and FullSeam refuses an entry amount with no stored result. Recalculate
  the workbook in a program that computes formulas and saves their results, and
  read it back before handing it over.

## The entry sheet

One sheet, named `FullSeam JE`:

|   | A | B | C | D | E |
|---|---|---|---|---|---|
| 1 | Date | 2026-09-30 | | | |
| 2 | Reference | PPD-2609 | | | |
| 3 | Memo | Prepaid amortization, September 2026 | | | |
| 4 | | | | | |
| 5 | Account | Name | Debit | Credit | Memo |
| 6 | 6300 | Insurance | 2600.00 | | General liability |
| 7 | 1350 | Prepaid expenses | | 2600.00 | |
| 8 | Total | | 2600.00 | 2600.00 | |

- **Above the table**, a label in column A and its value in column B. Each may
  be left out.
  - `Date` — a date cell, or YYYY-MM-DD. Left out, it is the last day of the
    task's month.
  - `Reference` — the journal number. Use the firm's numbering where it has
    one: `task_get(view=task)` on last month's task (`last_month` gives its
    `task_id`) lists its `journal_entries`. Left out, FullSeam assigns the next
    unused `JE-YYMM-NN`.
  - `Memo` — the entry's own.
  - `Entity` — the entity code. Only for a task covering several.
- **The table starts at the row holding `Account`, `Debit` and `Credit`.** A
  `Memo` column is read when it is there. A column headed with one of the
  books' dimensions — `Class`, the company's word for a department
  (`Location`, `Business`), `Vendor`, `Customer` — is read as that dimension of
  each line, by the value's name in the ledger (`books_read` with `read`
  `records` lists them: `Class`, `Department`, `Vendor`, `Customer`). Any other
  column, such as the account name, is ignored, so put what a reviewer wants
  to see beside the lines. One table to a sheet.
- **A line on Accounts Payable names its vendor, one on Accounts Receivable its
  customer**, where the books are QuickBooks: QuickBooks posts neither without
  one. A line names one vendor or customer at most.
- **`Account`** is the account number as `books_accounts` gives it. A name is
  read only where one account has it.
- **An amount is a positive number in `Debit` or in `Credit`**, never both. A
  number typed as text, a negative, more than two decimal places, or a formula
  with no stored result is refused, with the cell.
- **Lines run to the row whose account is `Total`**, or to the end of the
  sheet. A line with no amount is skipped: an item that took nothing this month
  can keep its row.
- **A second entry is a second sheet** whose name starts with `FullSeam JE` —
  `FullSeam JE - reclass`. Each sheet is one entry. A hidden sheet is refused.
- **The workbook is .xlsx or .xlsm.** Any other file is not read for an entry.

**A workpaper somebody already keeps stays as it is.** Add the sheet to it, its
amounts formulas over the cells the workpaper already works out. Do not
rearrange a schedule a reviewer knows in order to fit this.

What the upload answers with says what happened:

- `entries` — the entries drafted, each with the `sheet` it was read from and
  its `web_url`.
- `entry_not_read` — the sheet is there and was not read: one line with the
  cell or the reason. The workbook is on the task all the same.
- Neither — no sheet whose name starts with `FullSeam JE`, a file that is not
  .xlsx or .xlsm, or an organization that records entries by their number.

**The sheet is the entry.** To change the entry, change the sheet and upload the
workbook again: the same sheet redrafts the same entry and does not add a
second. A sheet that says what it said before leaves the entry, and any approval
it has, as they were. This works only while the task is not in review: see
*After it is handed in* in `close-task-work`. Once it is back, uploading again
redrafts it, and `entry_put(action=workbook)` with the deliverable's `file_id` alone
reads its sheet again. An entry already in the ledger is not redrafted. Do not
key the lines with `entry_put(action=workbook)` to get past a refusal — the entry and its
support would then say different things.

An entry somebody already booked in the ledger is not drafted again:
`entry_put(action=booked)` with its `reference` reads it in.

## Judgement, and when to stop

Do the arithmetic; do not make the policy.

- **A question of policy** — whether something is capitalised, which account it
  belongs in, what is too small to bother with, how a part period counts — is
  answered by the firm, not by you. Read the `policies` branch for the area
  (`company_get` with its `path`) and this work's own notes.
  - `stated`, written by a person: the answer. Follow it and cite it.
  - `stated` with `by_agent`: a chat's report of the answer. Follow it, cite
    it as that, and check its `source` where much turns on it.
  - `observed`: what has been done before. Follow it, and say you followed
    precedent and not a rule.
  - `open`, or nothing at that path: nobody has decided. Do not decide
    silently, and do not put the undecided item in the entry: leave it out,
    list it as open with what the entry would be each way, and ask the question
    where the answer will live — `company_put` with `status` `open` at that
    path — so it is answered once and not met again next month.
  - No `notes` in `company_get`: this person cannot read the notes, so you do
    not know whether it is decided. Ask them.
- **A fact you could not establish** — a period, a rate, what a bill was for —
  is listed as open, with the document you would need. It is not estimated.
- **A correction is a line and a sentence.** Moving something between accounts
  is its own line in the entry, or its own entry, with a memo saying what moved
  and why. Never folded into another line.
- **Never plug.** A difference you cannot explain is reported as a difference.

## The same arithmetic every month is a script

If the work is a roll — last month's schedule, this month's additions, the same
formula — write it as a script that takes the month and the input files and
writes the workbook, and keep it with the agent by `template_put(action=file)`. No
month, balance or file path written into it. Next month the script is run and
looked over, not rewritten.

What the script cannot decide — the judgement above — stays with you, each
month, and is what the agent's notes are for.

This is how the work is done well. Finding the task, reading its agent and
handing it back are `close-task-work`.
