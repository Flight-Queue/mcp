---
name: flightqueue-ees-schengen
description: Check EU Entry/Exit System (EES) border queues and the Schengen 90/180-day allowance with the FlightQueue MCP server. Use when the user asks "how long is passport control at Lisbon", "what is the EES queue", "how many Schengen days do I have left", or "will this trip overstay".
---

# FlightQueue EES and Schengen

Connect the FlightQueue MCP server at `https://mcp.flightqueue.com/mcp`.
All three tools are free.

## Border queue now

`get_ees_wait_times` returns first-entry (biometric registration) and
returning-traveller waits per airport.

- One airport: pass `iata`.
- One country: pass `country` as an ISO-2 code, e.g. `PT`.
- The full board: pass neither. `limit` caps the list (default 25, max 150).

Each row has a confidence tier. Say whether a number is a live reading or a
modelled estimate.

## Border queue later

`get_ees_forecast` with `iata` gives a 24-hour forecast. Set `direction`
to `entry` for an arriving traveller, `exit` for a departing one, or
`both` (default).

A first-time EES traveller registers fingerprints and a photo at the border.
Use the first-entry figure for them, not the returning-traveller one.

## Schengen 90/180

`check_schengen_days` counts days used in any 180-day window.

- `trips`: a list of `{ entryDate, exitDate?, country? }` with ISO dates
  (`YYYY-MM-DD`). Leave out `exitDate` for a stay that is still in progress.
- To test a future trip, add `planned_entry_date` and `planned_days`.

Entry and exit days both count, and overlapping trips count once. The tool
runs offline, so the trip history does not leave the tool.

Ask the user for their trip dates. Never invent dates to fill a gap. If the
result is close to 90 days, say so clearly and tell the user to confirm with
the official EU calculator before they travel. This is not legal advice.

## Present the result

Give the wait in minutes as a range, the airport, the direction, and the
local time of the reading. For Schengen, give days used, days left, and the
date the next day becomes free.
