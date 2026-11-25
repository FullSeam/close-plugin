---
name: close-configure
description: Set a piece of work up in FullSeam so it is done the same way every month — give it a template, write its instructions, keep its script and notes, and say which file templates it reads and makes; or make an agent of one's own that is on no task. Use when asked to set up or configure a task, set up, make or change an agent or its instructions, write or fix its instructions, make work recurring, or work out why the same task keeps being done differently.
---

# Setting work up

Writing here takes a connection that can change records, and:

- an agent's instructions and files: **Edit agents**, for that template. A role
  may hold it only for the templates that name the person a preparer.
- making an agent of one's own, and writing it: **Create agents**.
- file templates, whether the work is reviewed, giving a task a template:
  **Edit templates**, for each template a change ties to or takes from.
- a note: **Edit company notes**.

Without them, write the instructions, the
script and the notes out and give them to the person (`fullseam`, *When a tool
is not in your list*).

## How it is put together

Two pairs, the same shape both times: a standing thing, and this month's
instance of it.

| standing | this month |
|---|---|
| **task template** — "Property tax accrual" | **task** — September's |
| **file template** — "GL export" | **file** — September's copy |

A **file template is not a task template.** A task template is recurring
work. A file template is a recurring file: the name for it, so work can say
"I read the GL export" without naming January's copy. Each month's file says
which file template it is.

How they are tied:

- A file template is **made by** one piece of work at most — a task template,
  or an agent of its own — or by none: then it is uploaded, or pulled from a
  connected system, each month.
- Any number of task templates and agents of their own **read** it.
- A **reconciliation** reads one as its support, or as the ledger it is tied
  out against (`rec_setup`). No task template stands between them; where one
  makes the file, its task hands it to the rec.

Everything about *how* the work is done hangs off the task template, never
off one month's task: the instructions, the scripts, the notes. That is its
**agent**, and `template_get(view=template)` returns all of it. Its scripts
and notes are **project files**, not file templates: they have no month and
nothing else reads them.

Say this in these terms when you explain it. Somebody asked "should this read
the GL export?" is being asked to name a file template that outlives the
month.

## When work needs a template

- **A task made with `task_create` already has one.** Nothing to do.
- **A task that arrived without one** — imported from a spreadsheet, usually —
  has nowhere to keep instructions. `template_get(view=template)` answers with `template`
  null. `template_put(action=from_task)` gives it one, copied from the newest month of
  that work: its name, section, type, entities and people. It links to an
  existing template of the same name instead of making a second, and points
  the same work in other months at it unless `link_siblings` is false.
- **Work that will only ever happen once** needs none beyond the one its task
  came with. Do not write instructions for it.

Two things do not come across from a task, and the answer says which:
dependencies on tasks that have no template of their own, and people with no
account.

## Writing the instructions

Read first: `template_get(view=template)` with `history` — what is already written, the
scripts and notes kept, the file templates it reads and makes, and the last few months as they
were actually done: who prepared each, what it read, what it made. Those
outputs are what the instructions have to reproduce. Then `task_get(view=prior)` and
`file_read` to see last month's workpaper itself.

Then `template_put(action=instructions)`: `steps`, each a `name` and a `description` of how to
do it, and a `body`. What it said before is kept, so `note` says why it changed.

- Steps somebody could follow without you. Each says what to open, what to do,
  and how to know it is right: a total that must agree, a balance that must
  match the books.
- Name figures by what they are, not where they sit. "The row headed *Total
  accrued* on the Summary sheet", not "row 14".
- Say where each input comes from in FullSeam's terms: which accounts to read
  from the books, which file from the file store.
- Write what you did not work out. Instructions that hide their uncertainty get
  followed confidently into the same mistake.
- Never name a tool or an address of ours in them. Instructions describe the
  work; whoever runs it knows how to reach FullSeam.

## The agent: what is kept beside the instructions

- **A script**, where the work is the same arithmetic every month. It takes the
  month and the input files and writes the workbook; no month, balance or path
  is written into it. The instructions then say: run it, and what to look at.
  `template_put(action=file)` saves one file by `path` and keeps what it said before;
  say why in `note`, and pass your `session_id`. A file that is not text goes to
  the agent's `upload_url`, with `session_id` as a form field beside it.
- **Notes**: what a person would tell their replacement about this work, one
  fact to a note. `company_put` with the `task_id` or `template_id`, a `path`,
  the `text`, a one-line `description`, a `status` and a `source`. They come
  back in `notes` on `template_get(view=template)`.

**Prove a script before keeping it.** Run it on last month's inputs and compare
what it writes with last month's deliverable. A script only ever run on the
month it was written for is a draft.

What is true of the whole company and not this one piece of work — where
invoices are kept, a capitalisation threshold — is a company note: the same
call with no task. `company-context` has how they are laid out.

## The file templates it reads and makes

Look before making one: `file_template_get(view=list)` lists every file
template with what makes it and what reads it, and
`file_template_get(view=file_template)` shows one with its latest months'
files. A second with the same name is refused with the first one's id.

- `file_template_put(action=create)` makes one, with the work that makes it
  (`made_by_template_id`, or `made_by_agent_id` for an agent of its own) and
  the work that reads it (`read_by_template_ids`, `read_by_agent_ids`). Made
  by none, it is uploaded each month.
- `file_template_put(action=readers)` adds task templates and agents of their
  own to what reads one, or takes them off. Only someone who already sees its
  files can add a reader.
- `file_template_put(action=maker)` moves one to the work that makes it from
  now on (`template_id` or `agent_id`), or to none. It takes changing both:
  the work it leaves and the work it goes to. Files already made stay where
  they are. Work set up twice leaves its file templates on the copy: move
  them to the work that is done, before the copy is archived.

Making one says it recurs. Attaching this month's copy shows where it
lives: `file_store_find` finds it in the organization's file store, and
`file_put(action=add)` with the `store_file_id` makes it the task's. Where the
organization keeps its files in that store, the first deliverable of work that
names none, attached with `is_deliverable`, becomes a file template named for the
work. Nothing is read out of that file: where it is kept and how it
is found are for `file_template_put(action=edit)` to say.

Where the organization keeps its files in that store, a file template can be given
its `folder` when it is made, before any file exists: the path from the
store's root, with placeholders for the month, such as
`Close/{YYYY}/{MM} - {Month}/Cash`. It is kept as written: a path with no
placeholders is that one folder every month.

`file_template_put(action=edit)` changes one that exists, by its id: its name,
description, cadence and expected columns, its `folder`, and how each month's
file is found there. `found_by` is `pattern` (a `name_pattern` such as
`bank-rec-1040-{year}-{month}*`: `*` for anything, `{month}` and `{year}` for
the period however it is written, and not the folder's placeholders), `tags` (the accounts whose cell tags a
workbook carries) or `off`, where a person chooses it. It changes only when
given: send `found_by` with the pattern or tags it is to use. `attach` is `automatic`
or `manual`: automatic takes a name pattern or tags, attaches the one file that
fits at each refresh, and looks at each open month now, each time it is said:
send `attach: automatic` again after a file has been dropped in the folder. A
`folder` set is made in the store for each open month, so there is somewhere to
drop it; the answer lists them. A file another took the place of stays in its folder
and is not looked at again. `file_template_get`
gives all of these back: the name pattern and the tags kept
are both shown, and `found_by` says which is used.

Where the work files its own deliverable every month, the folder is enough:
the upload attaches the file. Finding it by name or tags is for a file a
person puts in the store.

A figure that comes from the books needs no file template. The instructions say which
accounts to read.

## An agent of its own

Not all recurring work belongs on the close checklist. A person can make an
agent that is theirs, on no task, with `template_put(action=agent)`:

- With an `account_id`, it works that account's reconciliation each month and
  puts its support on the rec (`rec_prepare`); the rec's own review is its
  review.
- With none, it works on nothing in particular: a weekly check, a monthly pull.
  Nothing reviews its runs: work that needs sign-off is a task.

Whoever made it is its first editor. Each run of an agent acts as the Agent —
its own permissions and the company's connections — or as the person who
started it, with theirs: its Runs as says which unless the run is told
(`run_start` takes `as`). `template_put(action=settings)` sets Runs as, its
Permissions and When done, on a template's agent or one of its own. A trigger
that runs as a person loses an agent somebody else changes. Write it as a
template's agent is written, by its `agent_id`:
`template_get(view=template)`, `template_put(action=instructions)`,
`template_put(action=file)`. It makes and reads file templates as a template does,
with its `agent_id` in place of a `template_id`. A run puts the month's file of
one it makes with `file_template_put(action=file)`, or from outside to the `upload_url`
`file_template_get(view=file_template)` gives with `period`: on no task, the month's copy
for everything that reads it, listed on the run. Anything else a run makes stays
on that run. `run_start` with `agent_ids` runs it now,
and `run_list` lists the agents of their own the person sees.

Ask which the person wants before making one. Work that belongs on the
checklist, reviewed there and counted in the month's progress, is a task and
its template's agent. Its schedules, Runs as and archiving are on its page
under Agents: give the person its `web_url`.

## Trying an agent

Before an agent is trusted to change anything, it can be run as a test:
`run_start` with `test: true` and the agent's `template_ids` or `agent_ids`.
It does the month's work as a run would — the same task, the same reads — and
nothing it changes is made: files, comments, moves and entries are recorded
for the person to look at. With no `period_id` it runs on the month it would
run on now. `note` is words of the person's own for the run ("build August from
July's copies; don't open August's").

A test run is read with `run_get` once it is done: what it made, with each
file to download; what it would have changed; what it read; and what was said.
Where it went wrong, change the agent's instructions or files
(`template_put`) and run the test again. `run_list` with `include_tests` lists
the month's test runs.

A test run is allowed where changes from outside are not: it changes nothing.

## What this skill does not do

Doing the work is `close-task-work`. What the system holds and when work needs
a task is `fullseam`.
