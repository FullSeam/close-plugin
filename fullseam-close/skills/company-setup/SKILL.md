---
name: company-setup
description: Use when somebody is new to FullSeam or asks what they can do here, when the company is still setting up or has no close under way yet, or when somebody wants to bring in their close or choose what to automate first. Takes them through setup a step at a time — where they are, their books, their close today, the first month, reconciliations, review — and asks before every change.
---

# Setting up a company in FullSeam

FullSeam is where a company's month-end close is run and kept. The books stay
in the accounting system. What happens at month end lives here: the checklist,
the reconciliations, the entries and the workpapers behind them, who prepared
each and who reviewed it.

You do the work with the person. FullSeam keeps the record of it.

## What you can do

- **Run the month's checklist.** Each piece of month-end work is a task, with someone who prepares it and someone who reviews it. Ask: "What is left for September, and what is late?"
- **Reconcile accounts.** Prove a balance against something outside the books, such as the bank statement for a bank account or a schedule for prepaids, and have it reviewed. Ask: "Reconcile the checking account for September."
- **Book month-end entries.** Accruals, prepaid amortisation, depreciation and reclasses, each drafted with the workbook that supports it and reviewed before it is booked. Ask: "Accrue September payroll."
- **Explain what moved.** Each account against last month and last year, down to the entries behind a change. Ask: "Why did software expense go up in September?"
- **Read the books.** Balances, the chart of accounts, ledger lines and the documents attached to them, as of any month end. Ask: "What was in the bank at the end of September?"
- **Write down how the company works.** Its policies, where its documents are kept, how its chart is used, so the same work is done the same way next month. Ask: "Note that we capitalise anything over $2,500."
- **Set work up to repeat.** A task carries its instructions and its files into next month, so the close is built once and then rolled forward. Ask: "Set the payroll accrual up so it runs the same way every month."

## Before every change

Explain, then ask. Before you make anything — a month, tasks, how an account is
reconciled — say what you are about to make and list it: each name, the month,
the accounts, who prepares and who reviews. Then wait for a yes. A yes covers
what you listed and nothing more; anything you add afterwards is listed and
asked again. Reading needs no asking.

Where this connection only reads, the change is not made some other way. In a
chat, say what it would change in FullSeam and ask for the access — Read and
write under Permissions — then wait. Through a connection from outside
FullSeam, do the work and hand back what you produced for the person to attach
and submit, as you were told when you connected.

## Where they are

Start by reading `company_get`. While the company is setting up, its `setup`
says what its setup bar says: whether the books are connected and how many
months of balances are in, the file store, and whether the person has
connected Claude. Once setup is finished it says only that. It is said only
to someone who may change the close's settings; anybody else is told whether
setup is open, and the steps are theirs to ask about, not to do.

A company that signs up has its first month opened already, and its settings
at their defaults: the year ends in December, chats may change records, and
sign-in is the way they signed up. Settings has each; bring one up only when
it matters to what they are doing.

A person can try a sample agent, from Get started or from the nav: it puts a
month of made-up payroll in the company — a Sample Payroll Entry task, its register,
a report, accounts numbered S1000 and on and named "(sample)", their recs, and
an agent to set up in a chat waiting on its first questions. Purge all sample data on
Get started takes all of it away. Its figures and people are invented: never
take them for the company's, and never carry them into its own work.

Say in a line what is done and what comes next. Skip what is done. Take the
steps below in this order unless the person wants another; every one can be
left for later. The setup bar has Accounting system, File store and Claude.

Ask one question at a time.

## 1. The books

**Why.** Connecting the accounting system brings in the chart of accounts, the
layout of the balance sheet and income statement, and about two years of
month-end balances. Without balances there is nothing to reconcile or explain
and the statements stay empty. Tasks, their files and their reviews work
without it.

**How, for QuickBooks.** Under Setup, Accounting system, the person presses
Connect QuickBooks, signs in at Intuit and chooses a company. It is done in
their browser; you cannot do it for them. The history comes in by itself over
a few minutes.

Each Intuit company is one set of books. A company on Intuit Enterprise Suite
with several entities connects them one at a time: after the first, Connect
another (the button names what the company calls an entity, `entity_word` in
`company_get`), and sign in again choosing the next. Each becomes an entity of
its own here. The parent is one of them, with its own books; FullSeam does not
pull Intuit's consolidated reports.

**Another ledger.** NetSuite and the others are connected with FullSeam. Under
Setup, Accounting system, they say which they use, and FullSeam is told.

`help_read` has Get started and its address.

## 2. Their close today

Ask what they do at month end now: a checklist they can paste or upload, or a
list from memory. Or, if they would rather start small, the one thing they
would most like done for them.

From a list, draft the tasks: each one's name, the entity it is for, whether it
books a journal entry, and who prepares and who reviews it (`company_get(view=people)`
lists who is here). Show them as a table and ask. A task belongs to a month, so
if there is none yet, the month comes first (step 3). Then make each with
`task_create`.

A checklist of dozens of lines goes in faster through the web app's task
import, on the month's page. Say so, and offer it.

From one thing to automate, make it one task, then offer to set it up to repeat
the same way every month (`close-configure`).

## 3. The first month

A company that signed up has its first month open and its close already
started: its tasks can be worked as soon as they are made. A close you were
told is open is started; do not tell them to start it.

Where there is no month, offer the one in `suggested_period`: the one they
would be closing now. Say which month and why — "you would be closing
September now" — and ask. Then make it with `period_create`.

A month made here is in Setup: tasks can be put on it and arranged, not yet
worked. Starting the close is what lets work begin, and it cannot be undone.
It is the person's to do, with Open close on the month's page, once its tasks
are in place. You cannot start it.

## 4. Reconciliations

Ask whether they want to reconcile accounts here: prove each account's balance
against something outside the books, each month, with a reviewer signing it
off. If not, say it can be set up later, and move on.

If so, ask which accounts. Do not assume which kind; with the books connected,
`books_accounts` lists the chart to choose from. For each group, ask how it is
proved — `rec_setup(action=accounts)` with no preset lists the ways — and who prepares and
reviews it. List what you will set up, ask, then set it up.

Without the books connected there are no balances to reconcile. Say so and
come back to it once they are in.

## 5. Review and the team

A task is reviewed by someone other than whoever prepared it, unless the
company lets one person do both (Settings, Close, Review). Ask whether someone
else will review. People are invited under Team, in the web app.

## A first job

Once there is a month, offer one that ends with something on the record, in
this order of preference:

1. With balances in and a month open: reconcile one bank account for the month.
2. With a month and no balances: set up the month's tasks, one for each thing the person does at month end, each with who prepares and who reviews.
3. With neither: say which is missing, and where it is done.

## With somebody new

- Say in two or three lines what can be done here, in their terms. Do not list everything.
- Use plain words. Say what a term means the first time: a *period* is the month being closed; a *reconciliation* proves one account's balance; the *preparer* does the work and the *reviewer* signs it off.

Once there is work to do, how it is done is another skill's: `close-task-work`
for a task, `work-journal-entry` for an entry and the schedule behind it,
`close-configure` for setting work up to repeat, `company-context` for writing
down how the company works, and `fullseam` for anything it is not yet clear how
to file.
