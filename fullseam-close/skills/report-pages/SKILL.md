---
name: report-pages
description: Build or change a report page in FullSeam — figures, charts and tables over the books and the files, kept as a page people open and filter. Use when asked for a report, a dashboard, a breakdown, a forecast or a budget page, or to change one there already. Read before calling report_put.
---

# Report pages

A report is a page in FullSeam that reads its rows again each time it is
opened. You do not write the page or its figures. You write a **definition**:
where each set of rows comes from, what the reader can set, and what to draw.
`report_put` keeps it and answers with the page's address; `report_get` lists
the pages there are, and with an `id` gives one's definition to change and put
back. What the system holds generally is `fullseam`.

`report_put` reads every dataset before it keeps anything, and answers with
each one's columns and first rows. It refuses, naming the columns there are,
a block that asks for one its dataset does not hold. So write the datasets,
put the page, read what came back, and fix what was refused.

A page is its person's own. Sharing it is theirs to do, on the page.

A change to a page there already is, is kept as a draft. Its owner sees the
draft when they open the page, and saves it there as a version; until then
nobody the page is shared with sees it.
`report_put` says `draft` when that happened: say so to the person. A new
page is saved as its first version. Every saved version is kept, and its
owner can go back to one.

Somebody else's page is not yours to change. Leave out `id` to keep a copy
of your own, or the person can make one on the page.

## The definition

```
{period, datasets, runs?, parameters?, filters?, blocks}
```

### `period`

The months `balances` and `entries` are read over. The reader can change them
on the page.

- `{preset}`: `month`, `trailing_3`, `trailing_6`, `trailing_12` or
  `trailing_24`, counted back from the latest posted month.
- `{from, to}`, each `YYYY-MM`.

### `datasets`

`{name: dataset}`. Each is one table of rows.

| `source` | Takes | Rows |
|---|---|---|
| `balances` | `entity?`, `statement?`, `accounts?`, `search?` | The posted trial balances, a row for each account and month: entity, month, account, name, statement, category, type, balance, movement. Every entity the reader sees when none is named. |
| `entries` | `entity?`, `accounts` | The ledger's entry lines on those accounts, read live: entity, account, date, month, type, number, name, memo, split, amount, and whatever else the ledger tags a line with. |
| `file` | `file_id` or `store_file_id`, `sheet?`, `header_row?` | A sheet of a workbook, or a .csv. Its columns are named by its header row: the first row with more than one cell filled, unless `header_row` says which. |
| `run` | `run`, `sheet` | One sheet of the workbook a run of this page makes. See *Runs*. |

On any dataset:

- `negate: true`, on `balances` and `entries`, turns the figures over. The
  books are debits positive, so income accounts want it.
- `where: {column: [values]}` keeps only those rows.

Columns are shown by their names with `_` as a space, so name a sheet's
columns as a person would read them.

### `filters`

`[{column, label?}]`. A pick-list on the page for that column, applied to
every dataset that has it.

### `blocks`

What is drawn, in page order. Three kinds are FullSeam's own, and are checked
against their dataset before the page is kept. Each takes `dataset`, and
`title?`, `width?` (`half` or `full`).

`agg`, wherever it is taken, is `sum`, `count`, `avg`, `min` or `max`; `sum`
when left out. `format` is how a figure is written: `money` (1,234.50),
`whole` (1,235), `compact` ($66.4K), `percent` (a fraction as 70.6%) or
`number`.

**A figure** — one number.

```
{kind: figure, dataset, value, agg?, format?}
```

**A chart.**

```
{kind: chart, dataset, type, x, y, series?, agg?, format?, line?, lineFormat?, colors?}
```

`type` is `bar`, `stacked_bar`, `line`, `area` or `combo`. `y` is added up for
each `x`, and for each `series` within it. Months, dates and numbers run in
order; anything else runs from the largest. With more than ten series, the
nine largest are drawn by name and the rest as one, Other. A `combo` is bars
with `line`, another column, drawn over them on an axis of its own: spend
with a headcount. That axis is a plain number unless `lineFormat` says
otherwise: `percent` for revenue with a margin. Only a combo takes `line`.

A chart is drawn in FullSeam's colours. `colors` says others, each as
`#rrggbb`: a list, taken by the series from the largest, or `{series: colour}`
by name, which holds when their sizes change places.

**A table of the rows as they are.** The reader sorts it by a heading and
pages through it.

```
{kind: table, dataset, columns?}
```

`columns` is the ones to show, each a name, or `{key, label?, format?}`. Only
a column of numbers takes a `format`.

**A table that adds up.**

```
{kind: table, dataset, rows: [columns], value, across?, agg?, format?, open?}
```

`value` is added up by `rows`. With more than one column in `rows`, each is a
level under the last, with a subtotal the reader opens and closes: category,
then account, then vendor. `across` makes a column for each of its values,
months usually, with a total. `open` is how many levels start open; all of
them when left out. A click on any figure lists the rows behind it. This is
the table for a statement, a breakdown, or spend by anything.

### `parameters`

Inputs on the page the reader sets: a growth rate, hires a quarter.

```
[{name, label?, type, default, min?, max?, options?, group?}]
```

`type` is `number`, `percent`, `integer`, `month`, `choice` (with `options`)
or `text`. A percent is a fraction: 0.05 for 5%. A `label` is two or three
words, with no unit or range in it. `group` is the heading related ones sit
under. A parameter does nothing by itself: a run reads it.

### Runs

For figures no dataset holds: a ratio, a forecast, rows sorted into kinds.

```
runs: {name: {script, inputs: {name: dataset}, workbook}}
```

`script` is Python. It is run once, top to bottom, alone on an empty machine
with no network. It finds:

```
in/<name>.csv     each input, as column names then rows
in/params.json    the parameters as they are set
in/period.json    {"from": "YYYY-MM", "to": "YYYY-MM"}
```

and must leave `out/<workbook>`, an .xlsx, which is recalculated and kept. A
dataset `{source: run, run, sheet}` reads one sheet of it: column names on one
row, a record on each row under it, no totals or blank rows.

- Every figure in the workbook comes from `in/`. Type none from the books into
  the script, or the page is wrong next month.
- Write what the script could not place — an account it has no kind for — to
  a sheet of its own, so the reader sees it.
- An input is `balances`, `entries` or `file`, not another run.

The run is made before the page is kept, and a script that fails comes back
with what it printed. It is made again, without you, when the reader sets a
parameter or the months, or presses Refresh.

Reach for a run when the arithmetic is the report. A table that adds up
already has subtotals; that alone is not a reason for a script.

## A view: a block you draw yourself

Where the three blocks cannot draw what is wanted — a layout, an interaction,
several things that move together — a block can be HTML of your own.

```
{kind: view, html, datasets?: [names], title?, width?, height?}
```

Nothing in a view is checked against its data, and nobody but you has looked
at what it draws. Use one only where a block will not do, and say to the
person that you did.

The HTML runs in a frame of its own that reaches nothing: no network, nothing
of the page around it, and no other address to go to, so a link out of it
leads nowhere and closes the view. It is handed `fullseam`:

| | |
|---|---|
| `fullseam.rows(name)` | A dataset as a list of objects by column name, as the reader has filtered it |
| `fullseam.datasets[name]` | Its `{columns, rows, source, read_at}` |
| `fullseam.params` | The parameters as they are set |
| `fullseam.period` | `{from, to}` |
| `fullseam.filters` | What the reader has picked |
| `fullseam.setParam(name, value)` | Sets a parameter, as the reader would |
| `fullseam.state` | The reader's choices this view has kept, by name |
| `fullseam.setState(name, value)` | Keeps one in the page's address; empty takes it out |

Every figure a view shows comes from `fullseam`. Write none into the HTML.

A choice the reader makes in a view — a tab, a basis, months or quarters — is
kept with `setState`, and read back from `state` when the view is drawn, so a
reload or a link sent to somebody opens on what was chosen:

```
let basis = fullseam.state.basis ?? 'Accrual'
fullseam.segmented('#basis', {options: ['Accrual', 'Invoiced'], value: basis,
  onChange: v => { basis = v; fullseam.setState('basis', v === 'Accrual' ? '' : v); draw() }})
```

The value it starts on is kept as empty: an untouched page has a clean address.

### The kit

A view draws with the same pieces the blocks are drawn with, so it looks and
behaves like the rest of FullSeam. Each takes the element, or its selector, to
draw into first. Write markup and styles of your own only for what the kit
has no piece for.

| Piece | Draws |
|---|---|
| `fullseam.tiles(el, [{label, value, note?, tone?}])` | A row of headline figures. `tone` is `good` or `bad`. |
| `fullseam.table(el, {rows, columns?, onRow?})` | The rows as they are, sorted by a heading, a page at a time. `columns` as a block's. |
| `fullseam.tree(el, {rows, groups, value, across?, agg?, format?, open?, drill?, onCell?, label?, totalLabel?})` | The table that adds up: `groups` is a block's `rows`. Left to itself it opens one level and writes figures `whole`, where a block opens every level and writes `money`: say `open` and `format`. `drill: true` lists the rows behind a clicked figure; `onCell({keys, across, rows, value})` hands the click to you instead. `label` heads the first column, `totalLabel` names the last row. |
| `fullseam.chart(el, {rows, x, y, series?, type, agg?, format?, line?, lineFormat?, colors?, height?})` | A block's chart. |
| `fullseam.vega(el, spec)` | A Vega-Lite 5 spec of your own, in the same look. |
| `fullseam.progress(el, [{label, value, of, note?}])` | A share of a whole as a bar. |
| `fullseam.segmented(el, {options, value, onChange})` | A choice between a few things, as a row of buttons. |
| `fullseam.tabs(el, [{label, render(el)}], {at?, onChange?})` | Pages within the view. `at` is the one it opens on, by its place; `onChange(at)` hears a change, to keep with `setState`. |
| `fullseam.drill(title, rows, columns?)` | Lists rows under the view. |

To add rows up yourself: `fullseam.sum(rows, column)`,
`fullseam.total(rows, column, agg)`, `fullseam.group(rows, columns)` (a list of
`{keys, rows}`), `fullseam.distinct(rows, column)`, and
`fullseam.regrain(rows, column, grain)`, which rolls a month column up to
`quarter` or `year`.

`fullseam.fmt` writes a figure: `money`, `whole`, `compact`, `percent`,
`delta` (+$3.0K), `month` (2026-09 as Sep 26) and `number`.
`fullseam.palette` is the colours, in order.
