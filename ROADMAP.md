# Kataleya Mobile — Roadmap

This is the real, intended version of Kataleya: a cross-platform recovery-companion app, built
properly this time. Not a demo, not a web port with a wrapper around it — the actual thing.

**Status: scaffolding only.** This repo is a fresh Expo/React Native starter (`blank` template),
nothing built yet. This document is the plan, not a spec to freeze — revisit and revise it as
work actually starts.

## Why this exists

The original mobile build (Expo/React Native, 2026 early) never got past onboarding reliably and
was shelved in favor of a web-first rebuild. That web version — live at
`kontor.studio/kataleya-demo/`, source at `github.com/kwasikontor45/kataleya` — turned out to be
the right move for *validating the product*: every feature below is already built, tested, and in
real use there. This repo exists to port a proven design to the platform it was actually meant
for, not to design from a blank page.

## What's already proven (port target, not guesswork)

From the live web app, in priority order:

1. **The room screen** — 24-hour circadian clock, breathing orb, hour-scars for hard moments.
   The emotional core of the app; get this feeling right before anything else.
2. **Guided exercises** — breathing (4-7-8 / box / coherent), 5-4-3-2-1 grounding, urge-surfing.
3. **The vault** — a private, clinician-PIN-protected journal.
4. **Sponsor/sponsee pairing ("send a light")** — real E2E-encrypted signal between two paired
   devices. **Redesign complete** (2026-07-30, real Web Push-based delivery via
   `kataleya-relay-worker`, deployed and verified) — no longer an open blocker, build against the
   current live architecture.

## Phases

**Phase 0 — scaffolding (done):** Expo blank template, this roadmap.

**Phase 1 — room screen + navigation shell.** Get the core screen and basic nav feeling right on
a real device (not just simulator) before building anything else. This was the original app's
first failure point (never got past onboarding reliably) — de-risk it first, not last.

**Phase 2 — guided exercises.** Breathing, grounding, urge-surfing. Self-contained, no backend
dependency, good next target once navigation is solid.

**Phase 3 — the vault.** Local-first storage (equivalent of the web app's localStorage model —
figure out the right on-device persistence choice for React Native: SQLite via `expo-sqlite` is
the likely candidate, matching what plain localStorage does on web). PIN gating ported from web.

**Phase 4 — pairing / send-a-light.** Built against the redesigned architecture (see above), not
before. This is the piece most likely to need real backend/push infrastructure, so it should land
after the redesign decision, not in parallel with it.

**Phase 5 — platform polish.** Push notifications (this is a real, native capability mobile has
that the current web PWA doesn't — likely resolves the "no light arrived" background-delivery
problem the web version currently has), offline support, app store packaging.

## Non-goals, for now

- Not porting the clinician-facing static pitch page (`kataleya-clinician.html`) — that's a
  marketing artifact, not part of the app itself.
- Not building a shared codebase between web and mobile (e.g. React Native Web) unless a real
  need for it shows up later — two independent, platform-appropriate implementations of the same
  proven feature set is a reasonable choice here, not technical debt.

## Reference

- Live web app: https://kontor.studio/kataleya-demo/
- Web source: https://github.com/kwasikontor45/kataleya
- Full project history/decisions: `kontor-studio` repo's `GAMEPLAN.md`
