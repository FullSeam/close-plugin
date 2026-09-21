# FullSeam close, from your own Claude

An installable Claude Code / co-work plugin that reaches a FullSeam close over
MCP. The accountant runs their own Claude, on their own API key, with their own
skills; FullSeam stays the record of what was done.

## What it is

- **`fullseam-close/.mcp.json`** — points Claude at the FullSeam MCP endpoint.
- **`fullseam-close/skills/close-task`** — how to work a close task with these
  tools.
- **`fullseam-close/commands/close.md`** — `/close`, the day's work.

The server is `backend/app/mcp/` in the FullSeam repository, where this
directory is also written: the skill makes claims about what the API returns,
and a test there checks them against it. `publish-plugin.yml` copies this
directory here on a `plugin-v*` tag.

## Install

```bash
claude plugin marketplace add FullSeam/close-plugin
claude plugin install fullseam-close@fullseam
```

Working on the plugin itself: add the directory instead of the repository, and
re-sync after every edit — what is installed is a snapshot, not a link.

```bash
claude plugin marketplace add <path to>/evergreen/plugin
claude plugin marketplace update fullseam
claude plugin uninstall fullseam-close@fullseam
claude plugin install fullseam-close@fullseam
```

The token has to be in the environment of the shell that launches Claude, not
set once it is running:

```bash
FULLSEAM_TOKEN=fs_pat_... claude
```

`FULLSEAM_URL` is optional; it defaults to the local dev stack. Set it to reach
another one.

Two things that bite:

- **Never write `localhost` in the URL.** It resolves to `::1` first and the dev
  containers bind `127.0.0.1` only, so the connection is reset rather than
  refused, and the error says nothing useful. Always `127.0.0.1`.
- **The installed plugin is a snapshot, not a link to this directory.** After
  editing anything here, re-sync it:

  ```bash
  claude plugin marketplace update fullseam
  claude plugin uninstall fullseam-close@fullseam
  claude plugin install fullseam-close@fullseam
  ```

## Getting a token

Issued from the backend until there is a screen for it:

```bash
docker compose -f docker-compose.dev.yml exec backend \
  python manage.py issue_mcp_token --email demo@evergreen.com --name "Claude Code"
```

The token is printed once. It is the person, not a service account: every call
it makes runs the same permission checks that person's browser session would,
so an agent holding it can do no more than they can.

## What it can do

v0 is reads plus the session:

| Tool | |
|---|---|
| `close_whoami` | who the token is, what they may do, which closes are open |
| `close_my_work` | their tasks across every open close, and what each waits on |
| `close_tasks_list` | tasks in a period, filtered |
| `close_task_get` | one task in full, with the moves this caller may make |
| `close_task_procedure` | the playbook the firm expects the task to follow |
| `close_task_prior` | last month's task and what it produced |
| `close_task_files` | the task's files, each marked link or stored bytes |
| `close_file_read` | a workbook as sheets and rows, text as text, a link as a link |
| `close_task_activity` | the trail: comments, status moves, reviews |
| `close_session_open` / `close_session_close` | put the run on the task's trail |

Writing a file back and moving a task's status are not exposed yet. The code
that does either one correctly still lives inside `close/views.py` request
handlers — `task_files` POST and the `task_detail` PATCH — and skipping any part
of what they do (declared-role resolution, the activity row, telling the recs
that read the file as support) leaves the record wrong. Those extractions are
the next step.

## Co-work

Co-work runs in Anthropic's cloud, so `FULLSEAM_URL` has to be an address it can
reach — a local stack will not do. Everything else is the same.
