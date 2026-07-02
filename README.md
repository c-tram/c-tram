# Hi there, I'm Cole 👋

<div align="center">
  
  ![Profile Views](https://komarev.com/ghpvc/?username=c-tram&color=1a1a1a&style=flat-square)
  
  **Inbound Team Leader @ Target | Full-Stack Developer**
  
</div>

---

## ⚾ The Cycle — MLB Analytics Platform

> **Live at [thecycle.online](https://thecycle.online)** — a self-hosted, self-updating MLB analytics platform built to test every measurable baseball statistic.

The Cycle ingests live MLB data, runs proprietary statistical models entirely server-side, and serves a React frontend from a single bare-metal VPS.
---

### 🖥️ Infrastructure & Hardware

The entire platform runs on a single **Hetzner dedicated VPS**:

| Component | Spec |
|-----------|------|
| **CPU** | 16 vCPU · AMD EPYC-Milan |
| **RAM** | 30 GB DDR4 |
| **Storage** | 150 GB NVMe SSD |
| **OS** | Ubuntu 24.04.4 LTS |
| **Host** | Hetzner Cloud |

**Runtime stack — all co-located on one machine:**
- **Nginx 1.24** — reverse proxy, SSL termination, static React asset serving, plus split response-cache zones (`api_cache_current` 4 GB / 1 h for live data, `api_cache_historical` 16 GB / 30 d for past seasons, routed by `map $arg_year`)
- **PM2** — 10-worker Node.js cluster (`the-cycle`) + 1 dedicated fork worker (`the-cycle-warmer`), load-balanced, zero-downtime reloads. Background jobs (Data Engine, live aggregation, cron tasks) gated to the primary worker only via `NODE_APP_INSTANCE === '0'`.
- **Redis 8.6** — in-process, local, `allkeys-lru`, `io-threads 4`. ~1.2 M keys / ~15 GB live dataset covering every player-game and team-game from 2015–2026.
- **Express.js** — ~70 API endpoints across 10 route files, fanned out across all 10 PM2 workers
- **React (static build)** — pre-compiled CRA bundle served directly from Nginx

A player profile page requires zero network hops between services: Nginx → Express → Redis → response in < 5 ms end-to-end. Past-season responses are also cached at the **app layer** under `cache:v2:<route>:<sha1>` (24 h) with a canonicalized URL so cache-busters and default-equivalent params don't fragment hits.

---

### 🏗️ System Architecture

```
                        ┌──────────────────────────────────────────┐
                        │          Hetzner VPS (single node)        │
                        │                                           │
  User Browser ──HTTPS──► Nginx (80/443) ──► React build (static)  │
                        │      │   │                                │
                        │      │   └─ response-cache zones          │
                        │      │      (current 4 GB · historical 16 GB)
                        │      │                                    │
                        │      └─/api/*─► PM2 cluster (10 workers)  │
                        │                  + 1 fork warmer worker   │
                        │                     │                     │
                        │              Express.js (~70 endpoints)   │
                        │                     │                     │
                        │                Redis 8.6 (local)          │
                        │            ~1.2 M keys / ~15 GB           │
                        │            2015–2026 historical data      │
                        └──────────────────────┬───────────────────┘
                                               │
                              MLB Stats API (statsapi.mlb.com)
                              Polled every 90 s + nightly full refresh
```

**Redis key schema:**
```
player:TEAM-Player_Name-YEAR:season     # Season totals (batting + pitching + WAR + CVR)
player:TEAM-Player_Name-YEAR:DATE       # Single-game boxscore stats
team:TEAM:YEAR:season                   # Team season aggregates
splits:pitch:batter:TEAM-Name-YEAR      # Pitch zone data (14 zones, 6 pitch types)
splits:pitch:pitcher:TEAM-Name-YEAR     # Pitcher arsenal and zone tendencies
baseline:season:YEAR:latest             # League-average baselines for 30+ stats
salary:TEAM-Name-YEAR                   # Salary, contract type, service time
team-contracts:TEAM:YEAR                # Full roster payroll breakdown
rolling:player:TEAM-Name-YEAR:season    # Rolling window stats (7/14/21 games)
```

**Data ingestion schedule:**

| Task | Cadence (UTC) | Description |
|------|---------------|-------------|
| Live game monitor | Every 90 s | Polls MLB schedule; runs the full ingest + WAR/CVR pipeline when a game flips to Final |
| **Live aggregation** | Every 3 min while games are live | Overwrites per-game keys with running stats and rebuilds season totals from per-game keys — so `/players` season views reflect mid-game stats without double-counting |
| Response cache warmer | 09:30 daily | Precomputes 11 historical years × 11 URL variants into the app-layer cache |
| Season refresh | 04:00 daily | Full re-pull of all current-season boxscores |
| Player index rebuild | 04:30 daily | Rebuilds searchable player + stat index |
| Splits recompute | 05:00 daily | Full play-by-play splits refresh |

**Rolling CVR overlay** — every UI surface (`/players`, `/teams`, `/trades`, player detail) calls v2 endpoints with `mode=rolling`, which overlays the rolling-window `universalCVR` / `acvr` / `acvrTrend` from `rolling:player:*` keys onto the season aggregate. Batting, pitching, WAR and salary remain season totals — only the value-rating numbers reflect the recent window.

---

### 🤖 AI Search — Strategy & Architecture

The home page (`/`) is a **natural language query interface** powered by **OpenAI gpt-4o with function calling**. The design principle: GPT should _never_ answer a baseball question from training data — every stat, every comparison, every trend must be retrieved live from Redis.

**System prompt framing:**
> GPT is defined as a "DATA RETRIEVAL AGENT with ZERO internal baseball knowledge." It is explicitly forbidden from stating any statistic not returned by a tool call. A metric glossary with tier benchmarks for every proprietary metric is baked into the system prompt so GPT interprets numbers correctly once tools return them.

**16 specialized function-calling tools:**

| Tool | What it fetches |
|------|----------------|
| `get_player_season` | Current season stats for a named player |
| `get_player_rolling` | Last 7 / 14 / 21 game rolling averages |
| `get_player_trends` | Multi-window trend (slumping / hot?) |
| `get_pitch_analysis` | Zone heatmaps, pitch-type arsenal, exit velocity |
| `get_player_splits` | Home/away, vs L/R, late & close, clutch situations |
| `get_historical_stats` | Year-by-year career arc (2015–2026) |
| `get_leaderboard` | Top-N players by any sortable stat |
| `get_pitch_leaderboard` | Spin rate, velocity, whiff rate leaders |
| `get_league_context` | League-average baselines for contextual comparison |
| `get_salary_info` | Player salary, contract status, service time |
| `get_team_contracts` | Full team payroll and roster financial breakdown |
| `get_standings` | Division standings and playoff picture |
| `get_team_stats` | Team batting / pitching aggregates |
| `get_games` | Recent scores and schedule |
| `get_breakout_players` | Pre-arb / emerging value candidates |
| `compare_players` | Side-by-side stat comparison across any two players |

**Two-pass orchestration (prevents training-data hallucination):**
```
Pass 1 → tool_choice: 'auto'
  ├── Tools called → execute → synthesize answer          ✓
  └── No tools called (GPT answered from training data) →
        inject grounding nudge, reset message context
        Pass 2 → tool_choice: 'required'                  ✓
```

**Additional durability features:**
- **Rate-limit retry:** `openaiCall()` wrapper catches HTTP 429, backs off 8s → 16s, retries up to 2× before graceful failure
- **Conversation threading:** Last 6 exchanges (12 messages) sent with every request so follow-ups like *"tell me more about that"* correctly resolve their subject without re-fetching the wrong player
- **Glossary grounding:** CVR tiers (85–100 = ELITE), WAR benchmarks (8+ = MVP), wOBA/ISO scales baked into system prompt — GPT interprets numbers correctly once tools return them
- **Eval:** 43/50 (86%) in automated batch; ~95% in direct testing (remainder are rate-limit timing artifacts in the eval harness)

---

### 📄 Pages & Features

| Page | Route | Key Features |
|------|-------|-------------|
| **AI Search** | `/` | Natural language stat queries — gpt-4o + 16 tools, two-pass orchestration, follow-up chips, 6-exchange conversation history |
| **Players** | `/players` | Full league list, 30+ sortable stats, team/position filters, card/list toggle, sparklines, leaderboard mode |
| **Player Detail** | `/players/:team/:player/:year` | CVR 8-component radar, WAR trend line, Trade Value gauge, game log, career arc table, situational splits, pitch zone heatmap, spray chart |
| **Teams** | `/teams` | All 30 teams with batting/pitching grade badges, CVR/ACVR scores, league rank |
| **Team Detail** | `/teams/:teamId` | Roster depth chart, batting + pitching splits, CVR aggregate, payroll snapshot |
| **Standings** | `/standings` | Division standings, wild card race, run differential, Pythagorean W%, L10 streaks |
| **Scores** | `/scores` | Live scoreboard with date picker; click any game → full boxscore with line score + batting/pitching tables |
| **Explorer** | `/explorer` | 5 tabs: **Pitch Lab** (zone heatmaps, arsenal, Stuff+ leaders), **At-Bats** (play-by-play drill-down), **Compare** (side-by-side radar), **Teams** (team comparison), **Spray Charts** |
| **Trades** | `/trades` | Trade Value leaderboard, DTV 6-component deep dive with 6-year projection, Mock Trade Builder (fairness grade + surplus verdict), Contender⚡/Rebuilder🌱 toggle, team assets view |
| **Momentum Board** | `/momentum` | Quadrant scatter (CVR delta × WAR), biggest 30-day risers/fallers cards, full sortable table. Unified replacement for CVR Movers + Hot/Cold Streaks |
| **Fantasy / DFS** | `/fantasy` | FVS (Fantasy Value Score) board, 4 tabs: Tonight's Slate / Value Board / Full Leaderboard / My Roster. DraftKings/FanDuel salary integration, stacking suggestions |
| **Awards Tracker** | `/awards` | Live MVP, Cy Young, ROY, Silver Slugger, Gold Glove race projections — updated daily |
| **Insights** | `/insights` | Payroll efficiency scatter, WAR leaders by position, power/speed clusters, team ACVR vs payroll, aging curve plots |
| **Spray Chart** | `/spray-chart` | Batted-ball hit-coordinate visualizer with field overlay, outcome color coding, pitch-type filter |
| **Admin** | `/admin` | User count, revenue snapshot, active subscriptions — isAdmin-gated |
| **Glossary** | `/glossary` | Full metric reference: CVR, ACVR, TV, DTV, WAR, wRC+, FIP, SIERA, wOBA, ISO, BABIP, Surplus Value, Aging Curve |
| **Settings** | `/settings` | Dark/light mode, season selector (2015–2026), email digest preferences (5 modules), watchlist management, Redis health check |

---

### 📊 Proprietary Metrics

**CVR (Composite Value Rating, 0–100)** — 8-component player quality score computed server-side from raw boxscore data:

`Production (25) + Plate Approach (12) + Power / Stuff (10) + Speed / Defense (8) + Situational (10) + Durability (10) + Contract Efficiency (10) + Trajectory (15)`

| Tier | Range |
|------|-------|
| ELITE | 85–100 |
| ALL-STAR | 70–84 |
| ABOVE AVERAGE | 55–69 |
| AVERAGE | 40–54 |
| BELOW AVERAGE | 25–39 |
| REPLACEMENT | 10–24 |

**ACVR** — CVR adjusted by salary efficiency, contract type, and YoY trajectory delta. Pre-arb stars with elite production can be 10–15 points above their raw CVR.

**TV (Trade Value, 0–100)** — `WAR (30) + CVR (25) + Contract Surplus (20) + Age/Future (15) + Positional Scarcity (10)`

**DTV (Dynamic Trade Value)** — Expands TV with a 6-year discounted surplus projection, aging curve decay, positional market rate comparison, and narrative synthesis.

---

### 🔧 Tech Stack

```
Frontend          React 18 · Material UI 5 · Recharts · Framer Motion (CRA)
Backend           Node.js · Express.js · ioredis
AI Layer          OpenAI gpt-4o · Function Calling · Two-pass orchestration
Data Store        Redis 8.6 (local, single node, ~1.2 M keys / ~15 GB, allkeys-lru)
Data Source       MLB Stats API (statsapi.mlb.com — no auth required)
Process Manager   PM2 (10-worker cluster + 1 fork warmer)
Web Server        Nginx 1.24 (reverse proxy + static + SSL + split response-cache zones)
OS / Host         Ubuntu 24.04.4 LTS · Hetzner VPS (16 vCPU · 30 GB RAM · 150 GB NVMe)
CI/CD             GitHub Actions → auto-deploy on push to main
QA                Jest · route-consistency tests · pre-push lint + structural checks
```

---

## 🌟 Recent Development Activity

<!-- CHANGELOG:START -->
### ⚾ The Cycle — MLB Analytics Platform
**See it live:** [thecycle.online](https://thecycle.online)

<sub>Auto-updated changelog • Last updated: 2026-07-02 • 49 recent changes</sub>

<details>
<summary><b>📋 Recent Development Activity</b></summary>

#### 💰 Trade Intelligence

  - CVR v7: percentile-anchored performance + surplus-value steal score (`2026-07-02`)

#### 📊 Advanced Metrics

  - cvr-movers: only accept v7-tagged snapshots as baseline (`2026-07-02`)

#### ⚾ Splits & Pitch Analytics

  - Phase 1: Batted Ball tab for hitters with real spray coordinates (`2026-07-02`)
  - Pitch Lab corpus: wire into precompute + cache warm layers (`2026-07-02`)
  - Pitch Lab rework: interactive Pitch Designer + Arsenal Builder (`2026-07-02`)
  - Phase 0: retire Compare Players, Spray Charts page, Bettor CLV Tracker (`2026-07-02`)
  - ui: remove duplicate pitch arsenal table below batted ball profile in pitch analysis tab (`2026-06-07`)
  - SMEMBERS fast path + Redis cache for splits/trends/analytics; matchups 20s timeout; All-Star team fix; warm analytics+splits+trends top-50 (`2026-06-07`)
  - Splits/AtBats/SprayChart remount cleanly on year change (`2026-06-07`)
  - stale team on ExpectedStatsCard, cache analytics player-profile in Redis (24h/5m), defensive team stats batting/pitching null init (`2026-06-07`)
  - add slug to player list response + apply category filter (batting vs pitching) + fix null-slug navigation (`2026-06-07`)

#### 🎨 Frontend & UX

  - Fix Batted Ball tab: normalize expanded event field names (`2026-07-02`)
  - Redis cache for team list+detail current year (5min TTL); PlayerDetail auto-switch to spring training tab when no regular season games (`2026-06-07`)
  - Teams → TeamDetail navigation now passes selected year in URL (`2026-06-07`)
  - pass team/playerName props to PlayerOverview so ExpectedStatsCard always uses current team not stale player.team (`2026-06-07`)
  - eliminate double profile fetch + lazy analytics tab fetch — kills page load animation and year-switch delay (`2026-06-07`)
  - instant player page render from list — seed state from route prefill, no skeleton on navigation (`2026-06-07`)
  - AnimatePresence mode=sync + no exit animation — kills double-fade on navigation (`2026-06-07`)
  - remove exit animation from route transitions — eliminates double-fade on navigation (`2026-06-07`)
  - ExpectedStatsCard skeleton — eliminate layout-shift during stat load (`2026-06-07`)

#### ⚡ API & Performance

  - P2: route ?season= requests to the correct cache tier (`2026-07-01`)
  - restore production players_v2.js lost to stale local copy (`2026-07-01`)
  - P1: version-keyed current-year response cache — one recompute per pull (`2026-07-01`)
  - P0: stop purging nginx cache on live pulls — refresh in place instead (`2026-07-01`)
  - ops/nginx: exempt 127.0.0.1 from API rate limit (warmer fix) (`2026-06-07`)
  - rewrite: cacheWarmer.cjs — keepAlive agent, capped concurrency, single loop, no competing async chains (`2026-06-07`)
  - warmer 12s timeout + parallel secondary/matchup; remove setLoading(true) from loadPlayerData so prefill renders stay visible (`2026-06-07`)
  - instant team detail render from list — seed state from route prefill (`2026-06-07`)
  - warmer timeout 90s→12s, matchup+secondary endpoints run in parallel — prevents worker starvation (`2026-06-07`)
  - fix double-load on player profile: prevent reload when setSelectedTeam/Year set from API response (`2026-06-07`)
  - gamelog-keys as Redis SETs + SADD in ingest + precompute includes 2026 (`2026-06-07`)
  - cache game-log SCAN keys 1h + profile TTL 5min→30min + warmer detail 150→30 sequential (`2026-06-06`)
  - current-year player list Redis cache 2min + parallelize critical warmer URLs (`2026-06-06`)

#### 🔧 Data Pipeline

  - client-side sort on Players page — stop re-fetching per sort change (`2026-06-07`)

#### 🏗️ Infrastructure

  - push.sh + watchdog target cycle pm2 daemon — eliminates EADDRINUSE death spiral (`2026-06-07`)
  - pm2 runs as root in push.sh + max_restarts=999 in ecosystem — prevents errored worker death spiral (`2026-06-07`)

#### 🐛 Bug Fixes

  - warmer covers 2015-2026 and common sort variants (SLG, OPS, AVG, HR, ERA, K) (`2026-06-07`)
  - team year switcher starts at 2015 not 2018 (`2026-06-07`)
  - retry once on 503 after 4s delay (`2026-06-07`)
  - include slug in slim mode player list response (`2026-06-07`)
  - push.sh detects errored workers via grep and does clean restart (`2026-06-07`)
  - eliminate double-load using refs for selectedTeam/Year — kills child component spinner flash (`2026-06-07`)
  - eliminate double-load skeleton flash — don't call setLoading(true) inside loadPlayerData (`2026-06-07`)

#### 📝 General

  - --frontend (`2026-06-07`)
  - --frontend (`2026-06-07`)
  - --frontend (`2026-06-07`)
  - year switcher chips on TeamDetail header (2018–current) (`2026-06-07`)
  - revert: restore all files to 8787d1e (pre-animation-fix state) (`2026-06-07`)
  - revert preventReloadRef fix (`2026-06-07`)

</details>

---

<!-- CHANGELOG:END -->

---

## 🎯 About Me

I'm an Inbound Team Leader at Target who builds software on the side that makes complex data genuinely useful.

- 🔨 **Building:** [The Cycle](https://thecycle.online) — self-hosted, self-updating MLB analytics on bare-metal
- 🚀 **Goal:** Bridging the gap between raw data and interfaces people actually want to use
- 🌱 **Currently learning:** LLM function-calling architectures · Redis performance patterns · system design at the edge
- 💡 **Specialty:** Turning messy data pipelines into something clean and fast

---

## 💼 Professional Focus

<div align="center">

| 🎯 Software Engineering | ☁️ Infrastructure | 📊 Data & Analytics |
|------------------------|-------------------|---------------------|
| Full-Stack Development | Linux VPS & Nginx | Statistical Computing |
| LLM / AI Architecture | Performance Optimization | Predictive Analytics |
| Real-Time Data Processing | PM2 · Redis · Docker | Business Intelligence |
| RESTful API Design | CI/CD & DevOps | Proprietary Metric Development |

</div>

---

## 🎓 Core Competencies

- **LLM Function-Calling Systems** — Multi-tool AI agents grounded in live data; two-pass orchestration, rate-limit handling, conversation history threading — GPT never answers from training data
- **Full-Stack Platform Architecture** — End-to-end systems from raw data ingestion through API design to polished UI, on commodity hardware with sub-5ms response times
- **Redis-First Data Design** — Pre-aggregated JSON at write time, O(1) reads at query time; no SQL, no ORM, no query planning overhead
- **Performance Engineering** — PM2 clustering, Nginx static caching, parallel Redis pipelines, React code splitting
- **Proprietary Algorithm Development** — Multi-component composite scoring systems (CVR, ACVR, TV, DTV) integrating production, contract, age curve, and positional scarcity signals
- **Cross-Functional Leadership** — 6+ years driving operational excellence and coaching high-performing teams at Target

---

## 📊 GitHub Statistics

<div align="center">
  
  ![GitHub Stats](https://github-readme-stats.vercel.app/api?username=c-tram&show_icons=true&theme=dark&hide_border=true&count_private=true&bg_color=0d1117&title_color=f0e68c&text_color=c9d1d9&icon_color=f0e68c)
  
  ![Top Languages](https://github-readme-stats.vercel.app/api/top-langs/?username=c-tram&layout=compact&theme=dark&hide_border=true&bg_color=0d1117&title_color=f0e68c&text_color=c9d1d9)
  
  ![GitHub Streak](https://github-readme-streak-stats.herokuapp.com/?user=c-tram&theme=dark&hide_border=true&background=0d1117&ring=f0e68c&fire=f0e68c&currStreakLabel=f0e68c)

</div>

---

## 📈 Contribution Activity

![Activity Graph](https://github-readme-activity-graph.vercel.app/graph?username=c-tram&theme=github-dark&hide_border=true&area=true&bg_color=0d1117&color=f0e68c&line=f0e68c&point=c9d1d9)
