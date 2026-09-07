# Kataleya — Privacy Policy

*Draft, 2026-09-07 — ready to publish once deployed to a public URL. Not yet live anywhere.*

**Last updated:** [date of actual publish]

## The short version

Kataleya is built so that almost nothing about your recovery is ever seen by anyone but you.
Your room, your exercises, and your private journal all live only on your own device. We do not
run a server that stores, reads, or has access to that data. The one exception — the optional
sponsor/sponsee "send a light" feature — is described in detail below, and even there, we only
ever see encrypted data we cannot read.

## What stays on your device, always

- Your room screen state, breathing/grounding/urge-surfing exercise history, and your private
  journal entries are stored locally on your device only.
- None of this is transmitted to us, to any server we operate, or to any third party. There is no
  account, no login, and no backend database holding this information.
- If you uninstall the app or clear its data, this information is gone — we don't hold a copy,
  because we never had one.

## The one feature that does use the network: "send a light"

If you choose to pair with a sponsor or sponsee, the app sends a small encrypted signal between
your two devices so a "light" can arrive even when both apps aren't open at the same moment.

- The message itself is end-to-end encrypted on your device before it ever leaves it, using
  standard, published cryptography (X25519 key exchange + XChaCha20-Poly1305 encryption). Only
  the paired device's key can decrypt it.
- We operate a small relay service (a Cloudflare Worker) that helps deliver this encrypted
  message and enables push notifications. This relay only ever handles already-encrypted
  ciphertext — it cannot read the content of what's sent, and does not need to.
- The relay retains delivery data only as long as needed for delivery (recent-history window),
  then it's cleared. [Confirm and state the exact retention window before publishing.]
- If you never use the pairing feature, none of this applies to you at all.

## What we don't do

- We don't collect analytics, usage tracking, or advertising identifiers.
- We don't sell or share data, because we don't have data to sell or share in the first place.
- We don't require an account, email address, or any personal information to use the app.

## Permissions this app requests (Android)

[Fill in once Phase 1+ is built and the actual permission list is known — e.g. push notification
permission for the pairing feature's "light arrived" alerts. Keep this section accurate to
exactly what the shipped app requests, nothing more.]

## Children's privacy

Kataleya is intended for adults in recovery and is not directed at children. We do not knowingly
collect data from children, consistent with the "we don't collect data" position above.

## Changes to this policy

If this policy changes, the "Last updated" date at the top will change, and material changes will
be described in the app's own release notes.

## Contact

[Add a real contact — email or a contact page URL — required by Play Store's Data Safety form
and generally expected on any published privacy policy.]

---

## Notes for whoever publishes this (not part of the public policy text)

- **Not yet live.** Needs a real public URL — the earlier check this session confirmed
  `kontor.studio/privacy` doesn't actually exist (SPA catch-all serving the homepage for any
  path). Either fix that route in the `kontor-studio` repo, or publish this at a dedicated path
  (e.g. `kontor.studio/kataleya-privacy`) once the deploy pipeline works — `arc deploy`'s
  Cloudflare token is documented as dead as of 2026-08-22, that needs fixing first or a manual
  Cloudflare Pages upload used instead.
- Bracketed `[...]` placeholders above need real answers before this goes live — the relay's
  exact data-retention window, the final permissions list (can't be known until the app is
  actually built), and a real contact method.
- This describes the architecture as designed per `ROADMAP.md`/`kataleya`'s README — re-verify
  against the actual shipped app before publishing, don't publish this from intent alone.
