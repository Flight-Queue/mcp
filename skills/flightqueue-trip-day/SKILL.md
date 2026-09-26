---
name: flightqueue-trip-day
description: Plan the day of a flight with the FlightQueue MCP server. Use when the user asks "when should I leave for the airport", "what time should I get to JFK", "is my airport delayed today", or which terminal or lounge to use.
---

# FlightQueue trip day

Connect the FlightQueue MCP server at `https://mcp.flightqueue.com/mcp`.
Every tool below takes `iata`, a 3-letter airport code. If the user gives a
city or airport name, call `search_airports` with `query` first. Never guess
the code.

## Workflow

1. `get_best_time_to_fly` — the quietest arrival window, from historical
   patterns. Free keys get one recommendation; paid keys get the top 3 with
   checkpoint detail.
2. The current queue:
   - `get_live_wait_time` **(paid)** — the freshest reading, from the
     airport's own feed. Try it first when the user has Premium.
   - `get_security_wait_estimate` — the free snapshot. Use it when the live
     call fails with a Premium error, or when the user has no plan.
3. `get_faa_delays` with `airports_only_with_delays: true` — ground stops
   and delay programmes. US airports only.
4. `get_aviation_weather` — METAR, TAF and flight category. Use it to explain
   *why* a delay exists: low cloud, visibility, wind or storms.
5. `get_terminals` with `airline` — which terminal and checkpoint the user's
   airline uses.
6. `get_lounges` with `terminal` — lounges airside of that checkpoint, with
   access rules.

Skip the steps the user did not ask about. A question about the queue alone
needs steps 1 and 2.

## Present the result

Lead with one time: "Leave to reach the airport by 06:10." Then give the
reasons in this order: security queue, delays, weather, terminal, lounge.

- Say which source each wait came from. A live reading and a free estimate are
  different things; never present an estimate as observed.
- Quote a range, not a single minute value, when the tool returns a confidence
  band.
- All times are in the airport's local time.

## Paid tools

`get_live_wait_time` needs a FlightQueue Premium plan. When it returns a
Premium error, do not retry. Fall back to `get_security_wait_estimate` and
tell the user that live waits are part of https://flightqueue.com/pricing.
