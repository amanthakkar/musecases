---
name: gadget-home-assistant-gateways
description: >-
  Read state and, only on explicit request, call services on devices an existing
  Home Assistant installation already exposes, using Muse Home Link. Scoped to an
  owner-approved set of entities; read-only by default. Not Home Assistant setup,
  not an automations editor, and not a reason to expose Home Assistant to the internet.
---

# Home Assistant Gateways — Exposed Entities via the Local API

Use this skill when the home already runs Home Assistant (typically on a Raspberry Pi
or home server) and the user asks about — or asks to change — something Home Assistant
already knows about: climate, lights, presence, covers, media, or the sensors other
integrations publish there. Home Assistant is the gateway here: many ecosystems
(Tesla presence, Nest thermostats, Zigbee devices) surface as entities in one place,
and this skill reads that one place instead of re-integrating each ecosystem.

## Identify the Setup

- Confirm the Home Assistant base URL on the home network and that it answers
  (`GET /api/`). A reachable dashboard login page alone does not prove API access.
- Inventory before answering: `GET /api/states` for the entity list. Devices are
  `entity_id`s (`climate.living_room`, `binary_sensor.front_door`); friendly names and
  rooms rarely match the user's words out of the box — confirm the mapping with the
  user for anything ambiguous, and remember the agreed mapping for the session.
- Agree an **allowlist** with the user: which entities Muse may read, and the much
  smaller set it may ever act on. Broker/dashboard access is not permission to touch
  every entity in the house.

## Guided Setup Check

If Home Assistant or API access is missing, stop and give the user these steps — do
not install Home Assistant, create accounts, or mint tokens on their behalf.

1. **Home Assistant installed and owned.** The owner runs Home Assistant (OS,
   Container, or Core) on their hardware and can sign in as an administrator.
2. **Long-lived access token.** In Home Assistant: profile → Security → Long-lived
   access tokens → Create token. The owner creates and stores it; the token grants
   their full account's API rights, so it is a credential, not a setting — never
   paste it into chat, a skill file, or a repo.
3. **Stay local.** Reach Home Assistant over the home network via Home Link. Do not
   ask the user to port-forward or publish Home Assistant to the internet for this
   skill; a remote-access product they already run (e.g. their own VPN) is their
   choice, not a setup step here.
4. **Name the entities.** List entities with the user, match each one they care
   about to its room/device, and write down the read/action allowlist from
   *Identify the Setup* before the first real question.

## Prerequisites

Read and follow the Home Link networking and safety rules in `~/docs/devices/home_link.md`.

- The guided setup check passes: base URL reachable, owner-created long-lived token
  available to the Home Link path, entity mapping confirmed.
- Know the semantics before interpreting state: every entity carries `state`,
  `attributes`, `last_changed` (state value last changed) and `last_updated` (any
  attribute last updated). `unavailable` and `unknown` are real states integrations
  publish when a device drops off — they are answers, not errors to paper over.

## Workflow

1. **Status questions:** read `GET /api/states/<entity_id>` for the targets (or the
   full `/api/states` when the question is house-wide, e.g. "is anything on?").
   Report the value, the room/device it maps to, and `last_changed` so the user can
   judge freshness.
2. **History questions:** use `GET /api/history/period/<timestamp>` filtered to the
   relevant entities instead of guessing trends from a single read.
3. **Changes — only on explicit request, only for allowlisted entities:** call the
   matching service with `POST /api/services/<domain>/<service>` and the target
   `entity_id` (e.g. `climate/set_temperature`, `light/turn_on`). One service call
   per requested change; never batch extra devices in "while you're at it."
4. **Safety-relevant domains get a spoken pause:** before `lock`, `cover`,
   `alarm_control_panel`, or `climate` changes that affect a whole home, restate
   exactly what will change and confirm — these move physical things and other
   people in the house live with the result.
5. **Never edit the installation:** no changes to automations, scripts (beyond
   running an existing, allowlisted one the user names), scenes, integrations,
   dashboards, or configuration from a skill session. If the fix is "edit the
   automation," say so and let the owner do it in the UI.

## Verify the Result

- A `200` from a service call is not proof: service calls return the states they
  *touched*, and devices apply changes on their own schedule. Re-read the entity
  after a short wait and report the achieved state, not the requested one.
- If the state reads `unavailable`/`unknown`, or `last_updated` predates the call,
  say the change is unconfirmed, give the timestamps, and do not repeat the call in
  a loop — a sleeping or dropped device will not be fixed by retries.
- For covers, locks, and climate, distinguish requested target from achieved state
  exactly as read back ("set to 72°, currently reading 74° and cooling").

## Limits

- Read-only by default; the action allowlist is short, explicit, and set with the
  user in advance. Anything outside it is a read, or a "here's how you'd do it in
  the UI," never a service call.
- No token handling beyond using the owner-provided token through the Home Link
  path; no creating users, tokens, or API keys; no exposing Home Assistant beyond
  the local network.
- Entity history is household data — who was home, when doors opened. Report what
  the user asked for, nothing more, and never for a household member who isn't
  the user asking.
- Presence and sensor state are not safety guarantees: never present Home
  Assistant state as proof a home is empty, secure, or safe.

## Sources

- [Home Assistant REST API](https://developers.home-assistant.io/docs/api/rest/)
- [Home Assistant authentication (long-lived access tokens)](https://developers.home-assistant.io/docs/auth_api/)
- [Home Assistant core entity state model](https://developers.home-assistant.io/docs/core/entity/)
