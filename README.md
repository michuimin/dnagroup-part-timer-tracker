# Part-Timer Weekly Tracker

Interactive weekly activity form + dashboard for AIA part-timers, with AFYC/ANP
auto-calculation, Excel export, and a Wednesday-12am auto-zero.

## Open it locally
Just double-click `index.html` — it opens in your browser. It's a single
self-contained file (no build step, no dependencies).

## Deploy to Vercel via GitHub
1. Create a new GitHub repo and upload the contents of this folder
   (`index.html`, `vercel.json`, `README.md`).
2. Go to https://vercel.com → **Add New… → Project** → import that repo.
3. Framework preset: **Other** (it's a static site). Leave build/output empty.
4. Click **Deploy**. Your form is live at `https://<your-project>.vercel.app`.

## ⚠️ Important limitation (read before rolling out to the team)
This version stores data in **each person's own browser** (localStorage). That's
perfect for one person testing, but on a deployed site it means:

- Every agent's submission stays only on **their own device**.
- The leader's dashboard shows only **their own** browser's data — not the team's.
- The Wednesday auto-zero runs in the browser, not on a real server clock.

To make agents submit to **one shared place** the leader can review, the app needs
a small backend (database). See the chat for the recommended setup
(Supabase or Google Sheets) — the UI stays the same; only where the data is saved
changes.
