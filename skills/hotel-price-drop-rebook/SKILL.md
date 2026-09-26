---
name: hotel-price-drop-rebook
description: "Tracks a booked hotel stay, and rebooks when the points or cash rate falls below what the user paid. Use when the user has a refundable hotel booking, or asks \"can I save on my hotel?\". Requires the Award Travel Finder MCP connector (https://mcp.awardtravelfinder.com/mcp)."
version: 1.0.0
---

# Rebook a hotel when the price drops

The tools named below are on the Award Travel Finder MCP connector. If they are not available, tell the user to connect https://mcp.awardtravelfinder.com/mcp (setup: https://awardtravelfinder.com/developers/mcp).

## 1. Find the booking

- Call `list_hotel_bookings`. If the stay is there, use it.
- If not, collect hotel, dates, room type, rate paid and confirmation number. Ask only for what is missing.
- Call `list_hotels` or `get_hotel` to resolve the hotel code.
- Save it with `add_hotel_booking`.

## 2. Check the current price

- Call `get_hotel` for the dates and compare with the original rate.
- A drop is real only for the same room type and the same cancellation terms.

## 3. Decide

- Cheaper now and the booking is refundable: tell the user to book the new rate first, then cancel the old one.
- Non-refundable: tell the user a rebook is not possible, and do not start monitoring.
- Same price: start monitoring (step 4).

## 4. Monitor

- Call `monitor_hotel_price` with hotel code, dates, original points, room type and confirmation number.
- Offer `create_standing_order` for a weekly summary of all tracked stays.

## Output format

1. Current rate vs. rate paid, and the saving.
2. Action: rebook now / monitoring started / not possible.
3. Rebook steps: book new, confirm, then cancel old.
