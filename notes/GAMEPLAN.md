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

**Phase 1 — architecture decision, made concrete.** The web↔mobile shared-codebase question
(`ROADMAP.md`'s old non-goal) needs an actual answer before either rebuild starts, not just
"open for debate": single-file vanilla JS keeps the web app's zero-build, works-from-a-plain-
offline-file property that's core to its privacy story — evaluate whether Expo Router's web
target or React Native Web can preserve that property before adopting either, don't default to
convenience over that guarantee.

**Phase 2 — room, reimagined first.** Same sequencing logic as the original roadmap (de-risk the
emotionally central screen before anything else), but now against the deepened personal-first
brief rather than a generic port. This is where visual direction gets decided in practice, not
just discussed.

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
- [ ] `npm install -g eas-cli`, then `eas login` — neither done
- [ ] Publish `privacy-policy.md` at a real public URL — blocked on `kontor-studio`'s deploy
      pipeline (`arc deploy`'s Cloudflare token is dead as of 2026-08-22) or an alternate host
- [ ] Fill remaining bracketed placeholders in `privacy-policy.md` (relay retention window, final
      Android permissions list, real contact method) — some can't be finalized until Track A
      code exists; retention window and contact method could be answered now
- [ ] Draft short (≤80 char) + full (≤4000 char) Play Store descriptions
- [ ] Verify existing icon/adaptive-icon assets meet current Play spec
- [ ] `eas.json` build profiles — scaffolded but unverified against current EAS CLI

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
