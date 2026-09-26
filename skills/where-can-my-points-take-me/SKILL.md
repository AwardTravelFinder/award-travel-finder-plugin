---
name: where-can-my-points-take-me
description: "Turns the user's balances into 3-5 concrete award trips they can afford, with live seats and transfer paths. Use when the user has points but no fixed destination, or asks \"what can I do with my points?\". Requires the Award Travel Finder MCP connector (https://mcp.awardtravelfinder.com/mcp)."
version: 1.0.0
---

# Where can my points take me?

The tools named below are on the Award Travel Finder MCP connector. If they are not available, tell the user to connect https://mcp.awardtravelfinder.com/mcp (setup: https://awardtravelfinder.com/developers/mcp).

> **Plan:** needs Premium: `update_points_balance`, `search_monthly_availability`. If the user is on the free plan, tell them before you call these tools, and use the free steps where the skill gives one.

Goal: 3-5 trips the user can book with the points they already hold.

## 1. Read the balances

- Call `list_points_balances`. If it is empty, ask the user for their main balances. Premium users can save them with `update_points_balance`. For free users, pass one balance straight to step 2.
- Call `get_portfolio` for home airport and past destinations when the origin is unknown.
- Ask only for origin, month and cabin if they are still unknown.

## 2. Find destinations

- Call `explore_destinations` with origin, cabin and the month range. It uses the saved balances, or a points balance and programme you pass. It lists destinations with award seats, cheapest first, and says which ones the balances cover directly or through transfer partners.
- Free accounts see the top 3 destinations. Say so if the user is on the free plan.
- The data is cached. Each row shows its age.

## 3. Confirm live seats

- For the 3-5 best reachable destinations, call `search_all_airlines` for a date in the month. Premium users can call `search_monthly_availability` for the whole month.
- Drop any destination with no live seats.

## 4. Cost each trip

- Call `estimate_award_fees` for each route and cabin to show the taxes and surcharges per programme. Say they are estimates.
- Call `compare_cash_vs_points` for the top pick, so the user sees if points beat cash for that trip.
- For a destination reachable only through a transfer, call `find_transfer_paths`. Check `whats_on_sale` for a transfer bonus.
- Mark a trip "reachable" only if balance + bonus covers the points for all passengers.

## Output format

A table: destination, programme, cabin, points per person, estimated taxes, best dates, reachable yes/no.
Then one line per trip on how to pay (transfer path), and one line on cash vs points for the top pick.
End with: "Say a destination and I will run the full plan." Use the plan-award-trip skill for that.
