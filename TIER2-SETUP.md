# Tier 2 — Turn on real multi-device sync (Firebase)

Tier 1 still works with **zero setup** (double-click `index.html`). Do the steps
below only when you want team phones syncing live across the room.

When `FIREBASE.databaseURL` is left blank, the app runs in Tier-1 (localStorage)
mode exactly as before. Filling it in flips the whole app to Firebase — no other
code change.

---

## 1. Create the Firebase project (once)

1. [console.firebase.google.com](https://console.firebase.google.com) → **Add project**.
2. **Build → Realtime Database → Create database** (pick a region; start in *locked* mode).
3. **Build → Authentication → Get started → Email/Password → Enable.**
4. **Authentication → Users → Add user** — this is the **auctioneer login**
   (e.g. `boss@sso.in` + a password). This one account is the only writer.

## 2. Get your web config

**Project settings (⚙) → General → Your apps → Web app (`</>`)** → register →
copy the `firebaseConfig` values.

## 3. Paste into `index.html`

Near the top of the `<script>`, fill the `FIREBASE` block:

```js
const FIREBASE = {
  apiKey:"…", authDomain:"…", databaseURL:"https://…firebasedatabase.app",
  projectId:"…", storageBucket:"…", messagingSenderId:"…", appId:"…"
};
```

`databaseURL` is the switch — once it's non-blank, Tier 2 is on.

## 4. Publish the security rules

**Realtime Database → Rules** → paste the contents of `database.rules.json` →
**Publish**. What they enforce:

- **Anyone can read** the auction (it's a broadcast — overlay and team phones need
  no login).
- **Only the auctioneer can write.** The rule pins writes to whichever uid is stored
  at `config/adminUid`.
- **`adminUid` self-claims once:** the first time the auctioneer signs in, the app
  writes their uid there; after that the field is locked to them. No hand-editing
  of rules to change auctioneer — but if you ever need to, just delete
  `config/adminUid` in the console and let the next sign-in re-claim it.

> This is the teaching moment: "why can't a team edit its own purse?" is answered by
> these ~8 lines. The app also checks `AdminAuth.isAdmin()` before every write, so a
> team device never even attempts one — the rules are the real guard, the client
> check is courtesy.

## 5. Run it

- Open the control screen and **sign in** with the auctioneer account. The auction
  controls appear only after sign-in.
- Overlay (`?view=overlay`) and team (`?view=team&id=PAN`) open with **no login** and
  follow live on any device.
- Team QR codes now light up real phones across the room.

---

## Notes

- **One live session** by default (`AUCTION_ID = "live"`). Change it to run parallel
  events, or to wipe a session cleanly.
- **Reads are public by design** — a live auction has no secret team strategy. If you
  ever truly need private per-team data, that's a data-model split (per-team paths +
  per-path rules), a bigger change than this one.
- Free Spark plan is plenty for a single auction (tiny payloads, one writer).
