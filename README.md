# Flight Queue

Airport intelligence for AI assistants. A hosted, remote **MCP server** at
[`mcp.flightqueue.com`](https://mcp.flightqueue.com) covering security wait times and
forecasts, FAA delays, EU Entry/Exit System border queues, lounges, terminals, aviation
weather, and airline baggage performance for 8,000+ airports.

Nothing to install. Point any MCP-compatible client at the hosted URL.

## Quick setup

**Claude Code** — run these three commands, one at a time:

```text
/plugin marketplace add Flight-Queue/mcp
/plugin install flightqueue@flightqueue
/flightqueue:setup
```

Your assistant connects to FlightQueue, signs you in over OAuth, asks for your home
airport and shows live waits and delays.

**Claude.ai and ChatGPT** — add a custom connector with the URL
`https://mcp.flightqueue.com/mcp`, sign in when asked, then ask it to set up FlightQueue.

**Codex, Cursor and other clients, or any agent without plugins** — add the MCP server
(see below), then send this prompt:

```text
Read https://flightqueue.com/setup.md and follow it to set up FlightQueue for me.
```

## Add it to your assistant

**Claude Code**

```bash
claude mcp add --transport http flightqueue https://mcp.flightqueue.com/mcp
```

**Claude Desktop, Cursor, Windsurf**

```json
{
  "mcpServers": {
    "flightqueue": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.flightqueue.com/mcp"]
    }
  }
}
```

**Claude.ai and ChatGPT** — add a custom connector pointing at
`https://mcp.flightqueue.com/mcp`.

**Grok Build** — install the `flightqueue` plugin from the xAI plugin marketplace.

Full per-client instructions: [mcp.flightqueue.com](https://mcp.flightqueue.com)

## Sign-in

OAuth 2.1 with PKCE. Your client runs the flow on first use and you sign in with a
Flight Queue account. There is no anonymous access and no API-key header.

A free account reaches 29 of the 32 tools. A
[premium plan](https://flightqueue.com/pricing) unlocks forecasts, history and the live
feed.

## Tools

Free, with any signed-in account:

| Tool | What it does |
| --- | --- |
| `search_airports` | Search 8,000+ airports by name, city, IATA or ICAO |
| `get_airport_overview` | Location, terminals, runways, current delays |
| `compare_airports` | Side-by-side wait and delay comparison |
| `get_faa_delays` | Current US FAA delays, ground stops, weather impacts |
| `get_airport_delays_summary` | FAA delay status for one airport |
| `get_security_wait_estimate` | Current security wait snapshot |
| `get_best_time_to_fly` | Recommended arrival window for a flight time |
| `get_crowd_reports` | Recent traveller-submitted wait reports |
| `get_airline_baggage_rankings` | Airlines ranked by mishandled-bag rate |
| `get_airline_baggage_policy` | Carry-on and checked policy for an airline |
| `get_ees_wait_times` | EU Entry/Exit System border waits across Schengen airports |
| `get_ees_forecast` | 24h EES border forecast, entry vs exit |
| `check_schengen_days` | Schengen 90/180 allowance and overstay projection |
| `get_aviation_weather` | METAR, TAF and flight category |
| `get_lounges` | Lounges by terminal, with access rules and amenities |
| `get_terminals` | Terminals, their airlines and checkpoints |
| `get_gate_walk_times` | Longest walk in each terminal and gate-to-gate walk times |
| `get_typical_wait_by_hour` | Typical security wait for each hour, busiest and quietest hours |
| `get_fast_track_offers` | Fast track passes and memberships at an airport, with prices |
| `check_connection_time` | Is a layover long enough: walk, security, passport and EES waits |
| `get_flight_wifi` | Wi-Fi system and Starlink odds for a flight number (SeatWifi data) |
| `get_security_wait_report` | Monthly security wait report per airport from live readings |
| `submit_wait_time_report` | Report an observed wait; feeds the shared model |
| `create_wait_time_alert` | Alert when a wait crosses a threshold |
| `submit_feedback` | Send a bug report, feature request or feedback to the Flight Queue team |
| `open_flightqueue` | Airport wait times panel for the ChatGPT sidebar |
| `list_skills`, `get_skill` | Read the Flight Queue agent skills (for clients without MCP Skills) |
| `discover_more_flight_tools` | Other Flight Queue MCP servers worth connecting |

Premium:

| Tool | What it does |
| --- | --- |
| `get_wait_time_predictions` | 24h and 7-day security wait forecasts |
| `get_historical_wait_times` | 7-day and 90-day hourly history |
| `get_live_wait_time` | Real-time scrape from 69+ airports |

Every tool is annotated. The tools that write (`submit_wait_time_report`,
`create_wait_time_alert`, `submit_feedback`) are marked non-destructive: they add a
report, an alert or a message and delete nothing.

## Events, tasks and forms

Clients on MCP protocol revision `2026-07-28` also get three extensions. Older clients
see no change.

- **[Events](https://modelcontextprotocol.io/extensions/events/overview)**
  (`io.modelcontextprotocol/events`): `events/list` and `events/poll`. Poll delivery only.

  | Event | Fires when | Plan |
  | --- | --- | --- |
  | `faa.delay_program` | The FAA starts or ends a ground stop or ground delay program. Filter by `airports`, `types`, `changes`. Replays up to 7 days from a cursor. | Free |
  | `wait_time.threshold_crossed` | An airport's security wait goes above (or below) `threshold_minutes`. | Premium |
  | `wait_time.spike` | An airport's security wait rises `rise_minutes` (default 15) from its recent low. | Premium |

- **Tasks** (`io.modelcontextprotocol/tasks`): a read-only tool still running after
  about 4 seconds returns a task handle (`resultType: "task"`). Poll `tasks/get` for the
  result; `tasks/cancel` stops it. Tasks are kept for 1 hour and only their owner can read them.
- **Elicitation** (form mode): `submit_wait_time_report` asks the user before it publishes,
  `create_wait_time_alert` shows the threshold and direction to check or change, and
  `submit_feedback` lets the user edit the message before it is sent.

## Skills

The `skills/` folder has agent skills that tell an assistant which tools to call, in
which order, for common travel questions. Claude Code and Codex load them with the plugin.

| Skill | Use it for |
| --- | --- |
| `flightqueue-setup` | First-time setup: confirm the connection, learn the home airport, show a live snapshot, offer an alert |
| `flightqueue-trip-day` | When to leave for the airport: best arrival time, queue, delays, weather, terminal, lounge |
| `flightqueue-ees-schengen` | EES border queues now and in 24h, and the Schengen 90/180-day check |
| `flightqueue-compare-airports` | Which airport to fly from: waits, delays and weekly patterns side by side |
| `flightqueue-baggage` | Carry-on and checked-bag rules, and which airlines lose the fewest bags |
| `flightqueue-crowd-reports` | Read traveller wait reports, and share a wait only with the user's consent |

## Ask it things like

- "What is the security wait at JFK right now?"
- "I have an 8am flight from ORD tomorrow, when should I arrive?"
- "Are there any FAA ground stops today?"
- "How long is the EES border queue at Lisbon?"
- "Which airline loses the fewest checked bags?"
- "Will my flight UA1 have Starlink Wi-Fi?"
- "I land at Heathrow T5 from New York with a 75-minute connection. Is that enough?"
- "Is the queue at Atlanta busier than usual right now?"

## Repository contents

This repo is manifests and skills only; the server itself is hosted.

| File | Purpose |
| --- | --- |
| `.mcp.json` | MCP server config |
| `.grok-plugin/plugin.json` | xAI plugin marketplace |
| `.claude-plugin/plugin.json` | Claude Code plugin |
| `.claude-plugin/marketplace.json` | Claude Code marketplace (`Flight-Queue/mcp`) |
| `commands/setup.md` | `/flightqueue:setup` command |
| `.codex-plugin/plugin.json` | Codex plugin |
| `skills/<name>/SKILL.md` | Agent skills |
| `server.json` | Official MCP registry manifest |
| `gemini-extension.json` | Gemini CLI extensions gallery |

## Links

- Docs: [mcp.flightqueue.com](https://mcp.flightqueue.com)
- Website: [flightqueue.com](https://flightqueue.com)
- Pricing: [flightqueue.com/pricing](https://flightqueue.com/pricing)
- Privacy: [flightqueue.com/privacy](https://flightqueue.com/privacy)
- Support: support@flightqueue.com

## Licence

MIT. Copyright Merchant Software Solutions Limited, company number 14098922, registered
in England and Wales.
