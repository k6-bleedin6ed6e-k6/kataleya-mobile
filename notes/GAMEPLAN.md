# Kataleya — Gameplan & Handover

*Written 2026-09-08. For Bonesaw, working across this chat (design/strategy) and Claude Code
(implementation). Grounded in the actual repos (`kataleya-main.zip`, `kataleya-mobile-main.zip`),
not the summaries in their own docs — a few things below correct what `ROADMAP.md` and the
READMEs undersell.*

## Read this first if you're Claude Code

- `kataleya-mobile`'s `AGENTS.md` says it plainly: **Expo has changed since your training data.**
  Read `https://docs.expo.dev/versions/v57.0.0/` before writing any Expo/RN code in that repo.
  Don't rely on memorized Expo APIs.
- The web app (`kataleya-main`) is **not** the toy the README implies. `index.html` is 3,311
  lines, single-file, no build step, vanilla JS/CSS. It has ~9 real screens (`room`, `bridge`,
  `cover`/cocoon, `terminal`, `pairing`, `burn`, `scars`, `vault`, `settings`, `mirror`), a
  phase-based color system keyed to time of day, canvas animations (ouroboros, EKG), a
  typewriter-driven PIN-gated log, and real E2E crypto (X25519 + XChaCha20-Poly1305, vendored in
  `vendor/kataleya-crypto.bundle.js`, not CDN-loaded — this is deliberate, don't "fix" it by
  switching to a CDN import). Read the whole file before touching it; a lot of behavior lives in
  inline event handlers and a shared `state` object, not in separated modules.
- `kataleya-mobile` is genuinely Phase 0 — `App.js`/`index.js` are the stock Expo blank template.
  No feature code exists yet. Nothing here is a partial port to finish; it's a fresh build against
  the web app as reference.
- Package name is decided and set: `studio.kontor.kataleya` (Android-only scope for now, by
  Bonesaw's call — no iOS bundle ID needed yet).

## Decision — resolved 2026-09-08

> **Kataleya is personal-first.** It's an instrument Bonesaw uses himself. The Play Store /
> generic-audience framing is secondary, not the driver.

This resolves the open question from the first draft of this doc. Consequence for direction: the
current app's voice — the terminal calling itself `the garden's vitals`, the cocoon's hold-to-
return ring, re-entry detection, `engine_room`, hour-scars — is **not** a barrier to smooth away
for strangers. It's the actual point. Reimagining means going deeper into that instrument for an
audience of one, not adding onboarding-for-strangers translation layers. Don't genericize copy or
metaphors in the rebuild without Bonesaw explicitly asking for that.

Practical effect on sequencing: Track B's stranger-facing paperwork (content rating answers, Data
Safety form language, generic store descriptions) stays real but drops in priority relative to
Track A. It's fine for those Play Store items to sit half-finished for a while — nothing about
personal-first blocks eventually shipping, it just means the reimagined experience for Bonesaw
comes first, store polish comes after it's right.

## Track A — Reimagined build (web + mobile), personal-first

Feature set and full visual direction are still to be designed in detail (that's ongoing design
conversation, not a Claude Code task) — but the frame is now set, so here's a real phase plan
Claude Code can start sequencing against as design decisions land, rather than a placeholder.

**Phase 0 — keep, don't discard.** Before any rebuild code: archive/tag the current `index.html`
as-is (e.g. a `legacy-web` branch or tag) so nothing in the existing ritual — ouroboros animation,
whisper cycling, phase color system, mirror/vessel EKG rendering — is at risk of being lost mid-
refactor. This is a personal instrument with real history in it; treat the existing build as a
reference implementation to draw from, not disposable scaffolding.

**Phase 1 — architecture decision, made concrete.** Design brief: *clean, inevitable, just works,
self-healing, useful, alive — an elegant, inevitable living/breathing organism.* Concretely for
architecture, "self-healing" is the load-bearing word here: fewer things that can stay broken, no
dependency chain to snap, no build step to fail, no app-store review gate between a fix and the
phone in Bonesaw's hand. The current web app already has this almost by accident — single file,
no build step, service worker just re-caches on next load. That property is not a limitation to
graduate out of; it **is** the self-healing mechanism. Default position: don't replace the web
core with a native rebuild — layer the minimum native surface on top of it that actually earns its
complexity. Push notifications for the pairing "light arrived" case is the one candidate raised so
far worth that trade (native has a real, mobile-only capability there — background delivery the
current web PWA can't do). Everything else defaults to staying in the self-healing web core unless
a concrete capability gap forces the trade, not because native "feels more like an app."
`ROADMAP.md`'s old non-goal (no shared codebase between web and mobile) stays the default too —
don't reach for React Native Web / Expo Router web target just to avoid writing two
implementations; that abstraction layer is exactly the kind of thing that erodes the self-healing
property for the sake of not repeating yourself.

**Phase 2 — room, reimagined first.** Same design brief applied to the room screen specifically:

- *Alive* / *inevitable* — the room's state should keep moving while the app is closed, not only
  compute fresh the instant it's opened. Right now growth stages, mood trends, and the whisper
  cycle are all rendered live off stored data; "alive" means the room reflects what happened while
  Bonesaw was away without being asked — an organism that grew or wilted unwatched, not one that
  only changes when tended.
- *Inevitable* also means killing the four discrete phase buckets (`choice`/`desire`/
  `still-pine`/`nyx`) as hard-cut states. Interpolate continuously — color, whisper tone, orb
  behavior all drifting against real time rather than snapping at phase boundaries.
- *Just works* / *elegant* — audit `screen-settings` hard. Every toggle or confirmation dialog
  that isn't PIN/security-critical is friction the organism metaphor doesn't survive; anything the
  app can already infer from stored state shouldn't be a question to the user.
- *Useful* is the guardrail against the metaphor eating the function — the real test for every
  change in this phase: does it make a bad moment (2am, mid-craving) easier, or does it just make
  a good moment prettier. Beauty that doesn't clear that bar gets deprioritized.
- Room should draw on the mirror/vault/scars data that already exists rather than that logic
  living only in the separate butterfly-overlay bolt-on — a rough week should show up as weight in
  the room itself, not just in a dedicated overlay screen.

**Phase 3 — the ritual screens.** Bridge, cocoon, terminal, mirror — the screens that give the
app its distinct voice. Carry forward specific mechanics worth keeping deliberately (re-entry
detection, hold-to-return, the PIN-gated typewriter log) rather than rebuilding from a blank
description of what they do.

**Phase 4 — vault + scars/burn.** Journal, mood-trend visualization, growth stages tied to
`daysSince()`. Local-first storage decision for mobile (SQLite via `expo-sqlite` was the prior
candidate — revisit once Phase 1's architecture call is made, since it may change).

**Phase 5 — pairing.** E2E sponsor/sponsee signal, ported last as before — most backend-dependent
piece, least central to a personal-first instrument (still real and worth keeping, just not
first).

**Phase 6 — polish + store packaging.** Only once Phases 1–5 feel right for actual daily use —
this is where Track B's remaining paperwork (descriptions, content rating, screenshots) picks
back up.

**Next action for this track:** continue the design conversation (visual direction, what changes
vs. what's carried forward from the reference implementation) before Claude Code starts Phase 1.

## Track B — Store readiness (unblocked, safe to parallelize)

From `PLAY-STORE-SHIPPING.md`, still accurate as of 2026-09-07:

- [x] Package name decided — `studio.kontor.kataleya`
- [x] Android-only scope confirmed
- [x] `npm install -g eas-cli` (24.3.0) + `eas login` (`b6ones6aw`) + `eas init` — project linked,
      real projectId in `app.json` (`ea0add28-a2af-4a15-8dde-8fa6f5cac030`) — **2026-09-14, M1 done**
- [x] `npm install` — 464 packages, real `node_modules` now exists (was never installed before)
- [~] Publish `privacy-policy.md` at a real public URL — page built and content-complete
      (`kontor-studio/privacy.html`), root cause of the dead deploy pipeline found (`arc deploy`
      was looking up a token named "arc-deploy", deleted from the CF account back on 2026-07-30 —
      the real live token was sitting right there as "arc-deploy-workers" the whole time, `arc`
      fixed to use the right name). **Genuinely blocked on one thing now**: that token is
      Workers-scoped only, no Cloudflare Pages permission — needs Bonesaw to add
      "Cloudflare Pages — Edit" to it (or a new token) in the CF dashboard. One command away
      from live once that's done.
- [x] Fill remaining bracketed placeholders in `privacy-policy.md` — retention window pulled from
      the real deployed relay worker's own source (`RELAY_TTL_SECONDS=86400` / 24h for messages,
      `SUBS_TTL_SECONDS=7776000` / 90d for push-subscription records, not guessed), contact method
      confirmed by Bonesaw (his email). Only the Android permissions section stays a placeholder,
      honestly — can't be known until Phase 1+ code exists.
- [ ] Draft short (≤80 char) + full (≤4000 char) Play Store descriptions
- [x] Verify existing icon/adaptive-icon assets meet current Play spec — re-verified live
      2026-09-14, matches the 2026-09-08 finding exactly (1024×1024 RGB icon, 512×512 RGBA
      adaptive layers, 432×432 RGBA monochrome)
- [x] `eas.json` build profiles — confirmed present and valid against current EAS CLI (24.3.0)

**Caution:** several of these (store description copy, "what this app does" framing, content
rating answers) are downstream of the open decision above — a personal-instrument reimagining may
describe itself very differently to Play Store reviewers than the current product-framed copy
does. Fine to do the purely mechanical items (EAS login, icon spec check, deploy pipeline fix)
without waiting; hold off on copy/description drafting until direction is clearer.

## Suggested split while working across both of us

- **This chat (Claude):** design direction, feature/scope decisions, copy and voice work, privacy
  policy language, anything that needs Bonesaw's judgment surfaced and discussed before code gets
  written.
- **Claude Code:** implementation once direction is set — Expo/RN code (checking v57 docs per
  `AGENTS.md`), the `kontor-studio` deploy pipeline fix, EAS setup, and any Track B mechanical
  work that doesn't require a copy/scope decision first.

## Immediate next actions (no blockers, start now)

1. Archive/tag the current `kataleya-main` `index.html` as a reference build before any rebuild
   touches it (Phase 0 above) — Claude Code, do this first, mechanical.
2. Fix `kontor-studio`'s dead `arc deploy` Cloudflare token, or stand up an alternate host for the
   privacy policy — Claude Code, mechanical, no design input needed, can run in parallel.
3. `npm install -g eas-cli` + `eas login` on the dev machine — Claude Code.
4. Verify icon/adaptive-icon assets against current Play spec — Claude Code.
5. Continue the design conversation in chat — visual direction and what's carried forward vs.
   reimagined in Phase 2's room screen — before Claude Code starts Phase 1's architecture work.
