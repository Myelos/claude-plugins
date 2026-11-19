---
name: overview
description: Open servicelog.cloud and show the interactive overview card of the user's motorcycles, with each bike's odometer, the next service item needing attention and its open issues. Use when the user wants to open servicelog, see their garage or bikes, or get a quick status of everything at once. Read-only. For the full schedule of one bike use whats-due.
---

# Bike overview

Shows the user's bikes at a glance.

Reply in the user's language.

## 1. Show the overview

Call `show_bike_overview`. It takes no arguments and returns one entry per bike the user can reach, their own and any shared with them: name, ref, access, last-logged odometer, the next item needing attention and the open-issue count. Clients that render MCP Apps display it as an interactive card.

## 2. Summarise briefly

After the call, always give a short text summary from the tool's text result, whether or not a card is shown: one line per bike with what needs attention (the next item due and any open issues), and one line for the bikes with nothing pending. Keep it brief and do not repeat every field the card already shows in detail. Keep each bike's ref exactly as printed, since the user may use it next.

## 3. Next steps

This skill writes nothing itself. Offer the natural follow-ups: what is due on a bike, logging work, or reporting a problem. Never invent a status, due point or value the overview does not show.
