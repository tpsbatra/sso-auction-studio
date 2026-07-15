# Session Handover — SSO Auction Studio

**Project:** Self-built live player-auction + broadcast-overlay tool for OBS
**Owner:** SSO Consultants (OPC) Pvt. Ltd. / DataOwl
**Status:** Tier 1 built, tested, and shippable. Tier 2 (Firebase) not started.
**Working mode:** Plan-first, no build without explicit approval. (Approval was given this session.)

---

## 1. Why this exists (motive)

Primary purpose is **pedagogical** — to demonstrate to students that tools like
KeepTheScore / BidAthlete can be built in-house. It is **not** positioned as a
market product competing on features. Target use is **live streaming of sports
games and auctions** (IPL-style auctions first). OwlQuiz integration is planned
for later, on the same realtime pattern.

> Competitive context (established this session): both the scoreboard space
> (KeepTheScore owns it) and the sports-auction space (BidAthlete + ThePlayerAuction,
> MyAuctionVerse, AuctionSpot, SportinU — India-heavy) are **mature and crowded**.
> So the value here is teaching + owned ecosystem + eventual OwlQuiz crossover,
> NOT out-featuring incumbents.

---

## 2. Architecture (the through-line)

Same pattern as OwlQuiz: **one control surface writes → shared state → many views read.**

- **Control** view = the only writer (auctioneer).
- **Overlay** view = transparent page for OBS browser source (read-only).
- **Team** views = one per franchise, private, read-only.
- **Setup** view = admin editor for teams/players/rules/branding.

State lives behind one small `Store` object. **Tier 1** = `localStorage` + the
`storage` event (syncs across tabs on one machine). **Tier 2** = swap that one
layer for Firebase Realtime DB (the OwlQuiz project); nothing else changes.

---

## 3. What was built (Tier 1)

Single self-contained `index.html` (no build step, no backend) + `README.md`.

**Views** (chosen by URL): `?view=control` (default), `?view=overlay`,
`?view=team&id=<CODE>`, `?view=setup`.

**Auction mechanics:** category/marquee sets, base prices, increment ladder
(<₹1Cr +10L, <₹2Cr +20L, ≥₹2Cr +25L), live purse deduction, squad max +
overseas cap enforcement, sold/unsold, unsold re-entry at end of pass,
end-of-auction standings.

**Control screen:** current-lot card, tap-a-team-to-bid grid (with
eligibility reasons), Sold / Unsold / Undo / Next, live team-purse table,
overlay layout switch + show/hide, QR grid per team, auction log, reset.

**Demo auto-pilot:** "Auto-run" makes teams auto-bid (valuation-based) so the
whole auction plays itself; speed slider; "Simulate one bid" for step-by-step.

**Overlay (OBS):** transparent; three switchable, team-wise layouts — Bidding
(lot + live bid + all-team purse strip), Standings board, Squad focus; animated
SOLD/UNSOLD slam stamp; SSO watermark.

**Team view:** franchise hero (purse), leading/outbid banner, stat tiles
(players / overseas / slots / spent), squad list. Read-only.

**Setup screen (added this session):** add/edit/remove teams (code, name,
colour); add players via form or **CSV bulk-import** (`Name, ROLE, COUNTRY,
base(lakh), rating, Set`); edit purse (₹Cr) / squad min-max / overseas cap;
**white-label branding** (name, logo initials, legal name). Saves to browser and
starts a fresh auction; "Restore sample" resets to defaults.

**Branding:** SSO Consultants name + copyright footer throughout; logo shown as
initials mark (real logo art can be dropped in later).

---

## 4. Files

| File | Purpose |
|------|---------|
| `index.html` | The entire app — all four views, logic, styles. |
| `README.md` | Run locally, deploy to GitHub Pages, add to OBS, use Setup, Tier-2 note. |
| `HANDOVER.md` | This document. |

---

## 5. Testing done

Headless Node smoke tests (logic only, DOM stubbed):
- Full auto-run auction: 34 lots cleared, purses never negative, squad ≤ max,
  overseas ≤ cap, reaches `done`. ✅
- Setup flow: form edits + CSV import (valid rows kept, invalid skipped) +
  Save & apply → config persisted → new auction rebuilds from it. ✅
- JS syntax checked (`node --check`). ✅

Not yet done: real in-browser click-through across multiple tabs; OBS keying
check; QR scan on a second device (Tier-1 only syncs same-machine).

---

## 6. Known limitations / honest caveats

- **Single-machine sync only** in Tier 1. Real remote team phones = Tier 2.
- **Setup saves per-browser** (localStorage), not centrally — Tier 2 fixes this.
- **QR to own phone** only truly works in Tier 2; in Tier 1 it opens the team
  view but syncs on the same machine.
- Player **base price is entered in lakh** in the form (100 = ₹1 Cr).
- Sample pool is small (34) by design; auto-run rarely produces "unsold"
  because valuations are generous (manual mode + Unsold button cover it).
- QR library loads from a CDN; offline use falls back to showing the URL.
- Squad *min* is stored but not hard-enforced (demo pool can't fill 8 squads).

---

## 7. Next steps (pick up here)

1. **Tier 2 — Firebase realtime layer.** Swap `Store` read/write/subscribe for
   Firebase Realtime DB on the existing OwlQuiz project. Then team QR codes work
   on real phones live. **Key work = the auction session data model + security
   rules** (only the auctioneer can move purses / mark sold; per-team read
   scoping so team A can't see team B's strategy). This is the genuinely careful
   part and a strong teaching moment ("why can't a team edit its own purse?").
2. **Deploy** to a GitHub repo + Pages for the shareable link (README has steps).
3. **Logo art** — replace the initials mark with a real SSO/DataOwl logo.
4. **OwlQuiz crossover** — quiz/poll module feeding the same overlay (halftime
   trivia, fan poll between lots).
5. Optional polish: RTM/retention cards, sponsor logo rotation, richer
   data-viz (spend bar-chart race), export auction summary.

---

## 8. Open questions for the owner

- Tier 2 now, or run more Tier-1 demos first?
- Is this staying an in-house teaching/event tool, or heading toward a sellable
  DataOwl product (decides how much to invest in multi-tenancy + rules)?
- Brand palette/logo to lock the visual identity?

---

© SSO Consultants (OPC) Pvt. Ltd. — SSO Auction Studio.
