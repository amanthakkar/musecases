# musecases

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

Context that makes these fun: as of October 2026, **no community member had contributed a
skill to the catalog yet** — #222 and #224 are the only skill PRs in the queue. If they merge,
these are the first community-contributed skills in muse-gadget-sdk.

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

More of my homelab stack is already Muse-shaped — the natural follow-ons are skills for the
rest of it (media, documents, home energy), contributed the same way: dogfood, document the
limits honestly, upstream.

---

## Part of a personal-Muse ecosystem

Built and run by [@amanthakkar](https://github.com/amanthakkar):

- **[muse-mailbox](https://github.com/amanthakkar/muse-mailbox)** — E2E-encrypted Muse↔Muse messaging via a blind relay
- **[musebook](https://github.com/amanthakkar/musebook)** — private shared boards for a family's Muses
- **[cortex-showcase](https://github.com/amanthakkar/cortex-showcase)** — the homelab agent stack, as an [interactive architecture tour](https://amanthakkar.github.io/cortex-showcase/)
- **[iCloud Photos on Android](https://github.com/amanthakkar/icloud-photos-on-android)** — iCloud Photos for Android, built the fix-the-friction way
- **musecases** ← **you are here**
