# SSO Auction Studio — Live Player Auction (Tier 1)

A single-file, IPL-style player-auction tool for live streaming. One `index.html`
runs the whole thing: the auctioneer's control panel, a transparent broadcast
overlay for OBS, and a private per-team view — no build step, no server.

Built as a teaching artifact for **SSO Consultants (OPC) Pvt. Ltd.**
© SSO Consultants (OPC) Pvt. Ltd.

---

## The three views (same file, different URL)

| View | URL | Who uses it |
|------|-----|-------------|
| **Control** | `index.html` (or `?view=control`) | The auctioneer — the only screen that can bid, sell, and advance |
| **Overlay** | `?view=overlay` | The transparent graphic you point OBS at |
| **Team** | `?view=team&id=PAN` | A franchise's private, read-only dashboard |

All three read one shared "auction session." The control screen writes; the others
follow live.

---

## Run it locally (30 seconds)

Double-click `index.html` — it opens in your browser and works immediately.
For live sync across separate tabs (control in one, overlay in another), it's best
to **serve** it rather than open the raw file. Easiest local server:

```bash
# from the folder containing index.html
python3 -m http.server 8000
# then open http://localhost:8000
```

> Tier 1 syncs views through the browser on **one machine** (all tabs, same
> origin). Real remote phones across the room is the Tier 2 / Firebase step.

---

## Put it online + get a shareable link (GitHub Pages)

1. Create a new GitHub repo (e.g. `ipl-auction`) and add `index.html` to it.
2. Repo → **Settings → Pages**.
3. Under *Build and deployment*, set **Source: Deploy from a branch**, branch
   `main`, folder `/ (root)`. Save.
4. Wait ~1 minute. Your link appears at the top of the Pages settings, e.g.
   `https://<your-username>.github.io/ipl-auction/`.

Share that link. The control screen, overlay, and team QR codes all work from it.

---

## Add the overlay to OBS

1. In OBS, add a **Browser** source.
2. URL: your overlay link, e.g.
   `https://<you>.github.io/ipl-auction/?view=overlay`
3. Width `1920`, Height `1080`. Tick **Shutdown source when not visible** off.
4. The background is transparent, so it keys cleanly over your video.

Switch overlay layouts (Bidding / Standings / Squad focus) and show/hide the
graphics from the **Broadcast overlay control** panel on the control screen — the
overlay updates live.

---

## Team access by QR

The control screen shows a QR code per team (**Team access — scan to open**). Each
encodes that team's URL (`?view=team&id=…`). Display them on the projector or share
them; a captain scans and lands on their own dashboard.

- **Tier 1:** scanning opens the team view; it syncs live on the same machine/tabs.
- **Tier 2 (Firebase):** the same QR lights up on any phone, anywhere, in real time.

---

## Make it your own — the Setup screen

Open the **Setup** screen (control screen → **⚙ Setup**, or add `?view=setup` to the
URL). No code editing needed. From there you can:

- **Teams** — add/remove franchises; edit each code, name, and colour.
- **Players** — add them one at a time (name, role, country, base, rating, set), or
  **bulk-import** a whole list by pasting rows: `Name, ROLE, COUNTRY, base(lakh), rating, Set`
  (ROLE = BAT/BOWL/AR/WK, COUNTRY = IND/OVR, 100 lakh = ₹1 Cr).
- **Rules** — purse per team (in ₹ Cr), min/max squad, overseas cap.
- **Branding** — white-label it: short name, logo initials, and the legal name used
  in the copyright footer.

Hit **Save & start auction** — your setup is stored in the browser and a fresh
auction begins from it. **Restore sample** brings back the built-in demo teams/players.

> Advanced: the built-in defaults still live in the `CONFIG` block at the top of the
> `<script>` if you'd rather seed them in code.

The sample pool is deliberately small (34 players) so a full demo runs quickly.

---

## Demo tips

- **Auto-run** (control → *Demo auto-pilot*) makes teams bid on their own so the
  auction runs itself — great for showing the whole flow. Adjust the speed slider.
- **Simulate one bid** advances a single bid at a time if you want to narrate it.
- Drive it manually anytime: tap a team to bid, then **Sold** / **Unsold**.

---

## Going to Tier 2 (real multi-device, later)

The whole app talks to state through one small `Store` object (localStorage today).
To make team phones live across the room, swap that layer's read/write/subscribe for
**Firebase Realtime DB** — the same project pattern behind OwlQuiz — and add security
rules so only the auctioneer can move purses and mark players sold. Nothing else in
the app has to change.

---

© SSO Consultants (OPC) Pvt. Ltd. — SSO Auction Studio.
