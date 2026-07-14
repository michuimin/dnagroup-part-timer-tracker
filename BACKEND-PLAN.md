# Backend plan — turning the prototype into a shared team app

## Your deployment plan: is it correct?
- **Code on GitHub → deploy to Vercel: YES, correct.** GitHub stores the source; Vercel builds/hosts it.
- **GitHub as the database: NO.** GitHub is version control for code, not a store for live app data.
  Committing submissions to a file would hit race conditions (two agents submitting at once
  overwrite each other), rate limits, and would expose a secret token in the browser.

You need **three layers**, not two:

| Layer | Job | Tool |
|---|---|---|
| Code | Store the source | GitHub ✅ |
| Hosting | Serve the site | Vercel ✅ |
| Database | Hold submissions | **Still needed** (see below) |

## Why a database is needed
The current app saves to the browser (`localStorage`). Deployed as-is, each agent's data stays only
on their own device and the leader sees nothing. A shared database fixes this without changing the UI —
only *where data is saved* changes.

```
Agents' phones ──►  Vercel (HTML form)  ──►  Database  ◄──  Leader dashboard (reads everyone)
                                                ▲
Wednesday 12am auto-zero  ◄── scheduled job ────┘
```

## Recommended: Supabase (pairs perfectly with GitHub + Vercel)
- Free hosted Postgres database with an auto-generated API — the form talks to it directly from the
  browser, so there's no server to manage.
- Free tier is plenty for 10–20 agents.
- Real scheduled Wednesday-12am auto-zero via **Vercel Cron** (a tiny function) or Supabase's own scheduler.

### What setup looks like (when you're ready)
1. **You:** create a free account at supabase.com → new project → copy the **Project URL** and **anon key**.
2. **Me:** create one `submissions` table + security rules, and swap the app's save/load code from
   `localStorage` to Supabase calls (the whole UI stays identical).
3. **Me:** add a small `/api/auto-zero` function + a `vercel.json` cron entry for the Wednesday job.
4. **You:** push to GitHub → Vercel auto-deploys. Done.

Repo would then contain: `index.html`, `/api/auto-zero.js`, `vercel.json` — still simple; Vercel handles the rest.

## Other options
- **Vercel Postgres** — all-in-one on Vercel, no third-party account, but needs the project restructured
  into a small Node app with API routes (more setup than a single HTML file).
- **Google Sheets + Apps Script** — data lands in a Sheet the leaders already know, native Excel,
  built-in scheduler. Doesn't even need Vercel. Simplest for a non-technical team.

## Note on access
Agents just pick a name (no login), so anyone with the link could submit as anyone. Fine for an internal
honesty-based tool; a shared passcode can be added if you want a light gate.

---
When ready, just say which option and I'll build it. Nothing here changes the look or flow of what you have now.
