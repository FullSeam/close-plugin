---
name: close-configure
description: Set a piece of work up in FullSeam so it is done the same way every month — give it a template, write its instructions, keep its script and notes, and name the documents it reads and makes. Use when asked to set up or configure a task, set up or change its agent or its instructions, write or fix its instructions, make work recurring, or work out why the same task keeps being done differently.
---

# Setting work up

Writing here takes a connection that can change records, and **Edit templates**
(a note takes **Edit company notes**). Without them, write the instructions, the
script and the notes out and give them to the person (`fullseam`, *When a tool
is not in your list*).

## How it is put together

Two pairs, the same shape both times: a standing thing, and this month's
instance of it.

| standing | this month |
|---|---|
| **template** (a task template) — "Property tax accrual" | **task** — September's |
| **document** (a file template) — "GL export" | **file** — September's copy |

A **document is a role, not a file**: the name for something that recurs, so
work can say "I read the GL export" without naming January's copy. A template
says which documents it reads and which it makes, and each month's
file says which one it is.

Everything about *how* the work is done hangs off the template, never
off one month's task: the instructions, the scripts, the notes. That is its
**agent**, and `close_agent_get` returns all of it.

Say this in these terms when you explain it. Somebody asked "should this read
the GL export?" is being asked to name a role that outlives the month.

## When work needs a template

- **A task made with `close_task_create` already has one.** Nothing to do.
- **A task that arrived without one** — imported from a spreadsheet, usually —
  has nowhere to keep instructions. `close_agent_get` answers with `template`
  null. `close_template_from_task` gives it one, copied from the newest month of
  that work: its name, section, type, entities and people. It links to an
  existing template of the same name instead of making a second, and points
  the same work in other months at it unless `link_siblings` is false.
- **Work that will only ever happen once** needs none beyond the one its task
  came with. Do not write instructions for it.

Two things do not come across from a task, and the answer says which:
dependencies on tasks that have no template of their own, and people with no
account.

## Writing the instructions

Read first: `close_agent_get` with `history` — what is already written, the
scripts and notes kept, the documents named, and the last few months as they
were actually done: who prepared each, what it read, what it made. Those
outputs are what the instructions have to reproduce. Then `close_task_prior` and
`close_file_read` to see last month's workpaper itself.

Then `close_agent_instructions_put`: `steps`, each a `name` and a `description` of how to
do it, and a `body`. What it said before is kept, so `note` says why it changed.

- Steps somebody could follow without you. Each says what to open, what to do,
  and how to know it is right: a total that must agree, a balance that must
  match the books.
- Name figures by what they are, not where they sit. "The row headed *Total
  accrued* on the Summary sheet", not "row 14".
- Say where each input comes from in FullSeam's terms: which accounts to read
  from the books, which document from the file store.
- Write what you did not work out. Instructions that hide their uncertainty get
  followed confidently into the same mistake.
- Never name a tool or an address of ours in them. Instructions describe the
  work; whoever runs it knows how to reach FullSeam.

## The agent: what is kept beside the instructions

- **A script**, where the work is the same arithmetic every month. It takes the
  month and the input files and writes the workbook; no month, balance or path
  is written into it. The instructions then say: run it, and what to look at.
  `close_agent_file_put` saves one file by `path` and keeps what it said before;
  say why in `note`, and pass your `session_id`. A file that is not text goes to
  the agent's `upload_url`, with `session_id` as a form field beside it.
- **Notes**: what a person would tell their replacement about this work, one
  fact to a note. `company_put` with the `task_id` or `template_id`, a `path`,
  the `text`, a one-line `description`, a `status` and a `source`. They come
  back in `notes` on `close_agent_get`.

**Prove a script before keeping it.** Run it on last month's inputs and compare
what it writes with last month's deliverable. A script only ever run on the
month it was written for is a draft.

What is true of the whole company and not this one piece of work — where
invoices are kept, a capitalisation threshold — is a company note: the same
call with no task. `company-context` has how they are laid out.

## The documents it reads and makes

`close_template_files_put` names them. It only adds: it attaches documents and
creates missing ones, and never renames or removes one, because other work
naming that document did not ask. A document another template already makes
is refused.

Naming a document says it recurs. Attaching this month's copy shows where it
lives: `close_store_find` finds it in the organization's file store, and
`close_file_put` with the `store_file_id` makes it the task's. Where the
organization keeps its files in that store, the first deliverable of work that
names none, attached with `is_deliverable`, becomes a document named for the
work. Nothing is read out of that file: where the document is kept and how it
is found are for `close_document_put` to say.

Where the organization keeps its files in that store, a document can be given
its `folder` when it is declared, before any file exists: the path from the
store's root, with placeholders for the month, such as
`Close/{YYYY}/{MM} - {Month}/Cash`. It is kept as written: a path with no
placeholders is that one folder every month.

`close_document_put` changes one document that exists, by its id: its name,
description, cadence and expected columns, its `folder`, and how each month's
file is found there. `found_by` is `pattern` (a `name_pattern` such as
`bank-rec-1040-{year}-{month}*`: `*` for anything, `{month}` and `{year}` for
the period however it is written, and not the folder's placeholders), `tags` (the accounts whose `#fq-` tags a
workbook carries) or `off`, where a person chooses it. It changes only when
given: send `found_by` with the pattern or tags it is to use. `attach` is `automatic`
or `manual`: automatic takes a name pattern or tags, attaches the one file that
fits at each refresh, and looks at each open month now, each time it is said:
send `attach: automatic` again after a file has been dropped in the folder. A
`folder` set is made in the store for each open month, so there is somewhere to
drop it; the answer lists them. A file another took the place of stays in its folder
and is not looked at again. `close_agent_get`
gives all of these back on each document: the name pattern and the tags kept
are both shown, and `found_by` says which is used.

Where the work files its own deliverable every month, the folder is enough:
the upload attaches the file. Finding it by name or tags is for a file a
person puts in the store.

A figure that comes from the books needs no document. The instructions say which
accounts to read.

## What this skill does not do

Doing the work is `close-task-work`. What the system holds and when work needs
a task is `fullseam`.
