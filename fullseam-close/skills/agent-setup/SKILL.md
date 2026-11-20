---
name: agent-setup
description: Help a person turn work they do by hand into an agent — where it lives, what to gather from them, how to understand and prove the work, and trying it before it is trusted. Use when someone wants an agent for work they do today, hands over how they do it (a transcript, a procedure, a workbook, past work), or asks how to start setting an agent up.
---

# Setting an agent up

`close-configure` says what an agent is made of and which tool writes each
part. This is about getting from what the person knows to an agent that does
the work as they would.

## Where it lives

Ask whether the work is on the close checklist.

- **On the checklist:** it is a task, and the agent is its template's. It
  follows the checklist's review rules and counts in the month's progress. Work
  that already has a template gets that template's agent, not a second one.
- **Off it:** an agent of its own (`template_put(action=agent)`). With an
  `account_id`, it prepares that account's reconciliation, and the rec's review
  is its review. With none, its person reviews its runs where **Reviewed** is
  ticked.

## Started on a piece of work

Asked from a task or a reconciliation, the work is already in FullSeam. Read
what it is first: its name, its checklist template, its accounts and entities, who
prepares and reviews it. Then put these questions to the person, together:

1. **Where it lives.** Only when started on a reconciliation: on a task that
   works it, as the task's template's agent, which prepares the rec's support
   as part of the task (one option for each task, one whose template already
   has an agent first); or its own, an agent on the rec's account that
   prepares the rec.
2. **Agent type.** Which of the subjects below the work is: the one it looks
   like first, said to be suggested, up to two others it could be, and
   "Custom". Each option's description is the subject's line below.
3. **What it delivers.** What it hands in each month, more than one allowed:
   a journal entry drafted, the rec's workpaper, a schedule or workbook,
   a report page in FullSeam, findings only. What the subject usually
   delivers first, said to be suggested. For reporting work, offer a report
   page in FullSeam (`report-pages`) beside the deck or workbook they make
   today; for a flux review, the explanations on FullSeam's flux page
   (`flux-analysis`).
4. **Files and SOPs.** What to learn from, more than one allowed: past
   months (how it was done before, and the files used), files they will
   attach, procedures or notes they will write. Past months first, said to be
   suggested, where there are any. Left unanswered, none.

Agent types are named by what an accountant calls the work. Each, with what
it usually delivers, and its own skill where it has one:

- **Prepaid expenses:** the prepaid schedule rolled forward and the
  amortization entry. `agent-prepaids`.
- **Fixed assets and depreciation:** the fixed asset roll-forward and the
  depreciation entry. `agent-fixed-assets`.
- **Accrued expenses:** the month's accruals, what each rests on, the entry
  and its reversal. `agent-accruals`.
- **Revenue and deferred revenue:** the month's revenue from its source, the
  deferred balance rolled forward, and the entry. `agent-revenue`.
- **Payroll:** payroll's register booked, and wages, bonuses, PTO and
  benefits accrued.
- **Cash and bank:** each bank and card account tied to its statement, with
  the reconciling items.
- **AP and AR:** the payables or receivables aging tied to the ledger.
- **Intercompany:** balances between entities that agree and eliminate.
- **Leases:** the lease schedules, the right-of-use asset and the liability,
  and the entry.
- **Debt and interest:** loan balances tied to the lender's statements, and
  interest accrued.
- **Allocations:** costs spread across departments, entities or lines by a
  basis, and the entry.
- **Tax and filings:** a return or a report due to an agency, prepared from
  the books, and the tax accrued.
- **Flux review:** the month's movements against last month and the
  average, with what explains each. `flux-analysis`.
- **Custom:** none of these.

Once a type is chosen, say in a line or two what that work usually involves
here, so the person sees where the agent starts from, and read its skill
where it has one. The person may change the type later; go on from the new
one.

Where they said to read the past months: its last few months
(`task_get(view=prior)`, and the months before that), what was handed in each
month and who prepared and reviewed it, the comments and what was sent back,
the reconciliation's balances and items where there is one (`rec_get`), and
the company's notes on the subject. Open the last finished file.

Then say back what the work looks like, in a few lines: what comes in, what is
done, what goes out, who reviews it. Ask only what that left open. Go on a
step at a time: how it is done, building it, testing it on a past month,
going live.

## The company's rules: read, then confirm

Each subject's skill lists the rules the work turns on: for fixed assets, the
capitalization threshold, useful lives, the first month's convention. Work
each out from the past months before asking: last month's schedule, the
entries, what was capitalized and what was expensed. Then put them to the
person together, as what was found, with the figures that show it:

> Last month's schedule depreciates computers straight-line over 36 months,
> from the month after purchase, and nothing under $2,500 was capitalized.
> Is that the policy?

Ask cold only what the past months do not show, or where there are none.
Check the company's notes under the subject's `policies/` path first: what a
person stated there is already settled. Write each rule they confirm there
(`company-context`), `stated`, with them as its source; one found and not yet
confirmed is `observed`.


Where the page beside the chat shows the setup's steps, report where each
stands as it starts and ends, with a few words on each: the agent type chosen
("Custom" included), with where it lives when that was asked; what it
delivers; the months read and the files given. List there too each file you
find that the work uses, with where it is.

## Connections

The best agents fetch their own inputs each month instead of waiting for
someone to upload them, so every system the person connects makes the agent
more able to run on its own. Where the accounting system or the file store is
not connected (`company_get`), say so in a sentence, and where to connect
them: Setup, Accounting system; Setup, File store. Where the person says the
work's files come from another system, such as payroll or a bank, suggest
connecting that too: Setup, Other External Connections.

## What to gather

Ask for what explains the work, in whatever form the person has:

- **How it is done, told:** a transcript of them walking through it, or of
  the call where they explained it. A recording cannot be read here.
- **How it is done, written:** a procedure, a checklist, notes, a skill or
  prompt they already use.
- **What goes in:** an example of each input, with what it is, where it comes
  from and when it arrives.
- **Work already done:** has this been done before? A past month's finished
  work shows what the agent has to produce, and is the answer to test against.

Not everyone has all four. Start with what there is and ask for the rest when
it is needed.

## Understanding it

Look at a large file before reading it: its sheets, their sizes, where the
headers are, a few rows, the kinds of formulas. Then read the parts that
matter. Finished work often holds its own inputs: a workbook with the month's
reports pasted in can give them back as the files they arrived as.

Talk to the person early. After a first look at what they gave you, say back
what the work is, in a few lines: what comes in, what is done to it, what goes
out and where, who looks at it. Say what you would look into next, and wait for
them. Working out how a file is built inside, or reading a long document whole,
comes after they agree that is the work. Ask only what the material does not
answer, with the figures that raised the question. Rules the person already follows are theirs: keep them, and ask
before changing one.

## Shapes of work

Work comes in different shapes, and the agent follows the shape:

- **Inputs to outputs:** reports in, a workbook or an import out. The mechanical
  part is a script kept with the agent, run the same way every month. A run in
  FullSeam works on workbooks with formulon, FullSeam's own build of an Excel
  365 engine; a script that uses it runs only in FullSeam, not on another
  machine.
- **Tying out:** a balance against its support, as in a reconciliation.
- **Gathering:** chasing documents or answers from people, and following up.
- **Reviewing:** checking another's work, or the books, for what looks wrong.
- **Judgment:** an estimate, an accrual, an explanation of a movement.

Most work mixes them. Put what is the same every month in scripts and standing
files, and what takes judgment in the instructions, with the person's rules
written out.

Sort what the work touches by how often it changes. What arrives each month is
a file template it reads. What it makes each month is one it makes. What stands
(a rate table, a mapping, a list to leave out) is a project file with the
agent. What arrives only now and then is asked for in the run.

## Proving it

Where there is work already done, run the agent's way on that month and
compare to it. Compare line by line, not in total. Each difference is either a
mistake or a rule, and each rule is named to the person. Where nothing has been
done before, the person checks the first run as they would a new colleague's.

Save scripts and standing files once they are proven, then write the
instructions (`close-configure`). Then try it: `run_start` with `test: true` on
the month already done. The test needs that month's inputs where a run would
look for them. Find them in FullSeam or the file store if they are there, and
otherwise ask the person where they are. Where it goes wrong, change the agent,
not the run, and try again.

## Going live

Give the person its page, and tell them in a few lines what it will ask them
for each month, what it makes and where it lands, who reviews it, and what is
still open. Its schedules are on its page; the person turns them on.

A new agent is in setup: its triggers start nothing and it gets test runs
only. When the person says it is ready, make it active
(`template_put(action=state)`).

## What this skill does not do

What each part is and the tool that writes it is `close-configure`. Doing a
month's work is `close-task-work`.
