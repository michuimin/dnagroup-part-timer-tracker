# Connect the tracker to Supabase (shared database)

The app already has all the Supabase code built in. It runs in **demo mode**
(data in your own browser) until you add your keys — then it switches to
**shared mode** automatically.

## Step 1 — Create a Supabase project
1. Go to **https://supabase.com** → sign in (you can use your Google account).
2. **New project** → give it a name (e.g. `part-timer-tracker`), set a database
   password (save it somewhere), pick region **Southeast Asia (Singapore)** (closest to Malaysia).
3. Wait ~2 minutes for it to finish setting up.

## Step 2 — Create the table
1. In the left sidebar click **SQL Editor** → **New query**.
2. Paste this and click **Run**:

```sql
create table if not exists submissions (
  id bigint generated always as identity primary key,
  week text not null,
  name text not null,
  recruit jsonb not null default '{}'::jsonb,
  sales   jsonb not null default '{}'::jsonb,
  auto_filled boolean not null default false,
  edited      boolean not null default false,
  updated_at  timestamptz not null default now(),
  unique (week, name)
);

alter table submissions enable row level security;

create policy "team access" on submissions
  for all to anon using (true) with check (true);
```

## Step 3 — Get your 2 keys
1. Left sidebar → **Project Settings** (gear) → **API** (or **Data API**).
2. Copy:
   - **Project URL** — looks like `https://abcd1234.supabase.co`
   - **anon public** key — a long string starting with `eyJ…`

## Step 4 — Put the keys in the app
Edit **`config.js`** (easiest: on GitHub, open `config.js` → pencil icon → edit):

```js
window.SUPABASE_URL = "https://abcd1234.supabase.co";
window.SUPABASE_ANON_KEY = "eyJ...your-anon-key...";
```

Commit. Vercel redeploys automatically in ~30 seconds.

## Step 5 — Confirm it worked
Open your live site → **Dashboard**. Under the title you should see:

> ☁ **Connected to shared database**

Now every agent who submits writes to the same database, and every leader sees
everyone's numbers. Test it: submit on your phone, then open the dashboard on
your laptop — the entry should appear.

---

## Notes
- **Security:** the policy above lets anyone with the site link read/write (fine for
  an internal, name-based team tool). Tell me if you want a shared passcode gate.
- **Auto-zero:** right now the Wednesday-0-fill runs whenever the dashboard is
  opened after the deadline (writing to the shared DB). For a *true* server job that
  fires exactly at Wednesday 12am on its own, add a **Vercel Cron** — ask me and
  I'll wire it (needs a small `/api` function + a service key stored as a Vercel
  environment variable).
- **Demo mode:** leave `config.js` blank to go back to browser-only mode.
