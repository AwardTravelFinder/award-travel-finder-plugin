---
name: check-trip-before-departure
description: "A pre-flight briefing - flight status, seat, lounges, wifi, airport waits, and a reprice check on the award. Use when the user has a flight in the next 2 weeks, or asks \"am I ready for my trip?\". Requires the Award Travel Finder MCP connector (https://mcp.awardtravelfinder.com/mcp)."
version: 1.0.0
---

# Check my trip before departure

The tools named below are on the Award Travel Finder MCP connector. If they are not available, tell the user to connect https://mcp.awardtravelfinder.com/mcp (setup: https://awardtravelfinder.com/developers/mcp).

## 1. Find the flight

- If the user gave no flight, call `list_flight_bookings` and pick the next departure.
- Call `lookup_flight` with the flight number and date for times, aircraft and status.

## 2. Briefing

- Call `get_trip_briefing` with flight number, date, origin and destination. It returns seatmap, delay and security-wait data.

## 3. Seat

- Call `fs_find_best_seats` for the flight and cabin. Compare with the seat on the booking, if known.
- If the user wants a better seat, offer `fs_create_seat_alert`.

## 4. Lounges

- Call `list_loyalty_statuses` to learn the user's status.
- Call `lounge_get_airport_lounges` for the departure airport.
- Call `lounge_find_lounges_by_access` when the user has a card or status that grants access.

## 5. Wifi

- Call `sw_get_flight_wifi` for the flight.

## 6. Reprice check

- Call `search_all_airlines` for the same route, date and cabin.
- If the same award now costs fewer points, tell the user. Call `get_cancellation_fee` so they know the cost of rebooking.

## Output format

A short briefing with headings: Flight, Seat, Lounges, Wifi, Airport, Reprice. One or two lines each. Put any action (seat change, rebook) at the top.
