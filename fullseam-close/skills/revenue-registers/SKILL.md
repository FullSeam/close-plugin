---
name: revenue-registers
description: Set up or keep a company's revenue in FullSeam — its customers, contracts, obligations, billing lines and payments, and the recognised revenue FullSeam works out from them. Use when asked to load billing, payment or contract data into FullSeam, to set up revenue recognition, to bring the revenue registers up to date, or to say where a revenue figure came from.
---

# Revenue: the registers, and what they add up to

FullSeam holds five **registers** — plain tables of the company's own records —
and works out recognised revenue by month from the first four. The fifth,
payments, says what has been collected and what is still owed. From the
registers on is FullSeam's: the pages people look at, and the arithmetic.

How the registers are filled depends on where the data is:

- **Stripe, where the company has connected it, FullSeam reads itself.** It
  fills customers, obligations, billing lines and payments from the account,
  the same way at every pull, by the company's **rules**. Your work there is
  the rules, and the rows the rules get wrong. See "Stripe" below, and read
  `revenue_stripe` before anything else.
- **Everything else is yours to bring**: contracts, revenue that never passes
  through Stripe, a billing system FullSeam does not read. You write those
  rows, usually with a script.

Either way there are two halves to keep apart:

- **The company's policy is yours to apply.** Which lines are a subscription
  and which are usage, which customer is in which segment, which invoice is
  treated by hand: FullSeam decides none of it. It is in the rules you write
  and the rows you write.
- **The arithmetic is FullSeam's.** You never send a revenue figure. You send
  rows, and FullSeam says what they earn in each month.

## How it fits together

Three layers, each built on the one before. Keep to this order: it is where a
first conversation goes wrong.

1. **The schedule is FullSeam's.** The registers hold the rows; FullSeam works
   out, month by month, what was recognised, invoiced and collected, and what
   is unbilled, deferred and owed at each month's end. That is the Revenue
   page, `revenue_recognised`, `revenue_collected`, `revenue_receivables`, and
   `revenue_workbook` for the same figures as a file. **Never build your own
   schedule in a sheet.** Where the page is wrong — a line not placed, a
   period missing, a contract not loaded — fix the rows or the rules until the
   page is right. A schedule worked out beside it differs from it (by how a
   month is split, at least), and the person is left with two sets of figures.
2. **The entry comes from the schedule, once a month, through a close task.**
   FullSeam does not post to the books. The month's revenue entry is the work
   of a standing task — "Revenue recognition", ending in a journal entry —
   whose instructions are below under "The monthly entry". Where the company
   has no such task, offer to set it up (`close-configure`) before drafting
   any entry: work done outside the close has no review, no reminder and no
   record of what was booked.
3. **The ledger is the check.** With the ledger accounts said for recognised,
   deferred and unbilled (see "Deferred, unbilled, and the ledger"), the page
   shows each beside what the books hold. Once the entries are posted, a
   difference is an entry missed or wrong.

**The first time, with a company:**

1. `revenue_stripe`: what was read, and what the rules place. Where nothing is
   connected, the registers are yours to fill (see "Doing a setup from data
   FullSeam does not read").
2. What the company has written down about revenue (`company_get`), and its
   contracts in the file store (`close_store_find`).
3. The rules: write them, `try` them, keep them when the person agrees (see
   "Stripe").
4. What no rule can say, row by row: service dates a line was billed without,
   contracts loaded and linked, an obligation's term and yearly price from its
   contract, revenue never billed through Stripe.
5. What only the person knows, asked in one list: a customer billed with no
   contract, a contract never billed, whether a customer left.
6. Months already in the books: compare the page with the ledger, and say what
   one catch-up entry would book.
7. The monthly task, then the ledger accounts.

**Changes need a chat that may make them.** Rules, rows and the task are all
changes. Where you may not make them, say what you would change and why, and
ask for what would let you — in a chat, usually Read and write under
Permissions — then wait.
Do not do the work some other way meanwhile: no workbook standing in for the
schedule, no entry drafted from figures FullSeam has not worked out.

## The monthly entry

The instructions a "Revenue recognition" task carries. Each month, for the
month being closed:

1. **Bring the schedule up to date.** `revenue_stripe` for when Stripe was last
   read and what is not placed; read it now (`revenue_stripe_pull`) if the
   month's last invoices are not in. Every line placed, every held value that
   differs from Stripe looked at (`revenue_stripe` with `against`), and every
   obligation past its term accounted for (see "Recurring revenue").
2. **Take the month's figures from it.** `revenue_workbook` for the month:
   revenue recognised, invoiced, collected, and unbilled and deferred at the
   month's end and the month before. Keep the workbook on the task as the
   entry's support.
3. **Draft the entry from those figures**, nothing else:

   | Account | Debit | Credit |
   |---|---|---|
   | Accounts receivable | invoiced | |
   | Unbilled revenue | its rise | its fall |
   | Deferred revenue | its fall | its rise |
   | Revenue | | recognised |

   It balances when invoiced, plus the rise in unbilled, plus the fall in
   deferred, is recognised. Cash collected is booked where the company books
   its receipts; say so if it is not booked anywhere. Where payments come
   through a processor (Stripe), what was collected goes to the processor's
   balance account against receivables, and the month's **fees** (Collected,
   `revenue_collected`) out of it to an expense: a cost, never less revenue.
   What is left in that balance account is the processor's payouts to the
   bank and its other movements, which are the bank reconciliation's. The accounts are the
   company's: where it has no account for unbilled revenue, say so, and do not
   invent one.
4. **Hand it over** as `work-journal-entry` says: the entry for review, its
   support beside it. Nothing is posted by you.
5. **After it is posted**, the page beside the ledger: recognised, deferred and
   unbilled each agree with the books, or the difference is said.

Lock the month on the Revenue page once its entry is posted, so a late change
lands in the next open month and not in one already booked (see "Locked
months").

Over an MCP connection with no skills, this text is `revenue_guide`. What
FullSeam holds besides revenue — the close, the books, the company's notes — is
`fullseam`; this skill is not for a close task or for figures from the ledger.

## The registers

`revenue_registers` gives each register's columns as this company has them.
Read it before writing anything: it is the authority, and this is the outline.

| Register | A row is | It must say | It may say |
|---|---|---|---|
| `customers` | somebody the company bills | `id`, `name` | `at_term_end` |
| `contracts` | an agreement with a customer | `id`, `customer`, `name` | `start`, `end`, `file` |
| `obligations` | one thing the company owes a customer and earns revenue by delivering | `id`, `customer`, `name`, `recognition` | `contract`, `start`, `end`, `amount`, `recurs`, `yearly_price`, `cancelled_on`, `renews`, `at_term_end` |
| `billing_lines` | one amount billed, or taken back | `id`, `customer`, `date`, `amount` | `obligation`, `due`, `document`, `description`, `start`, `end` |
| `payments` | money received against one invoice, or paid back | `id`, `customer`, `date`, `amount` | `document`, `reference`, `fee` |

**`id` is the row's id where it came from** — the billing system's id for a
customer or an invoice line, a contract's reference. It must be stable: the
same thing is sent under the same id every time, next month included. Where the
source has no id, make one from what does not change (`<customer id>:setup`),
never from a position in a file.

**How they link.** A row names another register's row by that row's `id`:

```
customer ◄── contract
   ▲  ▲         ▲
   │  └── obligation (may name the contract it was agreed in)
   │         ▲
   ├── billing line (names the obligation it bills)
   │         ▲
   └── payment · · · · ┘ (settles an invoice: the billing lines with its `document`)
```

A contract, an obligation, a billing line and a payment each name one customer,
and what they name must be that same customer's. A payment names no row but its
customer: it says its invoice as text (see Payments). A customer with no contract is normal.
So is an obligation with no contract.

**Conventions**, which a write is refused for breaking:

- Dates are `YYYY-MM-DD`. `start` and `end` are the first and the last day,
  both counted: a year is `2025-01-01` to `2025-12-31`. A source that gives a
  period as "start, and the moment the next one starts" needs a day taken off
  the end.
- `amount` is in the currency's own units with at most two decimals: `1188.00`.
  A source that counts in cents is divided by 100 first.
- An amount taken back — a credit note — is a billing line of its own with a
  negative `amount`, naming the same obligation. Money paid back is a payment
  with a negative `amount`, and by itself takes no billing back (see Payments).
- Only what the company counts as billed belongs in `billing_lines`. A draft,
  or an invoice that was voided, is not a row.

## Stripe

`revenue_stripe` says whether an account is connected, what was last read of
it, the rules in force, and for each test how many lines it places and what
they come to. Where `connected` is false and nothing has been read, this
section does not apply: fill the registers yourself.

**What FullSeam does without being told**, for every company:

- An invoice is billing unless its status is one the rules leave out. Its
  date is the day it was finalised, its `document` its number. A line that
  bills nothing is not a row. A line's amount is less its discount.
- A line's days are Stripe's period with the day taken off the end.
- A credit note is negative billing on the obligation of the line it credits,
  over that line's days.
- A payment settles the invoice Stripe says it paid. A refund is a negative
  payment.
- A customer is a row once it has been billed an amount.
- Rows keep Stripe's ids: `cus_…`, the line's `il_…`. An obligation is one
  kind of revenue on one subscription, `sub_…:saas`, or one kind for one
  customer, `cus_…:usage`.
- **Stripe's subscriptions are not read**, only what was billed on them. A
  recurring obligation's `yearly_price` is what it was last billed for a full
  period, brought to a year. Nothing says a subscription was cancelled: it
  shows as a term that ends with nothing billed after it, which "Each month,
  account for what is past term" below deals with. Where the company knows
  sooner, or the price to come is not the price last billed, write
  `cancelled_on` or `yearly_price` on the obligation: it is held like any
  other column you set.

**What the rules say**, and only the rules (`rules_shape` in `revenue_stripe`
gives every part):

- `kinds`: the kinds of revenue the company has, how each is earned
  (`over_time`, each line over its days; `point_in_time`, each line on its
  date), and whether its lines gather into one obligation to each Stripe
  subscription the kind is billed on or one to a customer. A `recurring` kind
  gets a `yearly_price`.
- `tests`: an ordered list. A line is the kind of the first test it fits, by
  its product, its description, whether Stripe billed it on a subscription
  (the part-period lines of an upgrade included; a fee someone attached to
  the subscription not), or a column of its customer. Each line says the test that placed it, in its
  `placed_by` column.
- `columns`: columns of the company's own that follow from Stripe — a
  customer's segment from its name or what it buys, an obligation's price
  band from its yearly price.
- `not_billed`: the invoice statuses that are not billing.

A line no test fits is **not placed**: loaded, naming no obligation, in no
figure, and listed. That is the queue. Do not add a last test that catches
everything to make it empty.

**Changing the rules.** Send the whole rules to `revenue_rules_put`. Always
with `try` first: the answer is what they would make, change and remove in
each register, which months' recognised revenue would move and by how much,
and what would be left not placed — and nothing is kept. Read it, tell the
person what will change, and only then send it again without `try`, with a
`note` saying what changed and why. Kept rules are applied at once to
everything read so far, and at every pull after. A month that is locked does
not move: what a rule would have done to it lands in the first open month.

**A rule is general. One invoice is not a rule.** "Lines for this product are
implementation" is a rule. "This invoice was a plan billed by hand for seven
months" is a fact about a row: write it on the row with `revenue_rows_put` —
the line's `obligation`, its `start` and `end` — creating the obligation first
if none fits, under an id of your own. Do not write a test that names one
invoice or one customer to get the same effect.

**What you change on a row, the loader leaves.** A column you or a person
changes is held: every pull after writes the row's other columns as Stripe has
them and leaves that one. A value sent as it already stood changes nothing and
holds nothing, so only send the columns you mean to set. `revenue_rows` gives
each row's `held` columns, and with `held` only the rows that have any. A
run's answer counts, as `kept`, the rows where a held column now differs from
what Stripe and the rules would write. `revenue_stripe` with `against` lists
them, each with the value held and the value it would otherwise be: read
those when the number moves, since one of the two has changed.
`revenue_rows_release` hands columns back to the loader, for a correction
that is no longer wanted. A held row Stripe stops giving, an invoice voided,
goes; if Stripe gives it again it comes back as it was held.

So with Stripe connected you never send Stripe's rows yourself. Sending a
column on a row the loader made does not update Stripe's data: it holds that
column against it.

**Whether Stripe fills revenue is the company's to say.** `fills` in
`revenue_stripe` says whether it does. Off, an account may be connected and
nothing is read from it; a pull is refused: a person turns it on in Revenue settings,
under Stripe. You cannot.

**A pull.** `revenue_stripe_pull` reads the connected accounts now: what was
made since the last reading, and what Stripe says changed. The reading runs
on its own. A small one is over while the tool waits, and its answer says
what was read, what each register gained, lost and had changed, and what is
not placed. A large one, the first above all, answers that it is going: call
the tool again with the `pull` it gave to hear how it went, and do other work
meanwhile. One pull runs at a time; a second is refused until the first is
done. In `revenue_stripe`, `last_pull` says when the accounts were last read,
`going` that a pull is under way, and `failed` what the last one said if it
did not finish. An account Stripe no longer answers for shows under
`accounts` as revoked: tell the person to connect it again in Settings. Do
not assume the account is read without being asked.

**What Stripe's figures are here.** A billing line's amount is what was
billed for the thing sold: less discounts, and less the tax inside a
tax-inclusive price. A day is the day in the account's own time zone. An
invoice marked paid outside Stripe is settled by a payment the loader makes
for it. Tax billed with a line is its `tax`, beside the amount: no revenue,
and in what the customer owes and pays. A credit note is billing taken back
on the invoice it credits, with its own number in `credit_note`. A
customer's credit in Stripe, put there by a credit note and used on a later
invoice, shows as two payments that are no money: one taking it off the
first invoice, one settling the second. The registers are kept in one currency, `currency` in
`revenue_stripe`; billing in another is left out and counted under
`other_currencies`. Where there is any, ask the person which currency the
books are in and say it in the rules' `currency`.

**Setting it up the first time**:

1. Read `revenue_stripe`. With the rules every company starts from, only what
   Stripe bills on a subscription is placed.
2. Read what the company wrote down about how it treats its revenue. Read the
   lines not placed (`revenue_rows` on `billing_lines` with `unlinked`), and
   group them in your head by product and description.
3. Write rules in the company's words — its kinds, its segments — and `try`
   them. Look at each test's lines and amount: a test that places nothing is
   wrong or not needed, and one that places far more than the company would
   expect is too wide. Tests are read top down, so the narrow ones go first.
4. Say what the rules are and what they leave unplaced, and keep them when
   the person agrees.
5. Then the rows the rules cannot say: by hand, as above. Then contracts and
   what never passes through Stripe, as the rest of this guide says.

**Each month, before the month is locked**: `revenue_stripe` for what is not
placed and what was last read; the runs under Changes for what arrived; the
`kept` rows; and "Each month, account for what is past term" under Recurring
revenue. A product the rules have never seen shows up as lines not placed:
that is a rule to add. One odd invoice is a row to set.

## An obligation

An obligation is the unit revenue is earned by. Choose its grain the way the
company would talk about it: "their platform subscription", "their setup fee",
"their usage fees". A customer who bought things that are earned differently,
or reported apart, has one obligation for each.

Keep it a short row. A price change, a renewal or a second year at another
price is another obligation with its own `id`, not more columns on the first.
A milestone is its own row. You never send months: FullSeam works them out.

`recognition` is how it is earned: `over_time`, evenly by day, or
`point_in_time`, all on one date. **Of what** depends on whether the obligation
says an `amount`:

| The obligation | Revenue comes from | Its billing lines are |
|---|---|---|
| says no `amount` | its billing lines: each line over the days that line covers (`over_time`; a line with no days of its own is spread over the obligation's `start` to `end`), or on the line's `date` (`point_in_time`) | the source of the revenue |
| says an `amount` | that amount: evenly over the obligation's `start` to `end` (`over_time`), or all on its `start` (`point_in_time`) | what was billed against it |

- **No `amount`** is the default, and right where what is invoiced is what is
  earned: usage, a fee billed once, a subscription whose invoices each cover
  their own period. A subscription billed twelve times is then one obligation
  with twelve billing lines.
- **An `amount`** is for where the agreement, not the invoice, says what is
  earned and when: a year billed in instalments, a fee billed before or after
  the work, a contract whose term differs from its invoices. It is the total
  for `start` to `end`, and both dates are then required. A milestone is a
  `point_in_time` obligation with its own `amount`, its `start` and `end` both
  the day it is met.
- `yearly_price` is what it is priced at for a year, and is what makes an
  obligation recurring revenue. Say it, do not leave it to be worked out: a
  first year at a discount, or a term of eighteen months, is still priced by
  the year. Use the price the company counts as recurring — it will tell you
  whether that is list or what was charged.
- `recurs` is only for saying no: an obligation with a yearly price that is
  not recurring revenue.
- `cancelled_on`, `renews` and `at_term_end` are about recurring revenue and
  nothing else. None of them changes how revenue is spread: `end` does that.

How what is earned over time is spread is the company's setting, not yours.
One of three, and every answer says which as `spreading`:

- By month: every full month earns the same and a part month its share by day.
- By day: every day earns the same.
- By month, whole from the first month: the month a term starts in counts whole, and the months are
  equal. A year from 15 November is twelve equal months, November to October,
  and nothing in the November after.

Do not correct for it in the rows.

There is no other rule to configure. Applying the company's policy means
choosing which of the two an obligation is, and linking each line to the right
one.

**A billing line that names no obligation is not placed.** It is in no figure —
not recognised, not deferred — and shows on Revenue as not placed until it is
linked. Leave a line unlinked when you do not know what it is, and say so; do
not park it on an obligation to make the number go away.

Where the company treats a particular invoice differently from its own rules,
that is still a link: its lines name an obligation that is earned the way they
say.

## Recurring revenue

`revenue_arr` is annual recurring revenue at each month's end, customer by
customer: the `yearly_price` of every obligation that says one, counted on the
month's last day from its `start`. No `end` is still running. It also gives
how each month's total got there from the month before — new, expansion,
contraction, churn — and with an `earlier` and a `later` month, the customers
whose figure changed between them.

**What happens when a term runs out is the company's choice**, `term_end` in
every answer, set in its Revenue settings and not by you:

- `stops`: an obligation counts to its `end`. A term that runs out with no
  renewal loaded is churn the month after.
- `keeps_counting`: an obligation counts until it says `cancelled_on`. From
  the day after its `end` it is past term: still counted, and listed in
  `past_term_obligations`, with `past_term` the amount of each month's total
  that is.

One customer or one obligation can say otherwise with `at_term_end`. The
obligation's own wins, then its customer's, then the company's. Use it where
the company tells you a customer is different — a pilot that ends, a customer
on auto-renewal — not to make a total come out.

Under either choice:

- **`cancelled_on` is the last day it counts.** Write it when the customer
  has cancelled, with the day the company treats them as gone. Leave `end`
  as the term's: revenue is still spread over it.
- **A renewal or a price change is another obligation that says which one it
  `renews`.** The one it names stops counting the day the new one starts, so
  a renewal at a new price is expansion or contraction by the difference.
  Without `renews`, where the company keeps counting, both count: the old
  one is past term with the new one named in `later_obligations`. FullSeam
  never takes two obligations of one customer for a renewal by their dates.
- **Each month, account for what is past term.** For every obligation in
  `past_term_obligations`, find out which it is and write it: the renewal,
  naming it in `renews`; or `cancelled_on`; or, if it is running on with
  nothing signed, leave it and say so. Then tell the controller in your own
  words which renewed, which ended, and which you could not tell. An
  obligation should not sit past term for months with nobody having said why.
- `not_counted` lists what cannot be counted: it says it `recurs` and says no
  `yearly_price`, or it has a yearly price and no `start`. Each is one to fix.

## Locked months

The company locks a month when it has closed it. A locked month's figures do
not change, whatever is written afterwards. A row that would have moved a
locked month is still written as it is; what it would have done lands in the
first open month, and says so: the answer's `from_locked_months` is that
amount for each month, and a row behind a figure names the month it
`belongs_to`. `locked_months` lists them.

So a late invoice dated in a locked month is loaded with its real date. Do not
re-date it to make it land somewhere; FullSeam does that, and keeps the trail.
An obligation with revenue in a locked month cannot be removed: end it with
its `end` date.

## Deferred, unbilled, and the ledger

For every obligation FullSeam sets what was billed against what was recognised,
to the end of each month. More billed than recognised is **deferred**; more
recognised than billed is **unbilled**. An obligation with no `amount` can only
ever be deferred, since it earns nothing it was not billed.

The company says which ledger account each figure belongs to in columns of its
own on obligations: `revenue_column_add` with `kind` `account` and the `figure`
it is the account of — `recognised`, `deferred` or `unbilled`, one column to
each. Fill them with the company's account numbers. A number must be one in
the chart of accounts of the entity whose books the registers belong to
(`books_accounts` lists them); one that is not refuses the write, saying which
row.

The registers' figures then sit beside the ledger's. `revenue_recognised`
answers with a `ledger` for each of the three: what is posted on the accounts
named, and the `difference`, the registers' figure less the ledger's.
Recognised is set against the month's movement on its accounts; deferred and
unbilled against the posted balance at the month's end. `revenue_accounts`
gives the same account by account, and with a `month` and a `figure` lists the
obligations one balance is the sum of. A month with nothing posted has no
ledger figure and no difference.

A difference is not a mistake to be made to go away. An account can hold
revenue that never passes through billing, and the ledger can be right where
the registers are behind. Say each difference and what you think it is; do not
net it, and do not add rows to close it.

`open_months` in those answers are the months whose close is open: the months
the work is about.

## Payments

A payment is money received, or paid back. It changes nothing of what is
recognised, deferred or unbilled: those come from billing lines and obligations
alone. Payments answer two other questions — what is still owed, and what was
collected.

**There is no invoice row.** An invoice is the billing lines that share a
customer and a `document`. A payment settles it by naming the same customer
and writing the same `document`, spelt exactly as the lines spell it. So fill
`document` on billing lines with the invoice's number, and on a credit note's
lines with the credit note's. Fill `due` on them too, where the source says
when the invoice is to be paid by, the same date on every line of one invoice:
receivables are aged from it. An invoice that says none is taken as due the
day it was billed.

- **One invoice to a row.** A payment that covers three invoices is three rows,
  each with the amount applied to that invoice, each with an `id` of its own
  (`<payment id>:<invoice>`), all carrying the payment's own number as
  `reference`. Most billing systems hold exactly this: a payment, and how it
  was applied.
- **A part payment** is a row with the amount paid. The invoice stays open for
  the rest, and a later payment is another row naming the same `document`.
- **A refund** is a row with a negative `amount`, dated the day the money went
  back. It names the credit note it pays out where there is one, or else the
  invoice it was paid on. It reduces what was collected. It takes revenue back
  only if the company also took the billing back, which is a negative billing
  line as before: write that line when a credit note exists or the company
  says the sale is reversed, and not otherwise.
- **A payment with no invoice** — a deposit, money on account, a payment the
  source cannot tie to anything — is written with `document` left out. Do not
  guess an invoice for it. It is shown as matching no invoice, and counted
  beside the figures, in none of them.
- **Only money that moved.** A failed or pending charge is not a row. A credit
  note set against an invoice with no money moving is not a payment either:
  the invoice then shows open and the credit note shows as a negative
  receivable, and for the customer the two net.

A payment is matched when it is read, not when it is written, so one loaded
before its invoice's lines is unmatched only until they arrive.

What comes of it:

- `revenue_receivables`: at a date, billed less paid for each customer, with
  what is open by how long it is past due. With `customer`, that customer's
  open invoices, each with its due date.
- `revenue_collected`: for each month, what was invoiced and what was
  collected, grouped as `revenue_recognised` groups. A payment is shared over
  its invoice's lines in proportion to their amounts, which is how it reaches
  an obligation. `cash_basis` is what was collected on lines that name an
  obligation, beside `recognised` for the same months; what was collected on
  lines not placed, and on no invoice, is counted apart.
- `revenue_rows` on `payments` with `unmatched` lists the payments that settle
  no invoice. One there because its `document` is spelt differently from the
  lines' is a mistake to fix in the script.

## Contracts

Billing says what was charged. A contract says what was agreed: the term, how
it renews, what the fee includes, a fee that is not billed yet. Reading the
contracts adds to the registers what only they know, and ties each one to the
customer, the obligations and the billing it governs.

**A contract is a row and a document.**

- The row: `revenue_rows_put` to `contracts`, with the `customer` it is with,
  a `name` a person would know it by, and the term as `start` and `end`. Give
  it an `id` that will be the same next time — the signing system's id, or the
  document's own path — never a counter.
- The document, one of two ways. Where it already lives somewhere people can
  open it, write that link as the row's `file`. Otherwise post its bytes to
  the `file_upload_url` that `revenue_registers` gives for contracts, with the
  row's id beside it, after the row exists:

  ```
  curl -sS -X POST "<file_upload_url>" -F "contract=<the contract's id>" \
    -F "file=@order-form.pdf" -F "session_id=<session_id>"
  ```

  The contract then names the file kept. An amendment or a renewal is a
  contract of its own, with its own document.

**Reading one.** A document you hold yourself, read yourself. One held in
FullSeam comes back on its contract's row from `revenue_rows` with a `file_id`:
`close_file_read` reads it, pages and all. A link is the company's to open.
Read the document, not its file name: a folder of contracts holds drafts and
unsigned copies, and only what was signed is a contract. Where you cannot tell,
load it and say so in a column of the company's own, or leave it out and list
it.

**Whose it is.** The name on a contract is often not the name the customer is
billed under. Match on evidence — an address, a signatory's email domain, the
product and the price, the dates — and where the evidence is thin, ask or
leave it and list it. A contract on the wrong customer is worse than one not
loaded. A contract for somebody never billed is a new customer row.

**What it adds.** Put each fact where it belongs, and nowhere twice:

- The term goes on the contract. Where the company takes its dates from the
  contract, it also goes on the obligations that contract governs, as their
  `start` and `end`.
- Link what it governs: set `contract` on each of that customer's obligations
  the document covers. Their billing lines come with them. An obligation the
  contract does not mention stays as it is.
- What the contract promises that billing has not shown yet — a later year at
  another price, a fee due on a milestone — is a new obligation naming the
  contract, with an `amount` where the contract and not the invoice says what
  is earned.
- Anything else the company wants to see — how it renews, what the fee
  includes, a notice period — is a column of the company's own on contracts,
  in its own words.

Send only the columns you are adding: a row sent again keeps what it had.

**Where the contract and the billing disagree**, the company says which is
right for what; do not choose for it. Many take the price from what was
charged and the dates from the contract. Write the one that is right, and put
the other where it can be seen — the contract's price in a column of its own
beside what was billed. Then say each disagreement you found.

**Check both directions** before you are done: `revenue_rows` on `contracts`
with `no_billing` is the contracts nothing was billed against, and on
`billing_lines` or `obligations` with `no_contract` is what was billed under no
contract. Neither is wrong in itself — a company bills small customers with no
contract, and signs contracts before it bills — but each row there should be
one you can explain.

Every row says who first wrote it and who last changed it (`created`,
`changed`), so an obligation that came from billing and was then filled in from
a contract shows both.

## Columns of the company's own

Whatever else the company reports by — a kind of revenue, a segment, a product,
a price band — is a column of its own, added with `revenue_column_add` and then
written like any other. Revenue can be grouped by every one of them, so add a
column for each way the company says it looks at revenue, on the register it is
a fact about: a segment is a fact about a customer, a kind of revenue about an
obligation, a product about a billing line.

Use the company's own words for the label and for the values. A person reads
these tables as their records.

A column that is not one of the register's, or one added, refuses the write. That
is how a misspelt column is caught.

## Writing rows

`revenue_rows_put` writes up to 500 rows to one register. More go as a file:
each register in `revenue_registers` has an `upload_url`, a whole address that
takes no header. POST the file there as `file`:

```
curl -sS -X POST "<upload_url>" -F "file=@billing_lines.csv" -F "session_id=<session_id>"
```

Where you cannot post to an address, write the rows in calls of 500.

The file is CSV under a header row of column names, or JSON: a list of rows, or
one row to a line. The address is good for an hour; `revenue_registers` gives
another.

What to know about a write:

- **All or nothing.** Every row is read before any is saved. If one is wrong,
  none is written, and the refusal says which row, which column and why. Fix
  the script, not the row by hand, and send it again.
- **Sending a row again updates it.** Columns you send are set; columns you
  leave out keep what they had; a column sent empty is cleared. So a second run
  over the same data changes nothing, and the answer says so: `unchanged`.
- **In order.** A row can only name a row that is already there: customers,
  then contracts, then obligations, then billing lines.
- `remove` takes rows out by id, for a row that should never have been sent. A
  row that other rows name stays until they are re-pointed or removed.
- No `upload_url`, or no `revenue_rows_put` in your list, means this
  connection only reads: say so, and hand the person the files to load.

Every write is recorded with who made it. Where your tools include
`close_session_update`, call it once at the start with what you are doing and
pass the `session_id` it gives on every write, so the rows say which run put
them there. End the run with it when you are done.

## Doing a setup from data FullSeam does not read

For Stripe, where it is connected, see "Stripe": there is no script. This is
for everything else.

1. **Read the guide's sources.** `revenue_registers`, whatever the company
   wrote down about how it treats its revenue, and the data. Look at the data
   before deciding anything: count it, and read real rows of each kind.
2. **Decide the shapes with the company's words**, and say what you decided
   before loading: what an obligation is here, which columns of their own you
   will add, what you will leave out and why.
3. **Write a script** that reads the source and writes one file per register.
   Keep the company's rules in it where a person can read them. The script is
   the company's: hand it over at the end, because it is what runs again when
   new billing arrives.
4. **Load in order**, fix what is refused, and load again.
5. **Check what came out** (below) before you say it is done.

Where a rule does not cover a row, or two rules disagree, ask. Do not invent a
treatment. A row you could not place is better unlinked and listed than
guessed.

## Running it again

For what FullSeam reads from Stripe there is nothing to run: it pulls. For what
you brought yourself — new contracts signed and amended, billing from
elsewhere — the registers are brought up to date by running the same setup
over the newer data. Nothing here
is a different way of writing: a row sent again under its `id` is updated, so
the whole of the newer data can be sent and only what differs changes.

1. **Start a run and name it on every write**, as before — every file posted
   too, a contract's document included. The run is then one entry under
   Changes, and each answer's `run_url` opens what it has made, changed and
   removed so far, row by row with each column's value before and after. That
   page is what the person reads to see what changed since last time; point
   them at it rather than retelling it.
2. **See what is there first.** Each register in `revenue_registers` has a
   `download_url`: the whole register as one CSV, under the header a file of
   rows is posted under. Fetch it the way a file of rows is posted, with a GET.
   Compare it with what your script is about to send before sending anything.
   Do not page through `revenue_rows` for this.
3. **Do not undo what somebody else wrote.** A register can have more than one
   writer: the Stripe loader, a script for other billing, a reading of
   contracts, a person's correction. The loader keeps out of the way of the
   others by itself (see "What you set on a row, the loader leaves"); a script
   has to be told.
   A row keeps every column you do not send, so a script sends its own columns
   and no others. Where two of them write the same column — a contract's dates
   over billing's on an obligation, a billing line moved to another obligation
   — settle which one wins once, in the script, before either is sent; a
   column that flips at every run is the sign they were not.
4. **Take out what is gone.** A row your source no longer has — an invoice
   voided since, a customer merged — is still in the register until `remove`
   takes it out. The download, less what you are about to send, is the list of
   candidates; remove only the ones that are yours and really gone.
5. **An amendment or a renewal is a contract of its own**, with its own
   document and term, naming the same customer. What it changes — a later
   year, another price — is another obligation naming it. What it replaces
   keeps its rows; end the old obligation on the day the new one starts.

A second run over the same data answers `unchanged` for every row. If it does
not, something in the script is not stable — usually an `id`.

## Checking, and saying where a figure came from

`revenue_recognised` is recognised revenue by month, worked out from the
registers at that moment. `by` groups it: `recognition`, `customer`,
`obligation`, or a column of the company's own as `register.column`
(`obligations.kind`, `customers.segment`).

- With `month`, and a `value` of the grouping, it answers with the billing
  lines that one figure is the sum of, each with how much of it falls in that
  month. This is how a figure is explained: never from memory of what you
  loaded.
- Each row behind a figure says its `source`: a `billing_line`, or an
  `obligation` earned from its own amount. `by` `source` splits a month the
  same way.
- `not_placed` in every answer is what was billed in those months on lines
  that name no obligation. `not_placed: true` lists them.
- `deferred` and `unbilled` in every answer are the balances at each month's
  end.

`revenue_workbook` is the same calculation as an Excel file for a range of
months, to hand to an accountant: a summary by month with deferred from its
opening balance through additions and releases to its closing one, a row to
each obligation and month, the billing lines behind them, and the lines not
placed. Each sheet sums the next by formula, so it can be re-added without
FullSeam. It answers with a `download_url`, fetched with a GET, and each
month's figures as the file gives them; they equal `revenue_recognised` for
the same months.

Before you call a setup done:

- **Billing ties to the source.** The rows and the total amount in
  `billing_lines` equal what the source says was billed, less what you left out
  on purpose. Say the difference and what it is.
- **Payments tie to the source.** The rows and the total amount in `payments`
  equal what the source says was received less what was paid back. Every
  payment under `unmatched` is one the source ties to no invoice.
- **Nothing is left unplaced by accident.** Every line in `not_placed` is one
  you chose to leave.
- **Deferred and unbilled make sense.** An unbilled balance that only grows is
  usually an obligation with an `amount` whose billing lines name another.
- **The months read like a business.** Group by the company's kinds and look
  along each row. A month several times its neighbours, or a row that stops, is
  usually a period read wrongly or lines on the wrong obligation: open the
  month and read its largest lines.
- **The rows read like their records.** `revenue_rows` on a few customers you
  have also read in the source.

Then tell the person what is in each register, what you left out, what you
could not place, and what you had to assume — with the `web_url` from the
answers, which opens the same thing on screen.
