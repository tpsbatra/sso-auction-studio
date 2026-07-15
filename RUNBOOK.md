# Go-Live Runbook — SSO Auction Studio (Tier 2)

Follow in order. **Do Firebase first** — you paste its config into `index.html`
*before* you push, so the very first deploy is already live/multi-device.

You do every step here (they're on your accounts). I'll troubleshoot each one.
This runbook supersedes the "Deploy to GitHub Pages" section of `README.md` —
we're using Cloudflare Pages to match your OwlQuiz workflow.

---

## PART A — Firebase (new project, free Spark plan)

**A1. Create the project**
[console.firebase.google.com](https://console.firebase.google.com) → **Add project**
→ name `sso-auction-studio` → you can turn **Google Analytics OFF** → Create.

**A2. Realtime Database**
Build → **Realtime Database** → **Create Database** → location **Singapore
(asia-southeast1)** → start in **Locked mode** → Enable.
Copy the DB URL shown at the top — looks like
`https://sso-auction-studio-default-rtdb.asia-southeast1.firebasedatabase.app`.

**A3. Auth — email/password**
Build → **Authentication** → Get started → **Sign-in method** → **Email/Password**
→ Enable → Save.

**A4. Create the auctioneer login**
Authentication → **Users** → **Add user** → email (e.g. `boss@sso.in`) + a
password. *This is the only account that can run the auction.* Write it down.

**A5. Get the web config**
⚙ (Project settings) → **General** → scroll to **Your apps** → click **`</>`
(Web)** → register app `auction` (leave "Firebase Hosting" unticked) → copy the
`firebaseConfig` values.

**A6. Paste config into `index.html`**
Find the `FIREBASE = { … }` block near the top of the `<script>` and fill it.
`databaseURL` is the switch — once it's non-blank, Tier-2 turns on. If the config
object didn't include `databaseURL`, paste the URL from step A2.

> The apiKey etc. are **not secrets** — Firebase web keys are public by design;
> your `database.rules.json` + auth are what protect the data (same as OwlQuiz).
> So committing them to the repo is fine. Repo can still be private if you prefer.

**A7. Publish the rules**
Realtime Database → **Rules** tab → replace everything with the contents of
`database.rules.json` → **Publish**.
(Public read; writes locked to the auctioneer uid; `config/adminUid` self-claims
on first sign-in.)

✅ **Checkpoint:** open `index.html` locally, add `?view=control`, and you should
see the **Auctioneer sign-in** screen. Sign in with A4 → controls appear. That
proves Firebase is wired before you deploy.

---

## PART B — GitHub repo

**B1.** On [github.com](https://github.com) → **New repository** → name
`sso-auction-studio` → **empty** (no README/licence, we have files) → Create.

**B2.** In the deploy folder on your machine, run:

```bash
git init
git add .
git commit -m "SSO Auction Studio — Tier 2 (Firebase) initial deploy"
git branch -M main
git remote add origin https://github.com/tpsbatra/sso-auction-studio.git
git push -u origin main
```

(You authenticate the same way you do for `quizforge`.)

---

## PART C — Cloudflare Pages

**C1.** Cloudflare dashboard → **Workers & Pages** → **Create** → **Pages** →
**Connect to Git** → pick `sso-auction-studio`.

**C2.** Build settings — this is a static single file, so:
- Framework preset: **None**
- Build command: **(leave empty)**
- Build output directory: **/** (root)

→ **Save and Deploy**. In ~1 min you get a `https://<something>.pages.dev` URL.

✅ **Checkpoint:** open `https://<your>.pages.dev/?view=control` on your laptop and
`?view=overlay` on your phone. Sign in on the laptop, bring a lot, place a bid —
the phone's overlay updates live. That's real multi-device working.

---

## PART D — OBS + team phones

**D1. OBS overlay**
Add a **Browser** source → URL `https://<your>.pages.dev/?view=overlay` →
Width **1920**, Height **1080**. Transparent background keys over your video.
Switch Bidding / Standings / Squad-focus from the control screen's overlay panel.

**D2. Team phones**
The control screen's QR grid encodes `…/?view=team&id=<CODE>` off the live
pages.dev URL — captains scan and land on their own read-only dashboard, live on
their own phones.

---

## If something snags

- **Sign-in screen never appears / stays on "Connecting…"** → `databaseURL` wrong
  or rules not published. Re-check A6/A7.
- **"Permission denied" in console** → rules not published, or you're signed out.
- **Overlay not transparent in OBS** → confirm the URL has `?view=overlay`.
- **QR opens but doesn't sync** → you're still on a `file://` open, not the
  pages.dev URL. Use the deployed link everywhere.

Report back at any checkpoint and I'll debug it with you.
