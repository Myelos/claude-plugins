---
name: whats-due
description: Show which service intervals are overdue or coming up on a motorcycle in servicelog.cloud, soonest first, and explain each status. Use when the user asks what is due, what service the bike needs next, whether anything is overdue, or when the next oil change or inspection falls. Read-only. Not for recording work that was done (use log-maintenance).
---

# What's due

Reports the service intervals on one bike from its servicelog.cloud record. This skill only reads: it writes nothing and offers no write.

Reply in the user's language. Keep interval names exactly as the record stores them.

## 1. Resolve the bike

If the user has not named a bike, call `list_bikes` and use the exact ref it prints; a shared bike's ref is `@owner-handle/slug`. If that lists several bikes, ask which one they mean or offer to cover all of them; never guess. If they want all of them, repeat the steps below for each ref. If a ref matches no bike or several, call `list_bikes` and ask which one they mean.

## 2. Read the intervals

Call `read_bike` with the bike's ref and `section: "intervals"`. `read_bike` takes one section per call, so if you need the current last-logged odometer to explain a distance, make a second `read_bike` call with `section: "profile"`.

## 3. Report

List overdue items first, then upcoming ones, each group soonest first, with its due odometer or date. Mention items that are never done or unknown after that, list items marked not applicable separately, and summarise the rest as ok in one line.

Explain the statuses briefly where they appear:

- **overdue**: past its due odometer or date.

- **upcoming** (due soon): within about 10% of its interval, at least 500 km or 30 days.

- **ok**: not due yet.

- **unknown**: no due point can be computed; the hint says what to record (an odometer or date baseline, or a readable odometer).

- **never-done** (due, no record): the interval has a schedule but no service on record.

- **not applicable**: the item does not apply to this bike, and the reason, if recorded, shows under Next due. Never report it as ok or as due.

A due point with a leading `~` is derived from the interval's cadence and moves as work is logged. A plain one was set as a fixed due point and does not move.

Only report intervals the record lists. Never invent an interval, a cadence or a due point, and never fill in a manufacturer value the record does not show. If the user wants to log work they have done, hand over to the log-maintenance workflow.
