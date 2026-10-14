---
name: getting-started
description: Use when somebody is new to FullSeam or asks what they can do here, when the company has no close under way yet, or at the start of a first conversation with a company that is still setting up. Says what FullSeam is for, what can be accomplished in it, and a first job to offer.
---

# Getting started in FullSeam

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

## What has to be there first

Three things are set up in the web app, under Setup, and cannot be done from here:

1. **The company's fiscal year end.** Year-to-date and last-year figures start from it.
2. **The books.** The accounting system connected. Until balances are in, there is nothing to reconcile or explain.
3. **A month.** The period being closed, created and then opened. Tasks cannot be worked before it is opened.

A second person is needed before a task can be reviewed, since whoever prepared a task cannot review it, unless the company lets the same person prepare and review (Settings, Close, Review).

`company_get` says which of these the company has. For a step that is done in the web app, `help_read` has the page and its address.

## A first job

Offer one that ends with something on the record, in this order of preference:

1. With balances in and a month open: reconcile one bank account for the month.
2. With a month and no balances: set up the month's tasks, one for each thing the person does at month end, each with who prepares and who reviews.
3. With neither: say which of the three above is missing, and where it is done.

Where this connection only reads, the job is not done some other way. In a chat, say what it would change in FullSeam and ask for the access — Read and write under Permissions — then wait. Through a connection from outside FullSeam, do the work and hand back what you produced for the person to attach and submit, as you were told when you connected.

## With somebody new

- Say in two or three lines what can be done here, in their terms. Do not list everything.
- Ask what they do at month end today, and offer the first job that fits.
- Use plain words. Say what a term means the first time: a *period* is the month being closed; a *reconciliation* proves one account's balance; the *preparer* does the work and the *reviewer* signs it off.

Once there is work to do, how it is done is another skill's: `close-task-work`
for a task, `work-journal-entry` for an entry and the schedule behind it,
`company-context` for writing down how the company works, and `fullseam` for
anything it is not yet clear how to file.
