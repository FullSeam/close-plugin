---
name: close-task
description: Work a month-end close task from FullSeam — find it, read the procedure and the prior month, do the work, and hand it back. Use whenever the user mentions their close, a close task, a workpaper, an accrual, a reconciliation, or names a task from FullSeam.
---

# Working a close task

FullSeam holds the close: the task list, who prepares and who reviews each one,
the written procedure, the files, and the trail of what happened. You do the
work; FullSeam stays the record of it.

## Start here

The server tells you who you are acting as, which closes are open and how much
is on their plate when it connects — do not spend a call re-asking. Go straight
to `close_my_work`.

`close_whoami` adds the full permission list and the entity codes. Most
questions do not need either; call it when one does.

**More than one close is normally open at once** — the prior month is often
still being finished while the current one is under way. Do not assume the
newest is the one meant.

## The loop

1. **`close_session_open`** with the task id and a one-line `intent`. This puts
   the run on the task's trail, so a reviewer can see the work was an agent's
   and whose. Do this before the work, not after.
2. **`close_task_get`** — status, people, dates, dependencies, and
   `allowed_transitions`: the moves this person may make. A task whose
   `waiting_on` still has unfinished entries is not ready.
3. **`close_task_procedure`** — the playbook the firm expects this task to
   follow. **Read it before deciding how to do the work.** It is the firm's
   procedure, not a suggestion, and it is why the answer has to match last
   month's.
4. **`close_task_prior`** — last month's version of the task and the files it
   produced. Most close work is a roll-forward: start from what was done, not
   from scratch.
5. **`close_task_files`** and **`close_file_read`** — the inputs.
6. **Do the work** locally: build or update the workpaper, compute the figures.
7. **`close_session_close`** with a summary of what you did and what you found.

## Handing the work back

**v0 cannot write files or move a task's status.** When the work is done, say
plainly what you produced, where it is, and what the person now has to do in
FullSeam — attach the workpaper and submit it. Do not claim the task was
submitted.

## Files

Every file says whether it has bytes here:

- `"content": "stored"` — `close_file_read` returns it. Workbooks come back as
  sheets and rows; csv and text come back as text.
- `"content": "link"` — the bytes live in Egnyte, Google Drive or SharePoint.
  `close_file_read` returns the address. **Fetch it with that system's own MCP
  server** if one is connected. If none is, say which file you could not read
  and ask for it — do not work around a missing input by assuming its contents.

A file you could not read is a hole in the workpaper. Name it.

## What not to do

- **Never state a figure you did not compute or read.** A close figure that
  cannot be traced to a source is worse than a missing one.
- **Never assume an approval.** You prepare; a person reviews. `close_task_get`
  publishes `may` — everything this person is permitted to do with the task. If
  `approve` is not in it, the work is not reviewable by them either.
- **Do not guess which close a task belongs to.** Ask, or read
  `open_periods` and check.
- If a tool refuses, read the refusal. "No task … you can see" means this
  person's permissions do not reach it, not that it is missing.
