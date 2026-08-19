## Abhinav

Full-stack developer in Delhi. First-year CSE at JIMS Greater Noida (GGSIPU).

Self-taught and solo — no internships, no team. Everything below I built alone
and then had to keep running, which is where most of what I know came from.

---

### Annie · [annie.monster](https://annie.monster)

An all-in-one Discord bot that replaces the six single-purpose bots most servers
end up running — economy, moderation, music, games, social, community tools.

**417 commands** (313 prefix + 104 slash) across 23 categories · 29 event
listeners · ~125k lines · sharded through `discord.js` `ShardingManager` ·
anti-nuke and automod · a simulated coin market with generated charts.

The dashboard is a separate Next.js app — **~41k lines, 128 API routes** — with
Discord OAuth2, per-guild config panels so nobody has to memorise command syntax,
premium checkout with IPN callbacks, a public status page with incident history,
and rendered ticket transcripts.

<details>
<summary><b>The bug I'm most proud of fixing</b></summary>

<br>

Shards leaked memory on a 2-core container: RSS climbed steadily while the JS
heap stayed flat. It wasn't JavaScript.

glibc's mmap threshold is *dynamic* — it ratchets upward every time a large
buffer is freed. So after the first few canvas/sharp image buffers came and went,
subsequent large allocations started coming from the arena heap (`sbrk`) instead
of `mmap`, and arena memory is never returned to the OS.

Pinning `MALLOC_MMAP_THRESHOLD_` to a fixed 128KB disables the ratchet — every
large allocation uses `mmap`, which *is* returned on free. `MALLOC_ARENA_MAX=2`
stops the container (which reports 16 cores but is limited to 2) from opening
~128 malloc arenas that fragment and never hand memory back.

Both have to be set in code before `manager.spawn()`, not in `.env` — glibc and
libuv read them at process start, before dotenv ever runs.

</details>

### DailyOS · [dailyos.dpdns.org](https://dailyos.dpdns.org)

A personal productivity OS — notes with folders, tasks, projects, calendar,
habits, goals, analytics, study mode, focus timer, doodle canvas, and a developer
mode. Eleven modules sharing one data model, so a task can come from a note and a
habit can roll into a goal without an integration in between.

Next.js, deployed on Vercel behind Cloudflare.

### Portfolio · [source](https://github.com/yabhinav1/portfolio)

This one's public. Server-rendered Node with a `/admin` panel, SQLite via Node's
built-in `node:sqlite`, no build step, two runtime dependencies.

---

### Stack

| | |
|---|---|
| **Frontend** | Next.js · React · TypeScript · Tailwind · Framer Motion |
| **Backend** | Node · Express · MongoDB · SQLite · discord.js |
| **Infra** | Vercel · Cloudflare · Fly.io · OAuth2 · sharding |

---

Open to internships and freelance work — [yabhinav0011@gmail.com](mailto:yabhinav0011@gmail.com)
