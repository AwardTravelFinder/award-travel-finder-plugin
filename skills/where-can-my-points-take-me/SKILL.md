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

- Call `list_points_balances`. If it is empty, ask the user for their main balances and save each one with `update_points_balance`.
- Call `get_portfolio` for home airport and past destinations when the origin is unknown.
- Ask only for origin, month and cabin if they are still unknown.

## 2. Find the reachable programmes

- For each transferable currency, call `find_transfer_paths` to the airline programmes that matter for this origin.
- Check `whats_on_sale` for transfer bonuses. A bonus can put a trip in reach.
- Load `get_skill` with key award-sweet-spots to pick candidate destinations.

## 3. Check live seats

- Pick 4-6 candidate routes from the sweet spots that match the origin and cabin.
- For each, call `search_monthly_availability` for the month.
- Drop any route with no seats in the month.

## 4. Cost each trip

- Compare points needed with the user's balances after transfer.
- Call `get_points_valuation` to show cents per point.
- Mark a trip "reachable" only if balance + bonus covers the points for all passengers.

## Output format

A table: destination, programme, cabin, points per person, taxes, best dates, reachable yes/no.
Then one line per trip on how to pay (transfer path).
End with: "Say a destination and I will run the full plan." Use the plan-award-trip skill for that.
