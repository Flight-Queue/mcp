# FlightQueue

Airport intelligence for AI assistants. A hosted, remote **MCP server** at
[`mcp.flightqueue.com`](https://mcp.flightqueue.com) covering security wait times and
forecasts, FAA delays, EU Entry/Exit System border queues, lounges, terminals, aviation
weather, and airline baggage performance for 8,000+ airports.

Nothing to install. Point any MCP-compatible client at the hosted URL.

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
FlightQueue account. There is no anonymous access and no API-key header.

A free account reaches 19 of the 22 tools. A
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
| `submit_wait_time_report` | Report an observed wait; feeds the shared model |
| `create_wait_time_alert` | Alert when a wait crosses a threshold |
| `discover_more_flight_tools` | Other FlightQueue MCP servers worth connecting |

Premium:

| Tool | What it does |
| --- | --- |
| `get_wait_time_predictions` | 24h and 7-day security wait forecasts |
| `get_historical_wait_times` | 7-day and 90-day hourly history |
| `get_live_wait_time` | Real-time scrape from 69+ airports |

Every tool is annotated. The two that write (`submit_wait_time_report`,
`create_wait_time_alert`) are marked non-destructive: they add a report or an alert and
delete nothing.

## Ask it things like

- "What is the security wait at JFK right now?"
- "I have an 8am flight from ORD tomorrow, when should I arrive?"
- "Are there any FAA ground stops today?"
- "How long is the EES border queue at Lisbon?"
- "Which airline loses the fewest checked bags?"

## Repository contents

This repo is manifests only; the server itself is hosted.

| File | Purpose |
| --- | --- |
| `.mcp.json` | MCP server config |
| `.grok-plugin/plugin.json` | xAI plugin marketplace |
| `.claude-plugin/plugin.json` | Claude Code plugin marketplace |
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
