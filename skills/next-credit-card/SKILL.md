---
name: next-credit-card
description: "Picks the next travel card from the user's spend, current cards, goals and live welcome bonuses. Use when the user asks which card to get next, or which card earns most for a spend category. Requires the Award Travel Finder MCP connector (https://mcp.awardtravelfinder.com/mcp)."
version: 1.0.0
---

# Choose my next credit card

The tools named below are on the Award Travel Finder MCP connector. If they are not available, tell the user to connect https://mcp.awardtravelfinder.com/mcp (setup: https://awardtravelfinder.com/developers/mcp).

## 1. Know the user

- Read the portfolio preamble. Call `list_points_balances` for current currencies.
- Ask only for what is unknown: country, current cards, monthly spend by category, and the goal.

## 2. Candidate cards

- Call `cards_rank_welcome_bonuses` for the user's country.
- Call `cards_analyze_spending` with the user's spend by category.
- For a named category, call `cards_find_cards_for_category`.

## 3. Match to the goal

- For a target airline, call `cards_find_transfer_programs_for_airline`.
- Call `cards_list_transfer_partners` for each finalist.
- Prefer a card that earns a currency the user already holds, so balances pool.

## 4. Compare finalists

- Call `cards_compare_cards` for the top 2-3.
- Call `cards_list_changes` to check for recent changes to those cards.
- First-year value = welcome bonus x `get_points_valuation` value + first-year earn - annual fee.

## Output format

1. Recommended card and why, in two lines.
2. Table: card, welcome bonus, annual fee, first-year value, key transfer partners.
3. Minimum spend and deadline.
4. One line on eligibility rules the user must check themselves.

Do not promise approval. Do not give financial advice beyond the travel-rewards math.
