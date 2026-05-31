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
| **CPU** | 8 vCPU · AMD EPYC-Milan |
| **RAM** | 32 GB DDR4 |
| **Storage** | 150 GB NVMe SSD |
| **OS** | Ubuntu 24.04.4 LTS |
| **Host** | Hetzner Cloud |

**Runtime stack — all co-located on one machine:**
- **Nginx** — reverse proxy, SSL termination, static React asset serving
- **PM2** — 6-worker Node.js cluster (load-balanced, auto-restart on crash, zero-downtime reloads)
- **Redis** — in-process, local, no external calls — sub-millisecond key lookups for all player/team/pitch data
- **Express.js** — ~70 API endpoints across 10 route files, distributed across all 6 PM2 workers
- **React (static build)** — pre-compiled, served directly from Nginx

A player profile page requires zero network hops between services: Nginx → Express → Redis → response in < 5ms end-to-end.

---

### 🏗️ System Architecture

```
                        ┌──────────────────────────────────────────┐
                        │          Hetzner VPS (single node)        │
                        │                                           │
  User Browser ──HTTPS──► Nginx (80/443) ──► React build (static)  │
                        │      │                                    │
                        │      └──/api/*──► PM2 Cluster (6 workers) │
                        │                     │                     │
                        │              Express.js (~70 endpoints)   │
                        │                     │                     │
                        │                  Redis (local)            │
                        │             3,500–4,000 players/year      │
                        │             2015–2026 historical data      │
                        └──────────────────────┬───────────────────┘
                                               │
                              MLB Stats API (statsapi.mlb.com)
                              Polled live + nightly full refresh
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

| Task | CDT | Description |
|------|-----|-------------|
| Live game monitor | Every 90s | Polls MLB schedule, writes boxscores when games go final |
| Season refresh | 11:00 PM | Full re-pull of all current-season boxscores |
| Player index rebuild | 11:30 PM | Rebuilds searchable player + stat index |
| Splits recompute | 12:00 AM | Full play-by-play splits refresh |

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
| **Trades** | `/trades` | Trade Value leaderboard, DTV 6-component deep dive with 6-year projection, Mock Trade Builder (fairness grade), team assets view |
| **Insights** | `/insights` | Payroll efficiency scatter, WAR leaders by position, power/speed clusters, team ACVR vs payroll, aging curve plots |
| **Hot/Cold Streaks** | `/streaks` | Rolling-window performance tracker (7/14/21 days), min-PA/IP filters, card and table views |
| **Spray Chart** | `/spray-chart` | Batted-ball hit-coordinate visualizer with field overlay, outcome color coding, pitch-type filter |
| **Fantasy / DFS** | `/fantasy` | DFS lineup optimizer, value plays, stack suggestions, DraftKings/FanDuel slate breakdown |
| **Awards Tracker** | `/awards` | Live MVP, Cy Young, ROY, Silver Slugger, Gold Glove race projections — updated daily |
| **Glossary** | `/glossary` | Full metric reference: CVR, ACVR, TV, DTV, WAR, wRC+, FIP, SIERA, wOBA, ISO, BABIP, Surplus Value, Aging Curve |
| **Settings** | `/settings` | Dark/light mode, season selector (2015–2026), Redis health check, API connectivity status |

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
Frontend          React 18 · Material UI 5 · Recharts · Framer Motion
Backend           Node.js · Express.js · ioredis
AI Layer          OpenAI gpt-4o · Function Calling · Two-pass orchestration
Data Store        Redis (local, single node, ~150 MB live dataset)
Data Source       MLB Stats API (statsapi.mlb.com — no auth required)
Process Manager   PM2 (6-worker cluster)
Web Server        Nginx (reverse proxy + static serving + SSL)
OS / Host         Ubuntu 24.04.4 LTS · Hetzner VPS
CI/CD             GitHub Actions → auto-deploy on push to main
QA                Jest · route-consistency tests · pre-push lint + structural checks
```

---

## 🌟 Recent Development Activity

<!-- CHANGELOG:START -->
### ⚾ The Cycle — MLB Analytics Platform
**See it live:** [thecycle.online](https://thecycle.online)

<sub>Auto-updated changelog • Last updated: 2026-05-30 • 50 recent changes</sub>

<details>
<summary><b>📋 Recent Development Activity</b></summary>

#### 💰 Trade Intelligence

  - contracts tab WAR/CVR/ACVR (`2026-05-01`)
  - skip per-player KEYS scan for traded players (`2026-04-30`)
  - overlay rolling CVR/ACVR to match UI display values (`2026-04-30`)

#### 📊 Advanced Metrics

  - monetization — Pro paywall, Bettor CLV Tracker, CVR email alerts (`2026-05-27`)
  - compute season WAR on-the-fly; stop overlaying multi-year rolling WAR (`2026-05-01`)
  - backfill team WAR + player position/playerType/war when season key fields stripped by reaggregation (`2026-04-30`)
  - infinite scroll pagination (25/page, 200 max) (`2026-04-30`)
  - bump CVRMoversCard limit to 25 (`2026-04-30`)
  - bump default limit to 25 per side for scroll (`2026-04-30`)
  - scrollable top/bottom 10 columns with fade hint (`2026-04-30`)
  - nightly rolling snapshots + 7d/30d/season toggle (`2026-04-30`)
  - nightly rolling snapshots + 7d/30d/season toggle (`2026-04-30`)
  - CVR Movers daily risers/fallers widget on hero (Tier-1 #1) (`2026-04-30`)
  - filter out 0-baseline noise (rookie stubs) (`2026-04-30`)
  - use prev-year season CVR as baseline (`2026-04-30`)
  - GET /api/v2/stats/cvr-movers — daily CVR risers/fallers (`2026-04-30`)

#### ⚾ Splits & Pitch Analytics

  - Tier-1 #3 — spider chart on Compare Players (RadarChart batting/pitching profile) (`2026-05-28`)

#### 🎨 Frontend & UX

  - account menu in top-right nav — login status, sign in/out, settings, upgrade/manage sub (`2026-05-30`)

#### ⚡ API & Performance

  - awards mget casing (ioredis uses mget not mGet) (`2026-05-28`)
  - restore team detail key metrics, player WAR/position (`2026-05-01`)
  - skip rolling overlay for past seasons + warm sortBy variants (`2026-04-29`)
  - canonicalize Redis cache key (drop cache-busters, default-equiv params, sort keys) (`2026-04-29`)
  - persist past-year payloads to Redis-backed shared cache (`2026-04-29`)
  - precompute /players and /teams cache for every year (`2026-04-29`)
  - Redis-backed shared cache for past-year responses (`2026-04-29`)
  - skip rolling overlay for past seasons (22s -> ~1s MISS) (`2026-04-29`)
  - ops: live boxscore pull cron + nginx cache purge (`2026-04-29`)
  - year-aware Cache-Control — 30d TTL for historical seasons (`2026-04-29`)
  - SCAN-based getKeysByPattern + scale to 10 PM2 workers (`2026-04-29`)
  - hit nginx (HTTPS) instead of backend so shared cache fills (`2026-04-29`)

#### 🔧 Data Pipeline

  - add openai to package.json dependencies (NLQ was broken — module missing) (`2026-05-28`)
  - rewrite push.sh — commit + GitHub + VPS rsync/restart in one script (`2026-05-27`)
  - replace floating overlay with dedicated /compare/players page (`2026-04-30`)
  - efficiency-first rewrite, absorb Insights, kill /insights page (`2026-04-29`)
  - restore: bring back the awesome Teams + Insights pages (regressed in d1ce384) (`2026-04-29`)

#### 🏗️ Infrastructure

  - move dotenv.config() to top of server.js so JWT_SECRET loads before auth module (`2026-05-27`)

#### 🐛 Bug Fixes

  - AI search (openai pkg), canceled snackbar, checkout error handling in RequirePro (`2026-05-28`)
  - awards tracker — position from player-index, ROY rookie detection via team history (`2026-05-28`)
  - push.sh SSH quoting + dotenv order (`2026-05-27`)
  - Fix Correa-style undercounts: stop double-subtracting postseason (`2026-05-02`)
  - Fix Boxscore shutter loop on /scores (`2026-05-02`)
  - Fix spring training contamination of season aggregates (`2026-05-01`)
  - use runsScored/runsAllowed for nested runs (flat 'runs' is ambiguous) (`2026-05-01`)

#### 📝 General

  - auth + Stripe paywall for AI search (`2026-05-27`)
  - Player comparison overlay — dual-radar + AI summary (Tier-1 #3) (`2026-04-30`)
  - The Cycle Index — team market efficiency leaderboard (`2026-04-30`)
  - warmer: include rolling/category/playerType variants (`2026-04-29`)
  - A-F dynamic grades, sortable Grade column, remove filter chips (`2026-04-29`)
  - grade filter chips + 162-game projected $/W (`2026-04-29`)
  - minimalist tabbed table — drop record cols, filters, embedded panels (`2026-04-29`)

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
