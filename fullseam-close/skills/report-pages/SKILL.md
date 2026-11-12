---
name: report-pages
description: Build or change a report page in FullSeam — figures, charts and tables over the books, the close's tasks, the team's time off and the files, kept as a page people open and filter. Use when asked for a report, a dashboard, a breakdown, a forecast, a budget page or close KPIs, or to change one there already. Read before calling report_put.
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

The months `balances`, `entries`, `tasks` and `time_off` are read over. The
reader can change them on the page.

- `{preset}`: `month`, `trailing_3`, `trailing_6`, `trailing_12` or
  `trailing_24`, counted back from the latest posted month.
- `{from, to}`, each `YYYY-MM`.

### `datasets`

`{name: dataset}`. Each is one table of rows.

| `source` | Takes | Rows |
|---|---|---|
| `balances` | `entity?`, `statement?`, `accounts?`, `search?` | The posted trial balances, a row for each account and month: entity, month, account, name, statement, category, type, balance, movement. Every entity the reader sees when none is named. |
| `entries` | `entity?`, `accounts` | The ledger's entry lines on those accounts, read live: entity, account, date, month, type, number, name, memo, split, amount, id (the transaction's, as `books_read` takes it with `type`), and whatever else the ledger tags a line with. |
| `tasks` | `entity?` | The tasks of the closes in those months that the reader sees, a row each: month, task, id, link (the task's page in FullSeam), close_status (the month's close: Setup, In Progress or Locked), entity, gl_accounts (the accounts its recs say it covers), type, section, status, needs_review, preparers, reviewers, second_reviewers, preparer_due, reviewer_due, prepared_on, prepared_by, reviewed_on, reviewed_by, second_reviewed_on, second_reviewed_by, completed_on, changes_requested, and a column for each of the company's task tags, named by its label. `prepared_on` is the first submission since the task was last moved back to In Progress or Not Started, so a resubmission after Needs Changes does not move it; `reviewed_on` is the approval that let it through; `changes_requested` how many times a reviewer sent it back. |
| `people` | | The people the reader is shown: person, email, holiday_calendar. |
| `time_off` | | Who is out in those months: person, holiday_calendar, starts_on, ends_on, kind (Full day, Half day (AM), Half day (PM), Holiday). A holiday is a row for each person whose calendar keeps it. Only where the company keeps time off. |
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
{kind: figure, dataset, value, agg?, format?, target?, breakdown?, colors?}
```

`target` is where the figure should be, in its own units: 0.95 for a percent
of 95%. It is shown, and the figure is coloured by which side of it it is on.
`breakdown` is a column: its rows counted by its values, drawn under the
figure as one bar with each value's count. `colors` colours them, as a chart's.

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
{kind: table, dataset, columns?, height?}
```

Every row is in it; it scrolls past `height` pixels (520 when left out), and
the reader filters any column and sorts by any heading.
`columns` is the ones to show, each a name, or `{key, label?, format?, bar?,
pill?}`. Only a column of numbers takes a `format` or a `bar`: a bar beside
each figure, as long as its share of the column's largest (of 100% for a
percent). `pill` draws a short text — a team, a status — as a pill.

**A table that adds up.**

```
{kind: table, dataset, rows: [columns], value, across?, agg?, format?, open?, height?}
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
[{name, label?, type, default, min?, max?, options?, group?, drawer?}]
```

`type` is `number`, `percent`, `integer`, `month`, `choice` (with `options`)
or `text`. A percent is a fraction: 0.05 for 5%. A `label` is two or three
words, with no unit or range in it. `group` is the heading related ones sit
under. With `drawer: true` the group is out of the way, in a drawer the
reader opens by the group's name: scoring rules, assumptions set once. A
parameter does nothing by itself: a run reads it.

### Runs

For figures no dataset holds: a ratio, a forecast, rows sorted into kinds.

```
runs: {name: {script, inputs: {name: {source, ...}}, workbook}}
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
- An input is a dataset written out in full, as under `datasets`, of any
  source but `run`. Naming one of the page's datasets is not enough.

The run is made before the page is kept, and a script that fails comes back
with what it printed. It is made again, without you, when the reader sets a
parameter or the months, or presses Refresh.

Reach for a run when the arithmetic is the report. A table that adds up
already has subtotals; that alone is not a reason for a script.

## A view: a block you draw yourself

A block can be HTML of your own: a layout, an interaction, several things
that move together, a look the blocks do not have.

```
{kind: view, html, datasets?: [names], title?, width?, height?}
```

Left out, `height` is as tall as the view draws itself; given, it is fixed at
that many pixels, empty below what is drawn.

Nothing in a view is checked against its data, and nobody has looked at what
it draws. Where you can see a page drawn, look at it once it is kept, and fix
what you see before you say it is done.

The HTML runs in a frame of its own that reaches nothing: no network, nothing
of the page around it, and no other address to go to. A link
(`<a href="…">`) opens in FullSeam where it is a task's `link`, a close
(`/close/<period id>`), a rec (`/close/<period id>/recs/<rec id>`) or
`/team`, without anything after the path; any other link does nothing, and
on a page opened from a shared link none do. It is handed `fullseam`:

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
| `fullseam.download(name, rows)` | Saves rows (a list of lists, headings first) to the reader's computer as `name`.csv |

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

### The look

A view is drawn in Geist, at 13px, before you style anything. Style it with
FullSeam's own, and write a size or colour of your own only where none of
these will do:

| | |
|---|---|
| Sizes | `--fs-label` 11px, `--fs-body` 13px, `--fs-heading` 14px, `--fs-title` 18px, `--fs-figure` 26px |
| Text | `--fs-ink`, `--fs-ink-2`, `--fs-muted`, `--fs-faint` |
| Surfaces | `--fs-surface`, `--fs-bg`, `--fs-soft`, `--fs-line` |
| Tones | `--fs-accent`, `--fs-good`, `--fs-warn`, `--fs-bad`, each with a `-soft` to fill behind it |
| Space | `--fs-gap-1` to `--fs-gap-5`: 4, 8, 12, 16, 24px; `--fs-radius`, `--fs-radius-sm` |
| Fonts | `--fs-font`, `--fs-mono` |

Classes for what every page is made of: `fs-card`; `fs-title`, `fs-heading`,
`fs-label`, `fs-figure`; `fs-button`, with `fs-primary` for the one that
matters; `fs-badge`, with `fs-good`, `fs-warn`, `fs-bad` or `fs-accent`;
`fs-row` to set things side by side; `fs-num` for figures in a column;
`fs-muted`, `fs-faint`.

`fullseam.icon(name, size?)` is one of the icons the app draws with —
Lucide's, by their names (`search`, `chevron-down`, `sliders-horizontal`) — as
markup, in the colour of the text around it. Draw no icon of your own.

### The kit

The pieces the blocks are drawn with, for a view to use where they fit. Each
takes the element, or its selector, to draw into first. Where a design asks
for something they do not draw, write it yourself.

| Piece | Draws |
|---|---|
| `fullseam.tiles(el, [{label, value, note?, tone?, aside?, asideTone?, target?, parts?}])` | A row of headline figures. `tone` is `good` or `bad`. `aside` is a few words at the top right — a change since last month — toned by `asideTone`. `target` is a line under the figure ("Target 95%"). `parts`, `[{label, value, color?}]`, is one bar under it with each part's count. |
| `fullseam.table(el, {rows, columns?, onRow?, sort?, onSort?, filters?, onFilter?, height?})` | Every row, scrolling past `height` pixels, sorted by a heading and filtered by any column. `columns` as a block's. `sort` is a column, `-` before it for largest first; `filters` is `{column: text}`. `onSort` and `onFilter` hear the reader's, to keep with `setState`. |
| `fullseam.tree(el, {rows, groups, value, across?, agg?, format?, open?, drill?, onCell?, label?, totalLabel?})` | The table that adds up: `groups` is a block's `rows`. Left to itself it opens one level and writes figures `whole`, where a block opens every level and writes `money`: say `open` and `format`. `drill: true` lists the rows behind a clicked figure; `onCell({keys, across, rows, value})` hands the click to you instead. `label` heads the first column, `totalLabel` names the last row. |
| `fullseam.chart(el, {rows, x, y, series?, type, agg?, format?, line?, lineFormat?, colors?, height?})` | A block's chart. |
| `fullseam.vega(el, spec)` | A Vega-Lite 6 spec of your own, in the same look. |
| `fullseam.progress(el, [{label, value, of, note?}])` | A share of a whole as a bar. |
| `fullseam.segmented(el, {options, value, onChange})` | A choice between a few things, as a row of buttons. |
| `fullseam.tabs(el, [{label, render(el)}], {at?, onChange?})` | Pages within the view. `at` is the one it opens on, by its place; `onChange(at)` hears a change, to keep with `setState`. |
| `fullseam.select(el, {label?, options, value, multiple?, onChange})` | A pick-list behind a button that names what is picked. With `multiple`, `value` is a list, and an empty one is all. |
| `fullseam.drawer({title, render(el), width?})` | A panel from the right, over the view, for what is set once and put away. Answers `{open(), close()}`; `render` draws it the first time it opens. |
| `fullseam.drill(title, rows, columns?)` | Lists rows under the view. |

To add rows up yourself: `fullseam.sum(rows, column)`,
`fullseam.total(rows, column, agg)`, `fullseam.group(rows, columns)` (a list of
`{keys, rows}`), `fullseam.distinct(rows, column)`, and
`fullseam.regrain(rows, column, grain)`, which rolls a month column up to
`quarter` or `year`.

`fullseam.fmt` writes a figure: `money`, `whole`, `compact`, `percent`,
`delta` (+$3.0K), `month` (2026-09 as Sep 26) and `number`.
`fullseam.palette` is the colours, in order.
