---
name: award-travel
description: This skill should be used when the user asks about "award flights", "points", "miles", "avios", "asia miles", "loyalty programs", "flight availability", "award search", "redemptions", "business class availability", "first class availability", "hybrid flights", "cash and points", "split ticket", "positioning flights", "mix cash and miles", mentions specific airlines (British Airways, Qatar Airways, Cathay Pacific, Virgin Atlantic, Aeroplan, Iberia, Emirates, Etihad, Turkish, ANA, JetBlue, Singapore, Qantas, Alaska, American, Frontier, Japan Airlines, Air France, Korean Air), or discusses using points/miles for flights or hotels. Provides guidance for using the Award Travel Finder MCP tools.
version: 1.0.0
---

# Award Travel Finder

Search award flight availability across 27 airlines and 23 loyalty programs using points and miles. Powered by the Award Travel Finder MCP server.

## Available MCP Tools

### search_availability
Search award seats for a specific route and date.

**Parameters:**
- `airline`: airline slug (e.g. `british_airways`, `qatar_airways`, `cathay_pacific`, `virgin_atlantic`, `iberia`, `emirates`, `qantas`, `alaska_airlines`, `american_airlines`, `jetblue`, `etihad`, `frontier` — call `search_all_airlines` if unsure)
- `departure_code`: 3-letter IATA code (e.g. LHR)
- `arrival_code`: 3-letter IATA code (e.g. JFK)
- `date`: YYYY-MM-DD

Returns cabin-by-cabin availability with points costs.

### search_monthly_availability
Search an entire month for availability. Best for finding flexible dates. Can take up to 90 seconds.

**Parameters:**
- `airline`: airline slug
- `departure_code`: 3-letter IATA code
- `arrival_code`: 3-letter IATA code
- `date`: YYYY-MM or YYYY-MM-DD (uses the month)

### search_all_airlines
Fan out a single date across every supported airline at once. Use when the user doesn't specify an airline.

**Parameters:**
- `departure_code`: 3-letter IATA code
- `arrival_code`: 3-letter IATA code
- `date`: YYYY-MM-DD

### search_hybrid
Find the cheapest way to fly by combining cash tickets with award redemptions into one split-ticket journey. Best for premium cabins on long-haul routes.

**Parameters:**
- `origin`: 3-letter IATA code
- `destination`: 3-letter IATA code
- `date`: YYYY-MM-DD
- `cabin`: `economy` | `business` | `first` (default: business)
- `points_value_cents`: How you value points in cents per point (default: 1.5)

Returns: direct cash price, best full-award option, and hybrid split options ranked by effective cost.

### get_pricing
Get the award chart for a route. Shows points per cabin class, off-peak and peak. No date needed.

**Parameters:**
- `airline`: airline slug
- `departure_code`: 3-letter IATA code
- `arrival_code`: 3-letter IATA code

### get_program_rates
Get full award chart for a loyalty program (zones, cabin pricing, partner rules).

**Parameters:**
- `program`: Program slug — one of `british-airways`, `iberia`, `qatar-airways`, `aer-lingus`, `virgin-atlantic`, `aeroplan`, `flying-blue`, `cathay-pacific`, `singapore-airlines`, `emirates`, `etihad`, `turkish-airlines`, `ana`, `jetblue`

### get_buy_points_pricing
Current "buy miles" promotions, in case it's cheaper than redeeming.

### get_status_matches
Which programs will status-match a given tier, and how long it lasts.

### Hotels
- `search_hotels` — Find hotels by city/area for a date range.
- `get_hotel_availability` — Award nights at a specific property.
- `monitor_hotel_price` — Watch a property and ping you on price drops.
- `list_hotel_bookings` / `get_hotel_booking` — Track hotel award reservations.

### Trip portfolio (per user)
- `add_flight_booking` / `list_flight_bookings` / `get_flight_booking` / `update_flight_booking` / `delete_flight_booking`
- `update_points_balance` / `list_points_balances` / `get_portfolio`

## Workflow: Hybrid Search (Cash + Points)

1. Use `search_hybrid` with origin, destination, date, and cabin class.
2. Review the comparison: direct cash vs full award vs hybrid options.
3. The best hybrid often uses cheap short-haul award positioning (e.g. 16,500 Avios LHR→DUB) combined with a cheaper cash long-haul from the hub.
4. Warn the user about split-ticket risks: no IRROPS protection, carry-on only.

## Workflow: Finding Award Flights

1. **Confirm route and airline.** Convert city names to IATA codes. If unsure which airline, use `search_all_airlines` to fan out across every supported airline.
2. **Check pricing first** with `get_pricing` to show what the route costs in points per cabin class.
3. **Search availability** with `search_availability` for a specific date, or `search_monthly_availability` for flexible dates.
4. **Present results clearly.** Format as a table showing date, cabin class, seats available, and points required.

## Workflow: Comparing Programs

1. Use `get_program_rates` for each program the user wants to compare.
2. Focus comparison on the route/region they care about.
3. Highlight which program offers the best value per cabin class. Note fuel surcharge differences (BA, Virgin = high; Aeroplan, Asia Miles, Flying Blue = lower or none on partners).

## Authentication

The MCP server uses OAuth 2.1 by default. On first tool call, the client redirects the user to awardtravelfinder.com to sign up (or sign in) and approve access — about 30 seconds, free, no credit card. Tokens auto-refresh.

Free tier: **50 searches/month** per account. Paid tiers (150/month and up) at https://awardtravelfinder.com/pricing for power users.

There is no API key, and you must not send an `Authorization` or `X-API-Key` header. API-key auth was retired on 2026-06-07; a static header overrides the OAuth token and every request then 401s despite a successful sign-in.
