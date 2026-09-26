---
name: flightqueue-compare-airports
description: Choose which airport to fly from with the FlightQueue MCP server. Use when the user asks "should I fly from JFK or Newark", "which London airport has the shortest security queue", "compare LAX and SFO", or which hub to connect through.
---

# FlightQueue compare airports

Connect the FlightQueue MCP server at `https://mcp.flightqueue.com/mcp`.

## Workflow

1. `search_airports` with `query` (a city or name) and optional `country`
   (ISO-2). Use it to turn "London" or "New York" into real airport codes.
   Metro codes such as LON and NYC are not airports.
2. `compare_airports` with `iata_codes` — a list of 2 to 5 codes. It returns
   average security and immigration waits side by side.
3. `get_airport_delays_summary` with `iata` for each candidate — current FAA
   ground stops and delay programmes. Major US airports only; other airports
   return an `unknown` status. Do not read `unknown` as "no delays".
4. `get_historical_wait_times` with `iata` **(paid)** — daily and weekly
   patterns. Use it when the user travels on a known day and time, e.g. "Friday
   evening".

## Present the result

Show a small table: airport, average security wait, immigration wait (if
international), current delay status. Then give one recommendation and the
main reason for it.

Averages do not show today's queue. For a flight today, add the current wait
from the `flightqueue-trip-day` skill.

## Paid tools

`get_historical_wait_times` needs a FlightQueue Premium plan. When it
returns a Premium error, do not retry. Give the comparison from the free tools
and tell the user that day-by-day patterns are part of
https://flightqueue.com/pricing.
