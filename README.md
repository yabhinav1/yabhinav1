## Abhinav

Full-stack developer in Delhi. First-year CSE at JIMS Greater Noida (GGSIPU).

Self-taught — no internships. Everything below I built alone and then had to
keep running, which is where most of what I know came from. The one team build
is marked.

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

### The Silent Co-Driver · [source](https://github.com/yabhinav1/silent-co-driver) · [slides](https://docs.google.com/presentation/d/18KpD-N1qZz-IirTM5YQVlFXKfGk4JwXepVmh9bDD5ng/edit?usp=sharing)

**3rd place, AI Race Month · GrandPrix** — two-person team, built in a day.

A pit wall watches tyre temps, fuel and sector deltas. Nobody has time to
process the one channel that carries fatigue first: the driver's own voice. This
reads Formula 1 team radio, scores how stressed the driver sounds 0-100, and
lines that score up against his real lap times.

Three Hugging Face models chained — **faster-whisper large-v3** for transcription
with per-word timestamps, **HuBERT** for emotion from the *sound*, **DistilRoBERTa**
for emotion from the *words* — blended 65/35 in favour of tone, because a driver
saying "I'm fine" through gritted teeth is not fine. Vocal energy separates the
two ways of not being okay: stress is loud, fatigue is flat. Thresholds are the
90th percentile measured across 63 real clips, not numbers that felt right.

**278 laps · 72 radio calls · 4 drivers**, one Grand Prix. Radio from a Hugging
Face dataset, lap times from the F1 timing feed via `fastf1`, joined only on a
timestamp. FastAPI backend, no framework on the frontend, and every model runs
locally so it works with the wifi off.

<details>
<summary><b>The bug I'm most proud of fixing</b></summary>

<br>

The transcripts looked fine. But on one clip the loudest part of the waveform had
no words against it.

Whisper was silently dropping whichever speaker talked first. On real team radio
the driver asks and the engineer answers — so we were keeping the engineer's calm
reply and throwing away the driver's question. On a project that exists to read
*the driver's* voice, every score was measuring the wrong person, and nothing
errored.

`vad_filter=True` fixed it: voice-activity detection segments the audio before
transcription instead of letting the decoder decide what counts as speech. The
trade-off is that VAD trims some word starts, so a few transcripts came back
slightly worse — a garbled phrase is a much smaller failure than a missing
speaker, so we kept it and filtered the garbled ones out of the demo.

The join has a similar story. Lap boundaries were first derived by assuming the
race started at 17:10:00 UTC. Lights-out was 17:13:00, so all 34 radio calls sat
two to three laps early — and the chart still looked entirely plausible. Only
loading the session telemetry and using the real `LapStartDate` fixed it.

</details>

### Portfolio · [source](https://github.com/yabhinav1/portfolio)

This one's public. Server-rendered Node with a `/admin` panel, SQLite via Node's
built-in `node:sqlite`, no build step, two runtime dependencies.

---

### Stack

| | |
|---|---|
| **Frontend** | Next.js · React · TypeScript · Tailwind · Framer Motion · HTML/CSS · Chart.js |
| **Backend** | Node · Express · Python · FastAPI · MongoDB · SQLite · Turso · discord.js · Hugging Face |
| **Infra** | AWS · Oracle Cloud · Vultr · Vercel · Fly.io · Cloudflare · Docker · Linux VPS · DNS · OAuth2 · sharding |

---

Open to internships and freelance work — [yabhinav0011@gmail.com](mailto:yabhinav0011@gmail.com)
