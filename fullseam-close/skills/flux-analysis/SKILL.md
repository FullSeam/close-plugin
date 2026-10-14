---
name: flux-analysis
description: Explain why accounts moved — the flux, or variance analysis, of a month against the month, quarter, year or three-month average before it. Use when asked why an account or a line is up or down, what is behind a variance, to explain or draft the flux for a month, or to answer a question somebody left on one.
---

# Flux: why an account moved

FullSeam holds the flux: every account's figure for the month beside what it is
compared with, whether the movement is large enough to want an explanation, and
what has been said about it. You find the cause in the ledger and write it down
where the reviewer reads it. What the system holds generally is `fullseam`; work
that belongs to a checklist task is `close-task-work`.

## Read the flux first

`flux_get` with the `month`. It answers, before any row:

- `columns` — what `current` and `prior` are on each statement, and which
  `comparison` the month is there: the firm sets, per statement, whether a
  month is compared against last month or the 3-month average, and `month`
  means whichever it set. On the income statement the figures are activity for
  a period; on the balance sheet, balances.
- `thresholds` — the bar a movement must reach to be `flagged`. Where there is
  none, nothing is flagged and every movement nobody has explained is open:
  work down from the largest, and stop where the amounts stop mattering to this
  firm. Last month's explanations show where that was.
- `counts` — how many are `unexplained`, a `draft`, `changed` or `explained`.

The `comparison` is the month unless the person asked for the quarter or the
year: the firm has already said what its month is compared against. `consolidated` reads the ledger's
own consolidation into the entity, which is what a group reviews; the accounts
are then the chart's, and the explanations are kept apart from the entity's own.

Each row's `status`:

| `status` | what it means | what you do |
|---|---|---|
| `unexplained` | nobody has said why | find the cause and draft it |
| `draft` | a chat wrote it and no person has accepted it | improve it if you can; it is yours to rewrite |
| `changed` | explained, and the figures have moved since | see what moved; comment with what you find |
| `explained` | a person wrote it or accepted it | leave it. Comment only if the ledger contradicts it |

`flux_get` with an `account` gives that one account and its whole `thread`.

## Find the cause in the ledger

Never explain a movement from the account's name. The cause is in what was
posted, and it is usually one of: something new, something that stopped,
something that moved between months, or a correction.

1. **What the movement is made of.** `books_read` with `read` `activity`, the
   `accounts`, and `from` / `to` covering the month and every month it is
   compared with. `by` `type` first: it shows whether the difference is in
   bills, journals, payroll or invoices. Then `by` `name` for who, and
   `department` or `class` where the ledger keeps them. `company_get` lists
   what this ledger can add up by.
2. **Check it ties.** Each account's `posted` is the ledger's total for the
   month. On the income statement it should equal the month's activity on the
   flux. If it does not, the trial balance here is older than the ledger: say
   so in a comment and do not explain a figure that is about to move.
3. **Name the transactions.** `by` `transaction` lists the largest items in
   the account with what opens each; `books_entries` lists every line. `books_read`
   with `read` `transaction` shows one in full — the other side of the entry
   says what it was.

Pass `consolidated` to these reads when the flux is consolidated, so the lines
are every book's. On the income statement they tie to the consolidated figure;
on the balance sheet they miss it by what the exchange rate itself moved.

An average hides its cause in one of its months as often as in this one. The
row's `months` give each month of the average; a prior month that is the odd
one out is the explanation ("June held the annual renewal"), not this month.

## Write it down

`flux_explain` with the `month`, the `comparison`, the `accounts` and the
`text`. Several accounts with one cause take one call and share the
explanation.

- **Say the cause, not the arithmetic.** The reader has the figures beside the
  text. "A-lign audit fees, billed in August" is an explanation. "Increased by
  21,000 over the prior average" is the row read back.
- **Name what a reviewer can find**: the vendor, the customer, the entry
  number, the event. Give an amount where one item does not account for the
  whole movement.
- **Up and down as the page shows them.** Figures here carry the ledger's
  sign. On a row marked `shown_flipped` the page shows them turned over —
  income positive — so revenue that rose is "up", whatever the ledger's sign.
- **Write as the firm writes.** Read last month's explanations for the same
  accounts before drafting. Most are a line. Match their length and words.
- **Attach what it rests on.** Pass `references`: each is a read of the ledger
  the reviewer can open from the explanation, on FullSeam, without asking you.
  The entry you named is `kind` `transaction` with its `type` and `id` — the
  ledger's own id, as `books_entries` gives it, not the entry's number. The
  breakdown that showed the cause is `kind` `activity` with the `accounts`,
  `from`, `to` and `by` you read it with. The lines themselves are `kind`
  `entries`, up to five accounts. `from` and `to` are months, YYYY-MM, at most
  24 apart. Give each a short `label` — the entry's number, "by vendor, May
  to Aug". Attach the few that carry the explanation, not every read you made;
  with them attached the text can be shorter.
- **The same cause as last month is still worth saying**, in the same words,
  if it is still the cause. Check that it is.
- What you write is a draft: it shows on the flux as a chat's and waits for a
  person to accept it. It is refused where a person has already explained the
  account; say what you found with `flux_comment` instead.

**Where you did not find the cause, do not write an explanation.** Leave a
comment with `flux_comment` saying what you looked at and what would settle it
— "No single item: 44 payroll journal lines, each within 2% of July's" — with
the `references` that show it. A question the firm has to answer is a comment
too. A guess written as an
explanation is worse than a blank row, because the row stops asking.

## A question somebody asked

Asked why one account moved, or what is in it, answer from the reads above and
say where each figure came from. That alone writes nothing. If they want it on
the record, or the question was left in the row's `thread`, put the answer
there with `flux_comment`.

## The run

`close_session_update` once before the first write, with an `intent`; pass the
`session_id` it returns to every `flux_explain` and `flux_comment`, so what you
wrote is tied to the run that wrote it.

What this firm considers worth explaining, which comparison it reviews, and
causes that recur every year belong in the company notes: `company_get` with
`path` `policies/flux` before you start, and `company_put` when a person tells
you something that will be true next month too.

## At the end

Say how many you drafted, how many you left a comment on, and how many you did
not reach, with the `web_url` of the flux. Name any account where `posted` did
not tie.
