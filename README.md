# musecases

[![Live catalog](https://img.shields.io/badge/live-interactive%20catalog-blue)](https://amanthakkar.github.io/musecases/) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
**Muse, put to work.** A working showcase of [Muse](https://muse.ai) gadget skills I've
written — each one dogfooded on my own hardware first, then contributed upstream to Meta's
[muse-gadget-sdk](https://github.com/facebookincubator/muse-gadget-sdk) catalog.

Interactive catalog: **https://amanthakkar.github.io/musecases/**

## The skills

| Skill | What it does | Upstream status |
|---|---|---|
| [gadget-tesla](skills/gadget-tesla/SKILL.md) | Tesla status, drive/charge history, and efficiency analytics from a self-hosted TeslaMate + Fleet Telemetry stack. Read-only by default; commands only on explicit request via the owner's own command path. The catalog's **first vehicle skill**. | [PR #222](https://github.com/facebookincubator/muse-gadget-sdk/pull/222) — open, CLA signed |
| [gadget-google-nest-thermostats](skills/gadget-google-nest-thermostats/SKILL.md) | Read and adjust Nest thermostats via Google's Smart Device Management API through a self-hosted bridge (e.g. Home Assistant). The catalog's **first thermostat skill**. | [PR #222](https://github.com/facebookincubator/muse-gadget-sdk/pull/222) — open, CLA signed |
| [gadget-bambu-lab-printers](skills/gadget-bambu-lab-printers/SKILL.md) | Bambu Lab printer status incl. AMS filament via cloud/LAN; LAN print dispatch only where the owner enabled Developer Mode — with the trade-offs stated *before* anyone flips it. | [PR #224](https://github.com/facebookincubator/muse-gadget-sdk/pull/224) — open, CLA signed |
| [gadget-home-assistant-gateways](skills/gadget-home-assistant-gateways/SKILL.md) | Read state — and, only on explicit request, call services — on the entities an existing Home Assistant install already exposes, scoped to an owner-approved allowlist. The upstream catalog's Gateways section (Zigbee2MQTT, ESPHome, ratgdo…) has **no Home Assistant skill**; this is the draft of the missing one, written against a production HA that already bridges this home's Nest thermostats and Tesla presence. | **In the lab** — drafted here first; PR planned once #222/#224 land, so the upstream queue isn't flooded |
| [gadget-jellyfin-servers](skills/gadget-jellyfin-servers/SKILL.md) | See what is playing on — search the library of — and, only on explicit request, control a controllable client of an existing self-hosted Jellyfin media server, scoped to a dedicated limited user. The upstream catalog has **no media-server skill** (its nearest neighbours are single-device players and the Lyrion/Squeezebox server skill); this drafts the missing one, written against a production Jellyfin on a home Pi. | **In the lab** — drafted here first; PR planned once #222/#224 land, so the upstream queue isn't flooded |
| [gadget-paperless-servers](skills/gadget-paperless-servers/SKILL.md) | Search — and, only when the question needs it, read — the documents in an existing self-hosted Paperless-ngx archive: full-text search over OCR'd content, filtered by correspondent, tag, type, and date, scoped to a dedicated limited user. The upstream catalog has **no document-archive skill**; this drafts the missing one, written against a production Paperless on a home Pi. Document content is treated as sensitive household data: identifiers redacted in summaries, downloads/uploads only on explicit request, deletes never. | **In the lab** — drafted here first; PR planned once #222/#224 land, so the upstream queue isn't flooded |

Context that makes these fun: filed on 16 Oct 2026, **#222 and #224 were the first
community skill PRs in the catalog's queue** — no community member had contributed a skill
before them. A second contributor joined the queue on 19 Oct (#251, Ecowitt gateways).
The catalog's first community skill to actually *merge* was a later arrival — #123
(Roku TVs and players, by @gurselm), merged 27 Oct 2026 — so #222/#224's claim is
*first filed*, not *first merged*, and this page says so plainly.
Status (28 Oct 2026): #222 and #224 open, CLA signed, checks green. The maintainer
began moving on the skills queue on 27 Oct — merging #123 and syncing #222's branch
with main the same evening — so a human review pass has started, though #222/#224
themselves still await review.

## Design principles (visible in every skill)

- **Read-only by default.** Writes and commands are gated, explicit, and reversible where possible.
- **Guided setup checks, not guessing.** If a layer is missing (no bridge, no telemetry), the
  skill walks the user through *that layer's* setup instead of hallucinating a path.
- **Honest limits, up front.** Closed ecosystems say so in the skill itself — e.g. Bambu's cloud
  dispatch is unavailable to third parties, and Developer Mode kills Bambu Handy while it's on.
  The skill states the trade before the user decides; it never enables risky modes on their behalf.
- **Built on real stacks.** Tesla via my own TeslaMate + Fleet Telemetry install, Nest via my
  Home Assistant, Bambu via my own P2S. If it isn't true on my hardware, it isn't in the skill.

## Layout

`skills/<name>/SKILL.md` mirrors the upstream catalog layout exactly, so anything here is one
copy away from a PR. The files in this repo track my contribution branches; once a PR merges,
upstream becomes the source of truth and this repo becomes the showcase + lab for what's next.

## What's next

The Home Assistant gateway skill, the Jellyfin media-server skill, and the
Paperless-ngx document-archive skill above are the current lab projects. Beyond
them, more of my homelab stack is already Muse-shaped — the natural follow-on
is a skill for the rest of it (home energy), contributed the same way:
dogfood, document the limits honestly, upstream.

---

## Part of a personal-Muse ecosystem

Built and run by [@amanthakkar](https://github.com/amanthakkar):

- **[muse-mailbox](https://github.com/amanthakkar/muse-mailbox)** — E2E-encrypted Muse↔Muse messaging via a blind relay
- **[musebook](https://github.com/amanthakkar/musebook)** — private shared boards for a family's Muses
- **[cortex-showcase](https://github.com/amanthakkar/cortex-showcase)** — the homelab agent stack, as an [interactive architecture tour](https://amanthakkar.github.io/cortex-showcase/)
- **[iCloud Photos on Android](https://github.com/amanthakkar/icloud-photos-on-android)** — iCloud Photos for Android, built the fix-the-friction way
- **musecases** ← **you are here**
