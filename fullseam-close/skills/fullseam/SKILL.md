---
name: fullseam
description: Start here for any accounting or close work in FullSeam — what the system holds, where a figure or a document comes from, when work needs a task or a template, and where the month stands. Use for booking, accruing, reconciling, analysing or reporting when it is not yet clear which task it is, and for any question about the progress of a close.
---

# FullSeam: what it holds and how to use it

FullSeam is the record. You do the work; it keeps what the work was, who did it,
what it was built from and who reviewed it. Everything you make ends up on a
task, or it was not done as far as the firm can tell.

## What is here

| thing | what it is | read it with |
|---|---|---|
| **Company** | its entities, fiscal years, ledger and file store; and what has been written down about how it works, one fact to a note, each at a path | `company_get` |
| **Task** | one month's piece of work: a preparer, a reviewer, a status, files, a trail | `close_my_work`, `close_tasks_list`, `close_task_get` |
| **Template** | the same work every month. It holds the *agent*: the instructions, the scripts and notes, and the documents the work reads and makes | `close_templates_list`, `close_agent_get` |
| **Reconciliation** | one account's month, proved: its balance against support, the items between them, its own preparer, reviewer and review | `close_recs_list`, `close_rec_get` |
| **Books** | the chart, each account's balance and movement by month, the entries behind an account | `books_accounts`, `books_balances`, `books_entries` |
| **More from the ledger** | what an account's postings add up to, what is unpaid, a transaction in full, a document attached to one | `books_read` |
| **Flux** | each account's movement against the months before it, and what has been said about why | `flux_get` |
| **Files** | a task's deliverable and what it read; the organization's own file store | `close_task_files`, `close_file_read`, `close_store_find` |
| **Trail** | what was said and done | `close_activity` |

## The words people see

Say these to a person; they are what FullSeam's screens say.

- **Template**: the same work every month.
- **Agent**: what a template's tasks are run by — its instructions, project
  files and documents. It is on the template's Agent tab. "Set up an agent for
  this task" means write that.
- **Instructions**: the agent's steps, with **Details** under them.
- **chat**: the conversation a person has in FullSeam, with FullSeam's own
  assistant.
- **external chat**: Claude, ChatGPT or another product connected from outside.

So a company has many agents, one for each kind of work that has instructions
written, and "Run agent" on a task starts a chat that works the task by them.
Do not call yourself "the agent": you are the chat a person is in, or their
external chat where you were connected from outside.

## How a task, its template and a reconciliation fit together

- The **template** is the work as it is done every month. It holds
  the instructions its tasks are run by, and whether the work is reviewed.
- A **task** is one month of that work. It holds the files and the status.
- A **reconciliation** is one account's balance for the month, proved. It has
  its own preparer, reviewer and sign-off, and it is what the next month and an
  auditor read.

A task is tied to the accounts it prepares, so its workpaper is the support
those reconciliations read. `close_task_get` lists a task's `accounts`, each
with its `rec_id` and where that rec stands; `close_rec_get` names the tasks a
rec is worked in.

Two shapes of work, and they are finished differently:

| the work | the task | the review |
|---|---|---|
| books a journal entry, alone or with a rec | reviewed: `submit_for_review` | on the task, where the entry is approved; the rec is submitted too |
| only builds a rec's support (a roll-forward, a tie-out) | not reviewed (`needs_review` false): its preparer moves it to `done` | on the rec: `close_rec_review` `submit` |

Whether work is reviewed is the template's: `close_template_reviewed`.

**Read `company_get` once, first.** It answers what a cold session otherwise
guesses: which entity, which fiscal year, which ledger and what it can be asked,
where files are kept, and, in `work`, whether entries are kept here with their
lines or recorded by their number.

**Then read the notes that bear on the work.** The connect message lists up to
forty, open questions and stated notes first; `company_get` lists every one.
`company_get` with a `path` returns that note, or every note beneath it:
`policies/prepaids` before prepaid work, `documents` before looking for a file.
How far to trust one:

- `stated`, `by_agent` false: a person's answer. It outranks your reading of
  the data.
- `stated`, `by_agent` true: a chat's report of what a person or a document
  said. A claim to check against its `source` before much turns on it.
- `observed`: somebody's reading of the data.
- `open`: a question nobody has answered. Do not act as though it were settled.

No `notes` in the answer to `company_get` means this person cannot read the
notes, not that nothing is written. Ask them what you would have looked up.

Learning the company and writing it down is `company-context`: when the person
asks for it, or when nothing at all is written and they are not in the middle of
a task.

## When a tool is not in your list

You reach FullSeam through a connection: one this person approved on screen for
the client you are running in, or an access token they made. You are given the
tools this person and this connection may use; the others are left out, not
refused. The connect message, what you were told when you connected, says
whether this connection can change records. Without a tool, do what you can and
hand the rest to the person, with the permission it takes as FullSeam names it.

What is missing is one of four things, and a refusal says which. Send the
person where that one is put right, and nowhere else:

- *The organization does not let an agent do it.* Whoever manages its agents
  changes that in Settings, Agents. No connection gets past it.
- *The person's role lacks it*, or lacks **Let agents change records**, without
  which no connection of theirs is offered the choice to change anything.
  Whoever manages roles adds it in Settings, Team.
- *This connection was approved without it.* The person disconnects it under
  Settings, Agents, and connects again with it ticked.
- *This token was made without it.* They make another, in the same place.

| without | it takes | instead |
|---|---|---|
| any tool that changes something | the organization letting agents change records, the person's role carrying **Let agents change records**, and a connection approved, or a token made, with **Change records** | hand the person what you produced, to attach and submit in FullSeam |
| `books_entries`, `books_read` | **See ledger detail** | ask for the export or the document; name what you could not read |
| `books_accounts`, `books_balances` | **See reconciliations** | ask for the trial balance |
| the notes in `company_get`; `company_put` | **See company notes**; **Edit company notes** | ask the person; give them the note to save |
| `close_agent_file_put`, `close_agent_instructions_put`, `close_template_reviewed`, `close_template_files_put`, `close_document_put`, `close_template_from_task` | **Edit templates** | give the person the script or the instructions themselves |
| `close_file_put`, `close_entry_draft`, the task's `upload_url` | **Edit files and journal entries**, on this task | hand the files back for the person to attach |
| `close_task_status` | **Prepare tasks** | tell the person which move to make |
| `close_task_create` | **Add tasks** | say what task you would make |
| `close_store_find` | **Browse the file store** | ask where the file is |

A tool you were not given is not missing data: say which tool, and what it
takes.

## What was asked

**Where things stand** — what is open, late, waiting on whom. See *Where the
month stands* below.

**A piece of work that is on the checklist** — "do the prepaids", "book
payroll". Find it: `close_tasks_list` with a `search` on a word from its name,
across the open months. Then `close-task-work`.

**Work that is not on the checklist.** Look before deciding it is new:
`close_tasks_list` and `close_templates_list`, each with a `search`. The firm
may call it something else.

- *A question with an answer* — "why is software up?" — is answered from the
  books and makes nothing. Say where each figure came from. Why an account
  moved, and writing that down on the flux, is `flux-analysis`.
- *A page of figures somebody will open again* — a report, a dashboard, a
  breakdown, a forecast — is a report page, where `report_put` is among
  your tools: `report-pages`.
- *Work that leaves something behind* — a workbook, an entry, a report somebody
  will review or want again — gets a task. See *When to make a task*.

**Set this up so it runs every month** — `close-configure`.

**Learn this company, or write down how it works** — `company-context`.

**Revenue** — loading billing or contract data, keeping the revenue registers,
or saying where a revenue figure came from — is `revenue-registers`, where the
connect message says the company keeps them here.

**Review this** — `close_task_get` says what this person may do in `may`.
Approving and signing off are theirs to do in FullSeam, never yours. Whoever
drafted an entry cannot sign it off unless their organization lets one person
prepare and review, which `may` says, and uploading a changed entry sheet
makes the uploader its drafter: where it does not, a reviewer who has the
sheet fixed this way can no longer sign it off.

## When to make a task

Make one when the work produces something a person will review, rely on or want
again. Do not make one to answer a question.

`close_task_create` puts it on a month and makes its template in the
same call, because work with no template has nowhere to keep instructions and
cannot come round again. A template of the same name is reused, so the second
entity's copy of the same work joins the first.

- Name the entities it is for. Work belonging to no entity is refused.
- Name who reviews it — `reviewer_ids`, from `close_people`. Work with no
  reviewer has nobody to be submitted to, and no tool here changes a task's
  people afterwards.
- Where the work only builds the support for a reconciliation, which is
  reviewed there, say `needs_review: false`: the task has no reviewer and its
  preparer marks it done. Ask before choosing this.
- Give it a `task_type`: it says how the work is handed back. `je_creation` ends
  in a journal entry and loads `work-journal-entry`, and is always reviewed. A
  task that is not reviewed can carry no entry.
- Say that you made it.

A new template on its own is never the goal. It appears with the
first task, and what goes in it — the instructions, a script — is `close-configure`.

## Reconciling an account

The record that an account is proved is its **reconciliation**, not the task. A
task holds the work; the rec is what the month's status, the next month's
opening and the reviewer's sign-off on the balance read. Work done only on a
task leaves the account unreconciled.

1. `close_recs_list` for the month. No rec for the account: `close_rec_setup`
   it — preset `balance`; `task_id`, which ties the account to that task;
   `support_file_template_id`, the workbook's file template (the documents in
   `close_agent_get`); `read_by` `tag`; the reviewer from `close_people`. An
   account with no balance yet has nothing to reconcile and is refused.
2. In that workbook, `#fq-<account number>` in the cell beside the support
   figure, once per account. FullSeam reads saved values, not formulas: save
   the workbook calculated.
3. The workbook goes on the task as its deliverable, in that file template.
4. `close_rec_get`: its support is now the tagged figure. Each difference is an
   item (`close_rec_prepare`). Key a support figure only where no document is
   read.
5. `close_rec_review` `submit`, beside the task's own move: `submit_for_review`,
   or `done` on a task nobody reviews (`needs_review` false), whose work is
   reviewed here on the rec.

Support that is not the task's workbook, a bank statement say, can go to the
rec itself: the `upload_url` on `close_rec_get`, or `support_file` on
`close_rec_prepare` for a file already in the file store. It is the month's
copy of that document for every task that reads it too. `close-task-work` has
where each kind of file is kept.

`close_task_accounts` replaces a task's whole list of accounts; use it to set
the list, not to add one. `close_task_get` lists a task's accounts with each
one's `rec_id`, and `close_rec_get` names the tasks a rec is worked in. An
account that already has a task takes its instructions there: a second task for
the same account is two places to look for one piece of work, so ask before
making one.

Whether work is reviewed is set when its task is first made
(`close_task_create` `needs_review`) and changed on its template:
`close_template_reviewed` with the `template` `id` from `close_task_get`. That
is the Reviewed box on the template's page. Its open tasks follow at once:
turned off, they lose their reviewers and one in review goes back to In
Progress. Finished tasks and sealed months stay as they were. Ask before
turning a review off: say which work, and that it will be reviewed on its
reconciliation instead.

## Where a figure or a document comes from

| you need | where |
|---|---|
| a balance, or what moved in a month | `books_balances` |
| what is behind an account | `books_entries` — each row has the transaction's `type` and `id` |
| what an account's month is made of, beside other months | `books_read` with `read` `activity` |
| what is unpaid as of a date | `books_read` with `read` `open_payables` or `open_receivables` |
| the bill or invoice behind a line, and the document attached to it | `books_read` with `read` `transaction`, then `document`, which puts the document on a task |
| last month's version of this work | `close_task_prior` |
| a document the firm keeps | `close_store_find`; `close_file_read` with its `store_file_id` to look at it, `close_file_put` to make it a task's |
| how this work is done | `close_agent_get` |
| how the firm does things generally, or a policy | `company_get` with the `path` of the branch |
| how a screen works, or a step only done in the web app | `help_read` — and send its `url` |

Figures come from the books, not from wherever they were last copied to. Do not
go through other tasks' files looking for a trial balance.

What a ledger can be asked differs by ledger. `company_get` lists the reads this
one answers and what each takes; one that is not listed cannot be had here, and
that is said, not worked around.

Three things about the figures themselves:

- An income statement account's balance is **year to date**. A month's activity
  is its `movement`.
- Debits are positive, credits negative.
- A month in `not_posted` has no trial balance. It is not a month of zeros.

Pass `task_id` to a books read and the whole read is also kept on that task as a
file: that is how what the work was built from gets on the record, and how a
script gets the figures as a file. Where this connection cannot change records
the read still comes back, with `not_kept` saying why it was not kept.

## Where the month stands

`close_my_work` is the caller's own queue across every open close;
`close_tasks_list` is any task in any month. Both carry `total` and `by_status`
before any rows, so a count needs no rows at all.

- More than one close is normally open. Do not assume the newest is meant.
- Lead with the split, then what needs a decision today: overdue first, then
  anything with something in `waiting_on`. Say how many you are not showing.
- `waiting_on` is on every `close_my_work` row and on `close_task_get`: the
  unfinished tasks this one waits on, empty when nothing is blocking it.
  `close_tasks_list` rows do not carry it.
- By person: `close_tasks_list` with `with_people`. By entity: `entity`.
- Waiting on a reviewer: `submit_for_review`, `review_in_progress` and
  `second_review`; an entry waiting for its sign-off: `je_created`. Approved,
  entry not yet booked: `approved_for_je`. Each is a `status` to filter by and
  a count in `by_status`.
- `not_shown` on `close_my_work` is work in an entity whose close has sealed.
- `more` true means there are further rows: page with `offset` before
  presenting a list as the whole of it.
- `close_activity` with no task is the recent comments across the close; with
  `task_id` it is that task's trail.

## Always

- Never state a figure you did not read or compute here.
- Say what you actually wrote to FullSeam — a task made, a file attached, an
  entry drafted, a status moved — and what you did not. A write that was
  refused, or a tool you did not have, leaves the work done and a step for the
  person: say which.
- Link what you name. A task, an entry and a note you wrote each come back with
  a `web_url`; an id is no use to the person reading.
- A refusal is an answer. Read it: it says what is wrong and usually what to do.
