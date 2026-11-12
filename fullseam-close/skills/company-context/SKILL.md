---
name: company-context
description: Learn how a company keeps its books and write it down in FullSeam, one fact to a note, so every later session starts with it — its entities and fiscal year, how its chart of accounts is used, where each kind of document lives, its accounting policies, and the questions nobody has answered yet. Use when connecting to a company for the first time, when asked to explore or document how it works, or when the notes are thin.
---

# Learning a company

A session that starts cold works the same things out every time, or guesses.
This is the work of finding them out once, from the company's own ledger, files
and past months, and keeping each answer where the next session will find it.

## How what is known is kept

`company_get` has two halves.

**`company`** is what FullSeam itself knows and computes when asked: entities,
fiscal year ends, the ledger each is connected to and what it can be asked, the
file store and its top folders, the open months. You never write that.

**`notes`** are what somebody learned and wrote down, there for whoever may read
them (**See company notes**); writing one takes **Edit company notes**. Without
that, give the person what you found, note by note, for them to save.
Each note is **one fact**, at a **path** that says what it is about:

```
profile/business
systems/payroll
chart/prepaid-expenses
documents/invoices
policies/prepaids/capitalisation-threshold
policies/prepaids/part-month-convention
skills/flux-commentary
```

The paths are the tree; there is no folder to make. `company_get` lists every
note by its path with one line on what it says, and no more; the connect message
lists up to forty, open questions and stated notes first. A body is read by its
path — `company_get` with `path` — and a whole branch by the path above it:
`policies/prepaids` returns both notes under it.
Nothing long is loaded unasked, which is what lets there be hundreds.

Every note says how far it can be trusted, in `status`:

| | means | needs |
|---|---|---|
| `stated` | a person, or a document that decides it, says so | a `source`: who, or which document |
| `observed` | you saw it in the data | a `source`: what you read |
| `open` | a question nobody has answered | the question, and what turns on it |

An observation is not a policy. "Every item on the schedule runs six months or
longer" is `observed`. It does not say a shorter item is expensed, and must not
be written as if it did.

Every note also says whose words it is. `by_agent` is true on one a chat
wrote, and the connect message marks it "by a chat": it is that chat's
report of what a person or a document said, until a person saves the note
themselves. Read a `stated` note that carries it as a claim to check against
its `source` when much turns on it. Everything you write carries it.

## Start from what is expected

Every company is expected to have a short list of things written down.
`company_get` returns the ones this company has not, in `not_written`: each a
`path` and what a note there `asks`. **That list is the job.** Onboarding a
company is writing every one of them, as what you found or as a question, so
that none is left unsaid. An entry is met by a note at its path or anywhere
beneath it, so a branch like `chart` is met by however many notes it takes.

Then read what is already there. The index is in the connect message; read the
branches that bear on what you are about to look at before you look. A note the
data now contradicts is corrected in place, and the correction says what
changed. The tree grows well past the expected list: write whatever else the
next session would otherwise have to work out.

## What to find out, and where

Work through these. Stop exploring a subject when the next file is telling you
what the last three did.

**`profile/…`** — what the business is and the shape of its year.
`books_balances` over the posted months, by `statement`: where revenue and cost
sit, what is seasonal, which balance sheet accounts carry real balances.

**`systems/<name>`** — one note for each system the company's data lives in
that FullSeam is not connected to: where payroll is run, the banks and cards,
how customers are billed, how bills and card spending reach the ledger. The
ones it *is* connected to are in `systems_connected` on `company` and need no
note. For each: what it holds, who has access, how data gets out of it — which
report or export, made by whom, when — and where that lands. Say whether you
could reach it yourself from this session, since that decides whether the next
one fetches the data or asks for it. You will rarely see these systems
directly: what the ledger shows of them — a payroll journal with a provider's
name on it, a bank feed's descriptions — is `observed`, and the rest is an
`open` question.

**`chart/…`** — how the chart is *used*, which the chart itself does not say.
One note for each account or group that needs saying something about: what goes
through it, how it differs from the account it looks like, that it is dormant.
`books_accounts` for what exists, `books_balances` for which move,
`books_entries` on the busy ones.

**`documents/…`** — one note for each kind of document a close needs:
workpapers, bank statements, invoices, contracts, policy documents. Where that
kind lives, how the folders run by month, how the files are named.
`file_store_find` by `folder`, from the top folders in `company`; open a few
with `file_read` and the `store_file_id`, which reads a file where it
sits without making it any task's. A kind kept in the ledger and not the store
— an invoice attached to its bill — is said so: `books_read` with `read`
`transaction` shows a transaction's `documents`. A kind that comes out of
another system is said so, naming its `systems/` note, not described again. A
kind you could not find anywhere is an `open` note.

**`policies/<area>/…`** — one note for each rule: a capitalisation threshold,
how a part period counts, when a bill is dated, how entries are numbered. Write
`stated` only for what a document or a person says. What the data merely shows
is `observed`. What the work turns on and nothing settles is `open`.

**Recurring work** is not a company note. What is true of one piece of work —
the vendor whose bill is always late, the sheet that must tie — is kept with
that work: `company_put` with its `task_id`. Its path and one line then come
back with `template_get(view=template)` for anyone working it, and what it says is read
with `company_get`, that `task_id` and the `path`.

## Writing a note

`company_put` with a `path`, the `text`, a one-line `description`, a `status`
and a `source`. Pass your `session_id`. Saving at a path that exists replaces
the note and keeps what it said before, and the answer's `outcome` says which
happened: made, replaced, moved, restored or unchanged. If you meant a new note
and it says replaced, you have written over somebody's: read what it said with
`company_get`, the `path` and `history`; save yours at a path of its own, then
save their last version back at the original path, with its description, status
and source. Moving yours with `replaces` would take their note's history with it
and leave the original path empty.

A path is letters a to z, digits and hyphens, with `/` between its parts. The
`source` is a line: who said so, or which document. What it says is the note.

- **One fact to a note.** If it has two headings, it is two notes.
- **The path says what it is about**, most general part first, so related notes
  share a branch and one read fetches them.
- **The description is all a reader sees** before choosing to read the note.
  Make it the fact, not a label: "Annual policies are capitalised and released
  monthly", not "Prepaid treatment". For a question, the question. Do not
  repeat the status in it: the index already marks an open one.
- **A question is asked at the path its answer will live at.** A person answers
  it in FullSeam by editing that note, and the next session reads the answer
  where you would have looked for it. Say what turns on the answer and what the
  entry would be each way.
- **A note in the wrong place is moved, not copied.** Write it at the path it
  belongs at with `replaces` naming the old path. It is the same note at a new
  address and keeps what it said before. The new path has to be free: a note
  is not moved onto another. Never leave a note that only says where another
  one went.
- **Do not copy the data.** A note says where the bank statements are, not what
  is in them. Balances go stale; where to read them does not.
- **Describe the company, not this system.** No tool names and no ids of ours.
  A person reads it too.
- Short. A few lines.

A **skill** is the other thing the tree holds: how the company does a kind of
work — the method for flux commentary, the house style for a board pack. It is
longer than a fact and is read only when that work comes up. `company_put` with
`kind` `skill`, usually under `skills/`. Write one only when a person has told
you the method or it is written down; a method you inferred is a note,
`observed`.

Then tell the person what you wrote: how many notes under each branch, every
`open` question, since those are theirs to answer, and anything still in
`not_written`. `open_questions` on `company_get` lists the questions.

## What this skill does not do

Doing a piece of work is `close-task-work`. Writing down how one piece of work
is done — its instructions, its script — is `close-configure`. What is where in
FullSeam is `fullseam`.
