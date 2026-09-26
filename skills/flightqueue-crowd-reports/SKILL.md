---
name: flightqueue-crowd-reports
description: Read traveller-submitted airport wait reports, and submit one when the user is at the airport, with the FlightQueue MCP server. Use when the user asks "what are people reporting at security", "is the queue really that long", or says "I just got through security in 20 minutes".
---

# FlightQueue crowd reports

Connect the FlightQueue MCP server at `https://mcp.flightqueue.com/mcp`.
Both tools are free and need no account.

## Read reports

`get_crowd_reports` with `iata` and optional `checkpoint_type`:
`security`, `immigration`, `checkin` or `all` (default).

Each report has a terminal, a time and a confidence. Reports are a small,
noisy sample. Use them to check a predicted wait, not to replace it. Say how
many reports there are and how old the newest one is.

## Submit a report

`submit_wait_time_report` writes to the shared prediction model. A wrong
report makes predictions worse for every traveller.

Call it only when **all** of these are true:

1. The user is at the airport now, or has just passed the queue.
2. The user gave the wait they actually had.
3. The user agreed to share it. Ask first: "Do you want to share this wait
   with other travellers?"

Never submit a number you read from another tool, a guess, or an estimate.

Arguments:

| Field | Required | Notes |
|---|---|---|
| `iata` | yes | 3-letter airport code |
| `wait_time_minutes` | yes | 0-180 |
| `checkpoint_type` | no | `security` (default), `immigration`, `checkin`, `customs`, `baggage`, `lounge`, `ees_kiosk` |
| `terminal` | no | e.g. `4` or `B` |
| `precheck` | no | `true` for a TSA PreCheck or fast-track lane |
| `at_airport_now` | no | `true` when the user is at the airport |

Set `at_airport_now: true` when it applies. After the call, tell the user the
report was recorded.
