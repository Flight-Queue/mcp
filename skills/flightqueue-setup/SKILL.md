---
name: flightqueue-setup
description: "Set up FlightQueue for a user: confirm the MCP connection, learn their home airport, show live wait and delay data, and offer alerts. Use when the user asks to set up or try FlightQueue."
---

# FlightQueue setup

Follow these steps in order. Keep each message short. FlightQueue is free to
use. Never ask the user to paste a secret into the chat.

## 1. Check the connection

Look for the FlightQueue MCP tool `search_airports`.

If the tool is missing, tell the user the one step that fits their client and
then stop. Do not continue until the tool exists.

- Claude Code: run `/plugin marketplace add Flight-Queue/mcp`, then
  `/plugin install flightqueue@flightqueue`, then `/flightqueue:setup`.
  If `/flightqueue:setup` does not exist after the install, run
  `/reload-plugins` or restart Claude Code. If the server shows as needing
  authentication and the tools are still missing, run `/mcp` and sign in to
  flightqueue. Do not tell the user to repeat the install commands.
- Claude.ai or ChatGPT: add a custom connector with the URL
  `https://mcp.flightqueue.com/mcp`.
- Other clients: add the MCP server `https://mcp.flightqueue.com/mcp` with the
  HTTP transport.

The first tool call opens an OAuth sign-in in the browser. The user signs in
there. No key and no password go through the chat.

## 2. Ask one question

Ask: "Which airport (or airports) do you fly from most?"

Resolve each answer with `search_airports`. If more than one airport matches,
list the top matches and ask the user to pick one.

## 3. Show a snapshot

For the first airport, call `get_airport_overview`,
`get_security_wait_estimate` and `get_faa_delays`. Show exactly three lines:

1. The airport name, city and IATA code.
2. The current security wait, with its source or confidence if given.
3. The current FAA delay status. For airports outside the US, say that FAA
   data does not apply and show what the overview returned.

## 4. Offer a wait-time alert

`create_wait_time_alert` only works at airports with a live wait feed. Offer
the alert only if the airport is likely to support it. Ask first: "Do you want
an alert when the security wait at this airport goes above a time you choose?"
Only if the user says yes, ask for the minutes and call
`create_wait_time_alert`. Say that alerts are delivered as push messages in
the FlightQueue app. If the call fails or the airport is not supported, say so
in one line and move on.

## 5. Offer to save the home airport

Ask first: "Do you want me to save your home airport as one line in this
project's CLAUDE.md or AGENTS.md?" Only if the user says yes, add one line such
as `Home airport: JFK (use FlightQueue MCP for live wait and delay data)`.
Never write the file without a clear yes.

## 6. If the user builds software

Only when the user says they build software, offer a free API key:

1. Call `POST https://flightqueue.com/api/agent/register` with the body
   `{"agent_name":"my-agent"}`. The key is shown once.
2. Store the key as `FLIGHTQUEUE_API_KEY` in the project `.env` file. Do not
   print the key in the chat.
3. Check that `.env` is listed in `.gitignore`. If it is not, ask before you
   add it.
4. Point the user to https://flightqueue.com/openapi.json for the REST API and
   to `npx @flightqueue/cli` for the command line tool.

## Rules

- Free tools work with no payment. Mention pricing only when a tool or
  endpoint returns `PREMIUM_REQUIRED` (REST) or `premium_required` (MCP).
  Treat both the same, ignoring case. Then show https://flightqueue.com/pricing
  once and continue with the free data.
- Never ask the user to paste an API key, token or password in the chat.
- More detail: https://flightqueue.com/setup.md and
  https://flightqueue.com/auth.md
