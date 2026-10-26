---
name: gadget-jellyfin-servers
description: >-
  See what is playing — and search the library of — an existing self-hosted Jellyfin
  media server using Muse Home Link, and, only on explicit request, control a
  controllable client's playback. Read-only by default; viewing history is
  household data. Not Jellyfin setup, not library administration, and not a
  reason to expose the server to the internet.
---

# Jellyfin Servers — Sessions, Library and Playback via the Local API

Use this skill when the home already runs Jellyfin (typically on a Raspberry Pi
or home server) and the user asks what is playing, what is in the library, or
asks to pause / resume / start something on a client that is already signed in.
Jellyfin is the media library of the house in one place: movies, shows, and
music the owner already hosts, plus a live view of every active playback
session. This skill reads that surface instead of scraping a client app.

## Identify the Setup

- Confirm the Jellyfin base URL on the home network (default port 8096) and
  that it answers: `GET /System/Info/Public` returns the server name, id, and
  version without authentication. A reachable web UI alone does not prove API
  access — prove it with an authenticated read before answering questions.
- Inventory before answering: `GET /Sessions` lists active sessions with their
  client, device, user, and `NowPlayingItem`. "The TV" is a *session*, not a
  server setting — match the user's words to a session's client/device name
  with the user when more than one session is active, and remember the agreed
  mapping for the session.
- Agree the scope with the user up front: which Jellyfin user this skill acts
  as, which clients it may ever control, and that library answers stay inside
  that user's library access and parental-control settings.

## Guided Setup Check

If Jellyfin or API access is missing, stop and give the user these steps — do
not install Jellyfin, create users, or mint keys on their behalf.

1. **Jellyfin installed and owned.** The owner runs Jellyfin on their hardware,
   can sign in to the Dashboard as an administrator, and the libraries and
   users already exist.
2. **A dedicated, limited user — not the admin.** Create (in the Dashboard) or
   pick a non-admin user for this skill, with library access scoped to what
   the user wants Muse to see. Playback control of other people's sessions
   additionally requires that user's *Allow remote control of other users* /
   *Allow remote control of shared devices* permissions — Jellyfin user
   settings, off by default; the owner turns them on deliberately, or control
   stays limited to that user's own sessions.
3. **Credential, owner-created.** Either an API key from Dashboard → API Keys
   or that limited user's access token. Be honest about the difference: a
   Jellyfin **API key is server-wide and effectively administrative**, so the
   limited user's token is the safer default. Either way the credential is a
   secret — never paste it into chat, a skill file, or a repo.
4. **Stay local.** Reach Jellyfin over the home network via Home Link. Do not
   ask the user to port-forward or publish Jellyfin to the internet for this
   skill; remote access they already run is their choice, not a setup step.

## Prerequisites

Read and follow the Home Link networking and safety rules in `~/docs/devices/home_link.md`.

- The guided setup check passes: base URL reachable, authenticated read works
  (`GET /Sessions` returns 200), session naming confirmed with the user.
- Know the units before interpreting progress: Jellyfin reports times in
  **ticks** (10,000,000 per second) — `RunTimeTicks` on the item,
  `PlayState.PositionTicks` on the session. Convert before quoting a position
  or runtime; a raw tick count is not a duration anyone can use.

## Workflow

1. **"What's playing?"** — read `GET /Sessions`; report each active session's
   user, client/device, `NowPlayingItem` title, and position vs. runtime from
   `PlayState` (converted from ticks). No active session is an answer, not an
   error.
2. **Library questions** ("do we have X?", "what haven't we finished?") —
   query as the scoped user: `GET /Users/<userId>/Items` (filters, search
   terms, `IsPlayed` / resume state) so answers inherit that user's library
   access. Answer from the server's metadata, not from memory of what a
   library "usually" contains.
3. **Playback control — only on explicit request, only for agreed clients:**
   `POST /Sessions/<sessionId>/Playing/Pause`, `…/Unpause`, `…/Stop`,
   `…/NextTrack`, `…/PreviousTrack`, or start an item with
   `POST /Sessions/<sessionId>/Playing/Play`. Confirm the target session by
   its current `NowPlayingItem` immediately before sending a command — session
   lists are live, and the living-room session from an hour ago may be a
   different device now.
4. **Only controllable sessions can be controlled.** Filter with
   `GET /Sessions?ControllableByUserId=<userId>` when in doubt; a client that
   does not support remote control (some DLNA and third-party players) can be
   *seen* playing but not driven. Say so instead of retrying commands at it.
5. **Never administer the server:** no item deletes, no library scans or
   metadata edits, no user or configuration changes from a skill session. If
   the fix is "change it in the Dashboard," say so and let the owner do it.

## Verify the Result

- A command returning success is not proof the client acted: re-read the
  session and report the achieved state — `PlayState.IsPaused`, the new
  `NowPlayingItem`, the position — not the requested one.
- If the session has vanished on re-read, the client closed or signed out;
  report the command as unconfirmed and do not replay it at a different
  session that happens to match the name.
- Progress answers carry their read time: a session's position advances in
  real time, so quote it as "about 41 minutes in, as of this read."

## Limits

- Read-only by default; playback control is explicit, per-request, and scoped
  to the agreed clients. Starting playback is the loudest action this skill
  has — it makes noise in a room other people may be in, and on a small
  server it can start a transcode that competes with everything else the
  machine does. Name the client and the item before starting anything.
- **Viewing history is household data.** Who watched what, and when, says as
  much about a household as presence history does. Report the asking user's
  own activity; another member's watching is theirs to share, not this
  skill's to narrate.
- No credential handling beyond using the owner-provided key/token through the
  Home Link path; no creating users, keys, or sessions on anyone's behalf; no
  exposing the server beyond the local network.
- Not every client is controllable and not every file plays everywhere:
  direct-play vs. transcode is the client's and server's decision — if an
  item will not start, report the server's answer rather than forcing
  settings changes.

## Sources

- [Jellyfin — managing users (remote-control permissions)](https://jellyfin.org/docs/general/server/users/adding-managing-users/)
- [Jellyfin — quick start (server and Dashboard basics)](https://jellyfin.org/docs/general/quick-start/)
- [openHAB Jellyfin binding (community integration documenting /Sessions and Dashboard API keys)](https://www.openhab.org/addons/bindings/jellyfin/)
