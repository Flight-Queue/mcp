---
name: flightqueue-baggage
description: Look up airline baggage rules and lost-bag records with the FlightQueue MCP server. Use when the user asks "what is the carry-on size for Delta", "how much is a checked bag on United", "which airline loses the fewest bags", or "is it safe to check a bag on this airline".
---

# FlightQueue baggage

Connect the FlightQueue MCP server at `https://mcp.flightqueue.com/mcp`.
Both tools are free. The data covers US airlines and comes from US Bureau of
Transportation Statistics (BTS) reports.

## One airline

`get_airline_baggage_policy` with `carrier` — an airline slug such as
`american-airlines` or `delta`, or a 2-letter IATA code such as `AA`.

It returns carry-on size, checked-bag fees, the mishandled-bag rate and the
trend.

## All airlines

`get_airline_baggage_rankings` with optional `limit` (default 20, max 50)
— airlines ranked by how rarely they mishandle bags.

## Present the result

- For a policy question, give the carry-on size and the first checked-bag fee
  first. Then give the lost-bag rate.
- For a ranking, show the top and bottom airlines with their rates, and the
  period the BTS data covers.
- Fees change often. Tell the user to confirm the fee on the airline's own site
  before they pay.
- A non-US airline returns "not found". Say that the dataset is US-only; do not
  guess a policy.
