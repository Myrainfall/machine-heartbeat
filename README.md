# machine-heartbeat

One scheduled GitHub Actions workflow that checks, once a day, whether a machine has checked in
within the last 26 hours. If it has not, the run FAILS — and the failure email is the alarm.

That is the whole repository. There is no application code here.

## Why it is public

A watchdog running on the machine it watches cannot report that machine being dead. This one runs
on GitHub's machines instead — but it originally lived in a private repository, and on 2026-08-27
GitHub stopped starting Actions jobs on that account for billing. The watchdog died with them, and
because a job that never *starts* still shows up as a failed run, it began emitting a daily false
alarm indistinguishable from a real one. That is worse than no alarm: it teaches the reader to
ignore the one signal that means something is wrong.

Actions minutes are unlimited on public repositories, so here it cannot fail that way again.

## What it can see

Nothing sensitive. It reads one row — a machine name and a timestamp — over a REST endpoint using a
**publishable** anon key that the corresponding web app already sends to every browser. Row-level
security allows anonymous SELECT on that one table and nothing else; every write path requires a
service key, which is **not** in this repository and must never be added to it.

## Keeping it alive

GitHub suspends scheduled workflows in repositories with roughly 60 days of no activity. If this
repo goes quiet that long the cron stops firing. Pushing any commit — or pressing **Run workflow**
on the Actions tab — resets that clock.

## Telling a real alarm from an infrastructure one

A failed run with a **log** is a real alarm: read it, it says how old the newest beat is.
A failed run with **no log at all** was never started by GitHub — that is an account or billing
problem, not a quiet machine.
