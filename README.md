# FullSeam close, from your own Claude

Work your month-end close tasks from Claude. FullSeam holds the close — the
task list, who prepares and who reviews, each task's agent, the files and the trail
of what happened — and answers over MCP. You run your own Claude, on your own
key, with your own skills.

## What is here

| | |
|---|---|
| `fullseam-close/.mcp.json` | where the FullSeam server is |
| `fullseam-close/skills/fullseam` | where to start: what FullSeam holds, where a figure or a file comes from, when work needs a task, where the month stands |
| `fullseam-close/skills/company-setup` | a company new to FullSeam: what to set up first, and bringing in the checklist it already works from |
| `fullseam-close/skills/company-context` | learning a company and writing down how it works, for every later session |
| `fullseam-close/skills/close-task-work` | working one task: how it is done, last month's version, its files, handing it back |
| `fullseam-close/skills/close-configure` | setting work up: its template and its agent — the instructions, scripts and notes, and the file templates it reads and makes — or an agent of its own; and trying it with a test run |
| `fullseam-close/skills/agent-setup` | turning work done by hand into an agent: what to gather, proving it on a month already done, handing it over |
| `fullseam-close/skills/work-journal-entry` | rules for work that ends in a journal entry, and the sheet the entry is read from |
| `fullseam-close/skills/flux-analysis` | why an account moved: finding the cause in the ledger and drafting the explanation on the flux |
| `fullseam-close/skills/report-pages` | a report page over the books and the files, kept for people to open and filter |

## Install

### Claude app

Customize → Plugins → upload the plugin as a .zip, or add this repository as a
marketplace. Then connect FullSeam: sign in when Claude asks, and press
**Connect**. Your organization's Claude owners decide whether members may add
plugins and connectors.

### Claude Code

```bash
claude plugin marketplace add FullSeam/close-plugin
claude plugin install fullseam-close@fullseam
```

## Sign in from Claude Code

Start Claude, run `/mcp`, choose **fullseam** and authenticate. A browser tab
opens on FullSeam: sign in if you are not already, and press **Connect**.

The screen says which organization the connection reaches. It only reads unless
**Change records** is ticked, and any permission unticked there is left off it.
It reaches no further than you do in the web app. Settings → General → Access
tokens lists it under **Connected agents**; disconnect it there and the next
call fails.

## Your organization's address

```bash
FULLSEAM_URL=https://your-org.fullseam.com/api/mcp/ claude
```

Your FullSeam address with `/api/mcp/` on the end, in the environment of the
shell that **launches** Claude. It defaults to
`https://close.fullseam.com/api/mcp/`; set it only if your organization is
somewhere else. To keep it:

```bash
echo 'export FULLSEAM_URL=https://your-org.fullseam.com/api/mcp/' >> ~/.zshrc
```

## Without a browser

A script, or a machine with no browser, uses an access token in place of
signing in: Settings → Agents → Access tokens, shown once when it is made.
Add the server yourself with the token as its header:

```bash
claude mcp add --transport http fullseam https://your-org.fullseam.com/api/mcp/ \
  --header "Authorization: Bearer fs_pat_..."
```

## Using it

Ask in your own words — "what am I on the hook for this month?", "what's left on
the September accrual?", "pull up last month's property tax workpaper".

## What it can and cannot do

| | |
|---|---|
| The checklist | reads your tasks, their trail and comments; creates a task; comments; moves a status |
| How work is done | reads and writes an agent — a task template's, or one of your own on a reconciliation or on nothing: its instructions, the scripts and notes kept with it, and the file templates it reads and makes; starts it, or a test run of it, and reads what it did |
| The books | reads the chart and each account's balance and movement by month; with **See ledger detail**, the entries behind an account, from the accounting system your organization has connected |
| The flux | reads each account's movement and what has been said about it; drafts an explanation, which shows as a draft until a person accepts it; comments |
| Files | reads a task's files and fetches them whole; attaches a file by upload, by link, or from your organization's connected file store |
| Reports | builds report pages over the books and files |
| The run | records what it did on the task, under your name |

Changing anything needs two things: your organization to have turned on writing
from outside the web app, and a connection approved with **Change records** ticked.
Without both it reads, and hands you what it produced to attach and submit in
FullSeam. Claude is told at connect whether it can change records.

It does not post to your accounting system. An entry it prepares is a
`FullSeam JE` sheet in the workbook it hands back, which FullSeam reads into a
draft on the task for the reviewer to approve.

A file FullSeam holds only a link to — in Egnyte or SharePoint, or in a Drive it
is not connected to — comes back as its address. Fetch it with that system's own
MCP server if you have one connected, or open it yourself.

## If it will not connect

| | |
|---|---|
| "needs authentication" | `/mcp`, choose **fullseam**, authenticate |
| `401`, "Disconnected. Connect again." | it was disconnected in Settings; authenticate again |
| "Your account cannot connect agents" | an administrator adds **Create access tokens** to one of your roles |
| connection refused or reset | `FULLSEAM_URL` is wrong. Never write `localhost` |
| the tools are missing | `FULLSEAM_URL` was not set in the shell that started Claude |
| every tool says "Requires access to the close module" | your FullSeam account does not open Close |
| `429` | too many calls in a minute; it clears by itself |

`claude mcp list` says whether the server connected and why not.

## Privacy

The plugin runs nothing on your machine. Its tools are calls to the FullSeam
server at `FULLSEAM_URL` (`https://close.fullseam.com/api/mcp/` by default),
signed in as you; what they send and receive goes there and nowhere else.
FullSeam's privacy policy: https://fullseam.com/privacy
