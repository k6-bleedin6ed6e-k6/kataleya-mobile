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

1. **Package name — decided.** `studio.kontor.kataleya`, set in `app.json`. **Android-only for
   now** (user's call) — no `ios.bundleIdentifier` needed until/unless iOS scope is revisited.
2. **Privacy policy — drafted, not yet live.** `privacy-policy.md` in this repo. Describes the
   real architecture (room/exercises/vault never leave the device; the sponsor-pairing relay only
   ever touches already-encrypted ciphertext). Has a few bracketed placeholders that can't be
   finalized until the app is actually built (exact relay retention window, final Android
   permissions list, a real contact method) — re-verify against the shipped app before
   publishing, this was written from the design, not from tested code. Needs a real public URL —
   `kontor.studio/privacy` doesn't actually exist (confirmed live: it's the site's SPA catch-all
   serving the homepage for any path), and `kontor-studio`'s own deploy pipeline is documented as
   broken (`arc deploy`'s Cloudflare token is dead) — that needs fixing, or another host, before
   this can go live.
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
6. **EAS build profiles.** Scaffolded — `development`/`preview`/`production` profiles in
   `eas.json`. Checked live: the EAS CLI itself isn't installed on this machine
   (`npm install -g eas-cli`), and no Expo account login exists yet (`eas login`) — real,
   concrete next step, not yet done.
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
| 4 | Sponsor/sponsee pairing — redesign confirmed complete (2026-07-30, real Web Push via `kataleya-relay-worker`), no longer an open blocker |
| 5 | Push notifications, offline polish, app store packaging |

## Milestones (sequenced, mixing both tracks)

- **M1 — Identity + build scaffolding set.** Package name decided, `app.json` updated,
  `eas.json` profiles in place. No blocker — do first.
- **M2 — Privacy policy live.** Real URL, accurate to the shipped architecture. No blocker.
- **M3 — Phase 1 real and running on a physical device.** First actual app code.
- **M4 — First Internal Testing build in Play Console.** Requires ID verification cleared + M1–M3
  done. This is the real "it's in the store" milestone — doesn't need feature parity with web yet.
- **M5 — Phases 2–3 complete**, promoted to Closed Testing with a few real testers.
- **M6 — Phase 4** (pairing) — redesign already confirmed landed, no longer gated on that.
- **M7 — Phase 5 polish**, then Production release.

## Immediate to-do (no blockers, start now)

- [x] Decide package name — `studio.kontor.kataleya`, set in `app.json`
- [x] Confirm scope — Android-only for now
- [ ] Publish the drafted privacy policy at a real public URL (needs the `kontor-studio` deploy
      pipeline fixed, or another host) — content is written, fill in the remaining placeholders
      against the real shipped app first
- [ ] `npm install -g eas-cli`, then `eas login` — neither done yet, checked live
- [ ] Draft short + full store description from `kataleya`'s README copy
- [ ] Verify existing icon assets meet current Play spec

## Open decisions / resources needed (not mine to decide silently)

- **Feature graphic design** — real design work, not yet started. Not blocking — Play only
  requires this at Closed/Open/Production track, not Internal Testing (M4).
- **A physical Android test device** — Phase 1 explicitly needs this; simulator-only testing was
  named in `ROADMAP.md` as the original app's failure mode. Needed before M3, not before now.
- **Expo/EAS account** — needed before any real build can be produced or submitted.

## Where things actually stand (2026-09-07)

Roughly **10% of the way to M4** (first Internal Testing build in Play Console) — most of the
store-readiness paperwork (Track B) is tractable and largely unblocked, but no app code exists
yet (Phase 1 is 0%), which is both the largest remaining chunk of work and the app's
historically riskiest phase. Google's ID verification is an external, timeline-unknown gate on
top of all of this regardless of how ready everything else is.

## Not a gap — already solved, don't rebuild

- Feature design/UX — proven live on the web app, this is a port job.
- Data-safety story — the local-first architecture already makes this simple to answer honestly.
