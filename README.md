# FullSeam close, from your own Claude

Work your month-end close tasks from Claude Code. FullSeam holds the close — the
task list, who prepares and who reviews, the procedure, the files and the trail
of what happened — and answers over MCP. You run your own Claude, on your own
key, with your own skills.

## What is here

| | |
|---|---|
| `fullseam-close/.mcp.json` | where the FullSeam server is, and the token to reach it |
| `fullseam-close/skills/close-task` | how to work a close task with these tools |
| `fullseam-close/commands/close.md` | `/close` — the day's work |

## Install

```bash
claude plugin marketplace add FullSeam/close-plugin
claude plugin install fullseam-close@fullseam
```

## Your token

Settings → General → Access tokens, in FullSeam. Name it, choose when it
expires, and it is shown once. **Connect Claude Code** on that panel gives you
the three commands with your organization's address and the new token already
in them.

A token is you: every call it makes is permitted or refused exactly as it would
be for you in the web app. Revoke it from the same panel and the next call
fails.

## Two variables

Both go in the environment of the shell that **launches** Claude. Setting them
inside a running session does not reach the MCP client.

```bash
FULLSEAM_URL=https://your-org.fullseam.com/api/mcp/ \
FULLSEAM_TOKEN=fs_pat_... \
claude
```

| | |
|---|---|
| `FULLSEAM_TOKEN` | required |
| `FULLSEAM_URL` | your FullSeam address with `/api/mcp/` on the end. Defaults to `https://close.fullseam.com/api/mcp/`; set it if your organization is somewhere else. |

To keep them:

```bash
echo 'export FULLSEAM_URL=https://your-org.fullseam.com/api/mcp/' >> ~/.zshrc
echo 'export FULLSEAM_TOKEN=fs_pat_...' >> ~/.zshrc
```

## Using it

`/close` lists what you are on the hook for across every open month. Or ask in
your own words — "what's left on the September accrual?", "pull up last month's
property tax workpaper".

## What it can and cannot do

Reads your tasks, their procedures, last month's versions, their files and their
comment trail, and records that an agent worked a task and on whose behalf.

It cannot attach a file or move a task's status. When the work is done it tells
you what it produced; you attach and submit it in FullSeam.

A file your organization keeps in Egnyte, Google Drive or SharePoint comes back
as its address rather than its contents. Fetch it with that system's own MCP
server if you have one connected, or open it yourself.

## If it will not connect

| | |
|---|---|
| `401` | the token is wrong, revoked, or expired — issue a new one |
| connection refused or reset | `FULLSEAM_URL` is wrong. Never write `localhost` |
| the tools are missing | the variables were not set in the shell that started Claude |

`claude mcp list` says whether the server connected and why not.

## Working on the plugin

It is published from `FullSeam/evergreen`, where it sits beside the server it
talks to — the skill states what the API returns and a test there checks those
claims against it. Add the directory instead of the repository, and re-sync
after every edit: what is installed is a snapshot, not a link.

```bash
claude plugin marketplace add <path to>/evergreen/plugin
claude plugin marketplace update fullseam
claude plugin uninstall fullseam-close@fullseam
claude plugin install fullseam-close@fullseam
```

A `plugin-v*` tag publishes it here.
