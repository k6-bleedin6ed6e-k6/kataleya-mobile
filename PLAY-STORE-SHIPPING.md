# Kataleya — Play Store Shipping Plan

Companion to `ROADMAP.md` (the feature-build phases). This doc is specifically about what it
takes to get a production build actually live on Google Play — milestones, to-dos, and the real
resources/decisions still needed. Written 2026-09-07, grounded in the repo's actual current state
(checked live, not assumed):

- `kataleya-mobile`: Expo SDK 57.0.9, React Native 0.86.2, React 19.2.3. **Phase 0 only** — a
  single "Initial scaffold" commit, blank template, no feature code written yet.
- No `android.package` / `ios.bundleIdentifier` set in `app.json` yet — required before an
  installable build exists, and **permanent once the first Play Store upload happens**.
- No `eas.json` — no EAS build profiles configured yet.
- **No privacy policy exists anywhere** — checked `kontor.studio/privacy` directly; it 200s but
  is just the homepage's SPA catch-all, not a real page. Play Store requires a public privacy
  policy URL for every app, no exceptions, and this app handles genuinely sensitive data
  (recovery journal, sponsor pairing) — this needs a real, accurate one, not boilerplate.
- Reference/port target: `kataleya` (the web app, live at `kontor.studio/kataleya-demo/`) is
  feature-complete and proven in real use — room screen, guided exercises, PIN-protected vault,
  E2E-encrypted sponsor pairing. This is a port job, not a design problem.

## The key structural insight: two tracks, only one is blocked

**Track A — app build** (`ROADMAP.md`'s Phases 1–5: room screen, exercises, vault, pairing,
polish) genuinely needs code written, phase by phase.

**Track B — store readiness** (identity, policy, listing assets, account setup) does **not**
depend on the app being feature-complete, and — critically — **does not depend on Google's ID
verification finishing either**, except for the one step of actually creating the app entry in
Play Console. Everything else in Track B can start today. This is the actual answer to "what can
I do while I wait."

## Track B — store readiness checklist

1. **Decide the Android package name / iOS bundle ID.** Permanent the moment the first build is
   uploaded to Play Console — cannot be changed later without publishing as a brand-new app
   listing. Needs your decision, not mine. Suggested default matching the existing brand:
   `studio.kontor.kataleya` (reverse-DNS of `kontor.studio`) — flag if you want something else.
2. **Write and publish the privacy policy.** Real gap, not yet started. Given the app's actual
   architecture, the honest story is genuinely simple and a real selling point: room/exercises/
   vault data never leaves the device; the sponsor-pairing relay only ever touches already-
   encrypted ciphertext, never plaintext or metadata beyond what's needed for delivery. Needs
   its own public URL (e.g. `kontor.studio/kataleya-privacy`) — I can draft this once you confirm
   scope, but it should describe the app as it will actually ship, not aspirationally.
3. **Data Safety form prep** (Play Console's mandatory questionnaire). Same local-first story
   above makes this straightforward — mostly "no data collected," with the pairing feature's
   encrypted-transit case needing its own precise, honest answer.
4. **Content rating questionnaire prep.** Recovery/mental-health support content, no violence/
   gambling/regulated content — should rate low, but needs honest answers about sensitive themes
   (references to addiction/recovery) since Play's questionnaire asks about this directly.
5. **Store listing assets:**
   - Hi-res icon (512×512) + adaptive icon layers — foreground/background/monochrome already
     exist in `assets/`, need to verify they meet current Play spec before reuse.
   - Feature graphic (1024×500) — does not exist, needs real design work.
   - Phone screenshots (Play requires at least 2) — blocked on Phase 1 being real and running on
     an actual device; can't screenshot a blank scaffold.
   - Short description (≤80 chars) + full description (≤4000 chars) — can draft anytime, the
     `kataleya` repo's README is a good starting source of real copy.
6. **EAS build profiles.** Scaffolded as part of this pass (see below) — `development`/
   `preview`/`production` profiles in `eas.json`. Actually building requires an Expo account
   (`eas login`) — confirm whether one already exists or needs creating.
7. **Signing.** Recommend EAS-managed Play App Signing (Google holds the signing key, EAS handles
   the upload key) — no manual keystore management, the standard modern default, nothing to set
   up ahead of time.
8. **Target API level.** Google raises Play's minimum required target API level roughly yearly —
   check the current requirement at actual submission time, not now (Expo SDK 57/RN 0.86 should
   already track a current Android target, but verify against whatever Play requires when you're
   actually ready to submit, since this doc will age).
9. **Once ID verification clears:** create the app entry in Play Console, start with the
   **Internal Testing track** (no review wait, fastest way to get a real build in your own
   hands on a real device) before ever touching Closed/Open/Production tracks.

## Track A — reference only

Full detail lives in `ROADMAP.md`. Quick map for sequencing against the milestones below:

| Phase | Scope |
|---|---|
| 0 | Scaffolding — **done** |
| 1 | Room screen + navigation shell (de-risk first — original app's failure point) |
| 2 | Guided exercises (breathing, grounding, urge-surfing) |
| 3 | The vault (local storage + PIN gating) |
| 4 | Sponsor/sponsee pairing — **check the web-side redesign status first**, don't build against the old architecture |
| 5 | Push notifications, offline polish, app store packaging |

## Milestones (sequenced, mixing both tracks)

- **M1 — Identity + build scaffolding set.** Package name decided, `app.json` updated,
  `eas.json` profiles in place. No blocker — do first.
- **M2 — Privacy policy live.** Real URL, accurate to the shipped architecture. No blocker.
- **M3 — Phase 1 real and running on a physical device.** First actual app code.
- **M4 — First Internal Testing build in Play Console.** Requires ID verification cleared + M1–M3
  done. This is the real "it's in the store" milestone — doesn't need feature parity with web yet.
- **M5 — Phases 2–3 complete**, promoted to Closed Testing with a few real testers.
- **M6 — Phase 4** (pairing), only after confirming the web-side redesign has landed.
- **M7 — Phase 5 polish**, then Production release.

## Immediate to-do (no blockers, start now)

- [ ] Decide package name / bundle ID (see suggestion above)
- [ ] Draft + publish privacy policy at a public URL
- [ ] Set `android.package` + `ios.bundleIdentifier` in `app.json` once decided
- [ ] Confirm Expo/EAS account status (existing login, or needs creating)
- [ ] Draft short + full store description from `kataleya`'s README copy
- [ ] Verify existing icon assets meet current Play spec

## Open decisions / resources needed (not mine to decide silently)

- **Package name / bundle ID** — permanent once set, your call.
- **iOS scope** — this doc and the ask both center on Play Store; confirm whether Apple
  Developer Program enrollment ($99/yr, separate from Google's process) is in scope too, or
  Android-first for now.
- **Feature graphic design** — real design work, not yet started.
- **A physical Android test device** — Phase 1 explicitly needs this; simulator-only testing was
  named in `ROADMAP.md` as the original app's failure mode.
- **Send-a-light redesign status** — only blocks Phase 4, nothing before it.
- **Expo/EAS account** — needed before any real build can be produced or submitted.

## Not a gap — already solved, don't rebuild

- Feature design/UX — proven live on the web app, this is a port job.
- Data-safety story — the local-first architecture already makes this simple to answer honestly.
