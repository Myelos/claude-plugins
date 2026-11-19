---
name: report-problem
description: Help diagnose a fault on a motorcycle in servicelog.cloud and, if the user agrees, record it as an open issue. Use when the user describes a symptom or something wrong with a bike that is not fixed yet, such as a noise, a leak, poor starting or a warning light. Checks the bike's record and manual before suggesting causes. Not for work already done (use log-maintenance).
---

# Report a problem

Works through a fault with the user, grounded in the bike's own servicelog.cloud record and manual, then offers to record it.

Reply in the user's language, but write anything you store in English: the web app localizes for each reader.

## 1. Resolve the bike

Call `list_bikes`. If the user has not named a bike, use the exact ref it prints; a shared bike's ref is `@owner-handle/slug`. Never guess when the user has more than one bike.

Before asking anything, check the access `list_bikes` prints in brackets for that bike. `open_issue` and `update_issue` need reporter, contributor or owner access. If the user has viewer access, tell them up front that they cannot record an issue on this bike, and run the diagnosis only if they still want it, without offering to record anything.

## 2. Ask about the symptoms

In one message, ask what happens, when it happens (cold or warm, speed, load, weather), since when, and anything that changed recently (work done, parts fitted, fuel, a fall). Then wait.

## 3. Check the record

Before diagnosing:

- Call `read_bike` with `section: "gotchas"` and with `section: "issues"`. The fault may already be known, or a past fix may point at the cause.

- Call `search_bike` with a few specific words from the symptom.

- Call `read_manual` with a query for the relevant troubleshooting or procedure section.

## 4. Diagnose

Summarise the likely causes, most likely first, and what would confirm or rule out each one. Cite the manual where you rely on it. If the manual and record do not cover it, say so rather than guessing, and never invent a torque, clearance, capacity or other spec.

Flag anything safety-critical plainly: brakes, fuel leaks, electrical faults that could cause a fire or a loss of lights, steering, and load-bearing or easily stripped fasteners. Say when the bike should not be ridden until it is checked.

## 5. Offer to record it

Offer to record the fault. Write nothing until the user agrees.

- For a new fault, propose a short title and the symptoms as the description, then call `open_issue` with `title`, `description` and, if known, `opened_date` (YYYY-MM-DD). Titles are unique per bike including resolved issues.

- If an active issue already covers it, find its id with `search_bike` (`read_bike` shows no ids) and offer `update_issue` to add detail. `description` replaces the stored text, so send the existing description with the new detail added.

- If a resolved issue has come back, find its id the same way and offer to reopen it with `update_issue` and `status: "active"`. Tell the user in that offer that reopening erases the issue's stored outcome, resolved date and link to the resolving job. To keep that history, read the issue's current description and its earlier outcome and resolved date from `read_bike` with `section: "issues"` (the line ends `Outcome: ... (resolved YYYY-MM-DD)`), and in that same `update_issue` call send a `description` that keeps the existing text, adds a line such as `Previously resolved YYYY-MM-DD: <outcome>`, then the new symptoms. Never send `outcome`, `resolved_date` or `resolved_by_job_id` with `status: "active"`: the call is rejected.

Once the fault is fixed, the log-maintenance workflow records the job and resolves the issue in the same entry.
