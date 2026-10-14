---
name: close-task-work
description: Work one task in FullSeam — read how it is done and how last month went, get what it needs from the books and the file store, do the work, and hand it back for review with its files, its entry and an account of the run. Use when the user names a task, or asks to prepare, book, roll forward or submit a specific piece of work.
---

# Working one task

FullSeam holds the record of the work; you do the work. What the system holds,
where a figure comes from, what to do when a tool named here is not in your
list, and the rules under *Always* are `fullseam`. Writing down how a piece of
work is done is `close-configure`.

## Read before you write

Four reads, in this order.

**`close_task_get`** — status, people, what it is `waiting_on`, its journal
entries, and **`may`**: what this person is permitted to do. A task waiting on
something unfinished is not ready. `read_only` means this connection can change
nothing here: `may` and `allowed_transitions` are empty, and the work ends with
the person attaching and submitting what you made. `task_type` says how the
work is handed back; for `je_creation`, load `work-journal-entry`.

A refusal reading "… has not been started" means the task's month, or the
funds it is for, is still being set up: it takes no files, no run and no
entries until somebody starts it. Starting a month is done in the web app, on
the Close page, by whoever plans the close. Say so and stop; there is no tool
for it.

**`close_agent_get`** with the `task_id` — how the work is done, in one call:
the written instructions, every script kept with it, the `documents` it reads and
makes, and the `notes` written about this piece of work in particular (empty for
somebody who cannot read notes). A script with `text_omitted` came without its
text: ask for it by `path`, or fetch its `download_url` if it is too large for
that. One marked `unavailable` is listed but its contents are gone from
storage: there is nothing to fetch, so say which file it is and go on without
it, or ask for it to be put back. `withheld` means this person sees the instructions and nothing else of the
agent. Add `history` for how the last few months actually went.

**`close_task_prior`** — last month: the task, the files it made and read, and
the account of how it went.

**`company_get`**, if you have not read it this session, and then the branch of
company notes that bears on this work, by its `path`.

**Register the run with `close_session_update`** before the first write: the
`task_id`, and `client` and `capabilities` so the record says what ran. It hands
back a `session_id`; pass it to every write that takes one and on every upload,
so the run's record on the task links to what it did. A run stays on the task it
was registered on, and registering another, for any task, ends this one.
Recording a run is a change: a connection that cannot change records is not
given the tool, and there is no run to register.

**Starting.** Reading a task does not start it, and nothing starts it for you.
Where the person asks you to do the work and the task is `not_started`, move it
to `in_progress` with `close_task_status` before the first write, if
`allowed_transitions` offers it. Where it does not — a reviewer asking about
the work in front of them — leave the status as it is.

## With an agent, and without

**There is an agent**: `close_agent_get` answers with instructions or files.
Follow it. It is the firm's, written by somebody who has
done this before, and it outranks your reading of the files.

- A script kept with the agent is run, not rewritten: as it is, on this month's
  inputs, wherever you run code. Read what it produced. Where you cannot run
  code, say so and stop there: working the figures out another way is not the
  firm's procedure. If it fails or its output is wrong, fix it and save it back
  with `close_agent_file_put` and a `note` saying why: that is next month's
  script too.
- Its checks are assertions. One you could not verify is reported as unverified,
  never as passed.
- If the work cannot be done the way it says — a source moved, a sheet is not
  where it should be — say so. Quietly doing it differently leaves next month
  following instructions that no longer describe what happens.

**There is not.** Work it out from last month and the trail, and say that is
what you did.

- `prior_task` null on `close_task_prior` does not mean there is no history.
  `close_tasks_list` with an earlier `period` and a `search` on the name usually
  finds it. `matched_by` `name` means it guessed two months calling something
  the same thing are the same work.
- `close_activity` with the `task_id` is often the only record of how a task
  was done.
- Having worked it out, you hold what the next person needs. Save it: see
  *Handing it back*, step 5.

## Files

`content` on a file says where it is: `store` is the organization's own file
store, readable here; `platform` is held by FullSeam; `link` is only an address
in Egnyte, Drive or SharePoint, to fetch with that system's own tools or ask for.

`close_file_read` reads one, and says how it went in `mode`:

| | |
|---|---|
| `workbook` | sheets and rows |
| `text` | csv, text, json |
| `pdf` | a PDF's text, page by page; a scanned page as a picture |
| `docx` | a Word document's paragraphs and table rows |
| `link` | an address — fetch it elsewhere |
| `unavailable` | a path was recorded and the bytes are not there |
| `locked` | password-protected: a workbook, a PDF or a Word document |
| `binary` | anything else; fetch it whole |

A workbook comes back one sheet at a time and capped. If `truncated` is true you
have part of it: page with `offset`, and pass `sheet` for any sheet but the
first. Totalling a column you only partly read is how a wrong figure gets in.

A PDF comes back some pages at a time: `page_count` is how many it has, and
`pages` (`3`, or `3-7`) asks for others. A page with no text layer is a scan,
and comes as a picture to read. `images` asks for pictures of the other pages
too, for a table whose columns the text ran together. A Word document pages
with `offset`. `truncated` means the same on both.

Rows and pages are for reading; rolling a workbook forward needs the file.
Anything with a `download_url` can be fetched whole. It is a whole address: a
GET of it is the file. The address is the permission. It takes no header and
lasts an hour; read the file or the task again for a new one once it has
lapsed. From a shell:

```
curl -fsS -o "last month.xlsx" "<download_url>"
```

Quote it. With `-f` a refusal fails the command instead of being saved as the
file; `close_file_read` on the same file says why.

Where you cannot fetch an address, you have the rows and not the file: say so,
and that the person will need to give you the workbook or roll it themselves.

A file you could not read is a hole in the work. Name it.

## Handing it back

One routine, in this order. Each write goes on the trail under this person's
name with the run that made it. Where this connection cannot change records,
none of these is open to you: hand the person the deliverable, what it was
built from, your account of what you did and the note for the reviewer, to
attach and submit in FullSeam.

**1. The deliverable.** POST the file to the task's `upload_url`, from
`close_task_files`: a whole address, taking a multipart form with the file in
its "file" field, and no header. From a shell:

```
curl -sS -X POST "<upload_url>" \
  -F "file=@/path/to/workbook.xlsx" \
  -F "is_deliverable=true" -F "session_id=<session_id>"
```

Where you cannot post to an address, give the person the file to attach in
FullSeam, and say that is what is left.

Name the file the way last month's was named. Where a file is kept follows from
what it is, in an organization with its own file store:

| The file | Where it is kept |
|---|---|
| The deliverable: a document the task makes (`makes` under `documents` on `close_agent_get`) | Always the file store, in the document's folder. With no folder yet the upload is refused until `folder` is given. |
| A declared document the task reads and no task makes: a statement, an export | The document's folder in the file store, as the month's copy for everything that reads it. With no folder set and none given, FullSeam. |
| Anything else: support, a second copy | Your choice: the file store with `folder`, FullSeam without. |
| Scripts and notes kept with the agent | FullSeam, always. |

Where the organization keeps its files in FullSeam, everything is kept there.

With one document declared as made, the deliverable is filed as it by itself;
with several, pass the document's `id` as `file_template_id`. A document that
has a folder (`folder` on it in `close_agent_get`) goes there with `folder`
left out, which is the usual way.

`folder` beside an upload is where this month's copy goes and nothing more: for
a document with no folder yet, or in place of its own. It may be written out or
with the month's placeholders; either way nothing standing changes. Where a
document is kept from now on is said of the document, with `close_document_put`
(`close-configure`), which takes **Edit templates**. A `folder` that would go
unused is refused, not dropped: beside a document other work makes, or where
the organization keeps its files in FullSeam.

A document has one copy a month: filing it again replaces the copy before,
wherever that was, and the file replaced stays in the store. The answer names
it under `replaced`. Taking a new copy off does not bring the old one back. A
second copy of a workbook, for an auditor's folder say, is the same upload
without `is_deliverable` or `file_template_id`, with the `folder` it goes in:
it is put there and recorded as something the task read, in place of nothing.

A `file_template_id` is a document this task reads or makes
(`close_agent_get`). Any other is refused: declare it first, with
`close_template_files_put`. No `upload_url` means this
connection cannot change records, or this person may not change this task's
files.

**2. The entry, when the work ends in one.** It is a sheet of the deliverable,
and step 1 drafts it: `work-journal-entry` has the layout and how to read the
upload's answer. Where the organization records entries by their number (`work`
on `company_get`), nothing is drafted: the person books the entry in the ledger
and records its number on the task.

**3. What it was built from.** A read of the books is kept by passing `task_id`
to it. Anything else the work read — a statement, an invoice, an export — goes
to the same `upload_url` without `is_deliverable`, or by `close_file_put` if it
is a link or already in the file store. Where it is a declared document the task
reads and no task makes — a statement, an export — name it with
`file_template_id`: it is filed in that document's folder in the file store, as
the month's copy for every task and reconciliation that reads it.

A reconciliation takes its support document directly: `close_rec_get` gives,
with where its support comes from, an `upload_url` for the bytes (with `folder` for this
month's copy where the document has none), and `close_rec_prepare` with
`support_file` takes one already in the file store.

**4. The account.** `close_session_update` with a `summary` and `steps`: what
you did and what you found, in order, including every check and whether it
passed. The task page shows it as markdown and next month reads it back: where
there are several things to say, write a short list or a table. At most 100
steps, and a summary of up to 2,000 characters.

**5. What the next person needs.**

- *A script* you wrote or fixed is kept with the agent by `close_agent_file_put`. A
  file that is not text goes to the agent's `upload_url` with `session_id`
  beside it, as a file goes to a task's; where you cannot post to an address,
  give it to the person.
- *What you learned about this piece of work* — the item to watch, the
  judgement you made — is a note on it: `company_put` with the `task_id`, one
  fact to a note, each with a `path`, its `text`, a one-line `description`, a
  `status` and a `source`.
- *What is true of the whole company* is the same call without the `task_id`.
  A question you could not settle goes there too, with `status` `open`.
- *How the work is done*, if you worked it out: `close_agent_instructions_put`.

**6. The move.** `close_task_status` to `submit_for_review`, with a `note` of at
most 500 characters that the reviewer reads first: the result, and anything
open. More than that goes in `close_comment_add`. A task with a drafted entry
lands at `je_created` — the review of the entry — and that is the move
succeeding. Read the answer:

- `entry_not_read` — the task went to review without its entry. Take it back,
  fix the sheet, and submit again.
- `no_deliverable` — nothing on the task is its deliverable.
- `unrecorded` — the run has no account yet: step 4.

A task nobody reviews (`needs_review` false on `close_task_get`) has no
`submit_for_review`: its move is `done`, with the same `note` and the same
answers to read. Work that builds the support for a reconciliation is often
set up this way, because the review is the reconciliation's: submit that with
`close_rec_review`, as *Reconciling an account* in `fullseam` says.

A refusal names what is missing: a declared document not filed, checklist items
not signed as prepared (the person signs them in FullSeam; no tool here does),
or a move that is not this person's.

`close_comment_add` is for what a reviewer should know and the workpaper does
not show. `close_file_remove` takes a file back off. End the run with `status`
on `close_session_update`: `completed`, or `failed` or `stopped` if it did not
go well.

## After it is handed in

An entry does not change while its task is in review: `submit_for_review`,
`review_in_progress`, `second_review` or `je_created`. A workbook uploaded then
becomes the deliverable but its sheet is not read, so the workpaper on the task
no longer matches the entry under review. Upload a corrected workbook only once
the task is back in progress, or after changes are requested.

| status | how it comes back | then |
|---|---|---|
| `submit_for_review`, `review_in_progress`, `second_review` | the preparer moves it to `in_progress` | fix, upload, `submit_for_review` |
| `needs_changes` | the reviewer has asked for changes | fix, upload, `submit_for_review` |
| `je_created` | only the reviewer, by sending the entry back | — |
| `je_in_progress` | the entry was sent back, its booking has begun, or its preparer reopened the finished task | fix the sheet, upload, `je_created`. An entry already in the ledger is not changed from here: say so |
| `approved_for_je` | the workpaper is approved; the entry is still to book | upload the entry sheet, `je_in_progress`, then `je_created` |

`allowed_transitions` on `close_task_get` are the moves open to this person now.

## What not to do

- **Never assume an approval.** You prepare; a person reviews. Nothing here
  approves.
- **Never present something you did not fetch as data.** A field you did not ask
  for is not an empty field.
- **Never work around a missing input.** Say which one you could not read.
