---
name: log-maintenance
description: Record completed motorcycle maintenance into a bike's servicelog.cloud service record. Use whenever the user reports work as done, fixed, replaced, adjusted, installed or serviced, such as "I changed the oil" or "log this", even if they never say "log". Asks focused questions and confirms the entry before writing one add_maintenance_entry call. Not for a fault that has not been fixed yet (use report-problem) or for checking what is due (use whats-due).
---

# Log maintenance

Turns a reported job into one complete servicelog.cloud record. A job is a single record with two views, a lean log line and (for in-depth work) a detailed write-up, so they never drift; `add_maintenance_entry` writes both in one transaction. This skill covers the judgement around that call and the house style.

Reply in the user's language, but write everything you store in English: the web app localizes for each reader.

## 1. Resolve the bike

Call `list_bikes`. If the user has not named a bike, or the name could match more than one, use the exact ref it prints. A shared bike's ref is `@owner-handle/slug`; a bare slug always resolves to the user's own bikes first. Never guess when the user has more than one bike.

Before asking anything, check the access `list_bikes` prints in brackets for that bike. `add_maintenance_entry` needs contributor or owner access. If the user has reporter or viewer access, tell them up front that they cannot log work on this bike, and do not run the interview.

## 2. Ask before writing

Ask a proportional set of focused questions, all in one message, then wait. One or two for a simple job, more for an in-depth one. Cover, as relevant:

- What was changed, adjusted or checked, item by item.

- The date (YYYY-MM-DD) and the odometer reading.

- Parts and part numbers used.

- For a fault: the root cause and how it was confirmed.

- Measurements and torque values applied.

- Whether it resolved an existing issue or surfaced a new one.

Do not nag. If the user cannot recall a detail or does not want it recorded, note that briefly (for example "method not recorded") and move on. If the exact day is unknown, use the best date and set `date_is_approximate`.

## 3. Check the record

Before composing, call `read_bike` with the bike's ref and:

- `section: "profile"` for the last-logged odometer. The odometer unit is km unless the profile shows `Odometer unit: mi` or `list_bikes` prints `· odometer in mi` for the bike; ask for and record readings in that unit.

- `section: "intervals"` for the exact interval names to use in `due_items_completed` or `interval_updates`. Names match tolerantly on case, accents and punctuation, never on wording; a mismatch aborts the whole entry.

- `section: "issues"` when the job may resolve an active issue, so `resolved_issue.title` matches the stored title.

Never invent a torque, capacity or spec. Look it up with `search_bike` or `read_manual`, cite the manual when you use it, and say plainly when the value is not in the record.

## 4. Classify

- **In-depth**: diagnosis, wiring, root-cause work, a multi-step procedure, or anything with cost, torque or measurements worth keeping. Set `is_in_depth: true` and write `details_markdown`.

- **Routine**: an oil and filter change, a simple check. Log lines only, `is_in_depth: false`, no write-up fields.

## 5. Compose in house style

- **`log_items`**: one item per thing done, with the part number in `part_number` rather than in the text, for example `Oil filter changed` with `part_number: "HF-145"`. No cost, no torque and no prose summary in log lines.

- **`details_markdown`** (in-depth only): scope or fault and root cause, diagnosis, numbered steps, measurements, parts, notes. Put torque values in `torque_notes`, cost in `cost`, and parts in `parts` where they help.

- **`gotcha`**: a durable lesson worth knowing before an unrelated future job on this bike. Do not bury it in the write-up.

- **`resolved_issue`** for an active issue this job fixed (with an `outcome`); **`new_active_issue`** for a fault found during the job that stays open.

- **`due_items_completed`** for intervals this job satisfies at the job's own date and odometer; **`interval_updates`** only when a different date or odometer applies.

## 6. Confirm

Show the user a short summary of what you will write: title, date, odometer, the log lines, routine or in-depth, and any issue, interval, gotcha or odometer change. Wait for an explicit yes. Adjust and re-confirm if they change anything.

## 7. Write once

Make exactly one `add_maintenance_entry` call with the assembled payload. If the new odometer reading is higher than the bike's last-logged odometer, include `update_odometer_last_logged` in that same call rather than a separate `set_odometer`, so interval statuses refresh atomically.

- On a rollback error nothing was saved: fix what the error names and re-send the complete call.

- A duplicate-job error means the entry already exists and nothing changed. Never re-send it; to correct an existing job, find its id with `search_bike` and use `update_maintenance_entry` after the user confirms.

Afterwards, tell the user what changed.
