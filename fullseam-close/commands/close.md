---
description: What you are on the hook for in FullSeam
---

Call `close_my_work`.

Lead with the shape of it: how many are open, how they split by status, and
which closes they are in. `total` and `by_status` say all of that.

Then the tasks that need a decision today — overdue first, then anything with
something in `waiting_on` — with name, entity (in the organization's own word,
`entity_word` on `company_get`), status, due date and your role. If there are
more than a screenful, show those and say how many you are not showing. Do not
print sixty rows because sixty came back.

`waiting_on` empty means nothing is blocking that task. Never fill that column
from anywhere else.

If `not_shown` is there, say it: that work is in an entity whose close has
already sealed.

`close_my_work` returns the first twenty by due date. When `more` is true, that
is not the whole queue — raise `limit` or page with `offset` if the person needs
all of it, and never present twenty as though it were everything.

End by asking which one to pick up.
