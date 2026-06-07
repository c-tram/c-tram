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

<sub>Auto-updated changelog • Last updated: 2026-06-07 • 49 recent changes</sub>

<details>
<summary><b>📋 Recent Development Activity</b></summary>

#### 💰 Trade Intelligence

  - Redis-backed shared cache for all heavy scans (players, rolling, leaders, leaderboard, cvr-movers, cycle-index, teams) — near-0% cold miss across all 10 workers; fix warmer URLs (cvr-movers, cycle-index, HotCold defaults, FantasyDFS, trade path fix, detail ceiling 300) (`2026-06-03`)
  - Trade War Room v5 — 3-tab rewrite (War Room / Market / Compare) (`2026-06-02`)
  - MLB-only — exclude spring training players from Fantasy/Momentum/Trades (gameTypeBreakdown.R check in players_v2, trade_v2, recalculateCVR) (`2026-06-01`)
  - Momentum Board (replaces CVR Movers + Hot/Cold), FVS fantasy board, Trade War Room (surplus verdict + contender/rebuilder toggle), AL/NL league insights email split (`2026-06-01`)
  - revert WBC player filter in trades, resolve MLB team from redis for logo display (`2026-05-31`)
  - filter WBC/non-MLB team codes from trade_v2 player key scans (`2026-05-31`)
  - sidebar cleanup, invite-pro fix, sub days left, CVR Movers standalone+filters, hot-cold filters+mode, fantasy roster+team filter, trade engine CTS explainer (`2026-05-31`)
  - Redis response cache for stuff-plus leaderboard + trade leaders (fix cold Pro page loads) (`2026-05-31`)

#### 📊 Advanced Metrics

  - email — date in header, remove hero, condensed Yesterday scores, Team Pulse L10+boxscore fixes, League Snapshot SP/RP split, CVR Movers BUY/FADE signals (`2026-06-03`)
  - full digest email — all 5 modules (CVR movers, hot/cold, team pulse, league insights, fantasy edge), personalized per user (`2026-06-01`)
  - email digest — fetch CVR movers from API, add emailPrefs.enabled check (`2026-06-01`)
  - Settings.js extra paren in CVR Movers tab fragment (was breaking build → 403) (`2026-05-31`)
  - Email Digests tab — player watchlist, team pulse, module toggles; CVR Movers nav rename (`2026-05-31`)
  - remove CVR Movers + Cycle Index from home; CVR Movers now a Pro tab in Settings (`2026-05-31`)

#### ⚾ Splits & Pitch Analytics

  - batted ball profile 0% + pitcher Quality of Contact color inversion (`2026-06-06`)
  - pitch count table showing cumulative totals instead of season totals (`2026-06-06`)
  - game-preview + pitcher-vs-lineup use Redis cache (shared across all 10 workers, 2h TTL) (`2026-06-03`)
  - cacheWarmer matchup loop — pre-warm today's game-preview + pitcher-vs-lineup on 30-min cadence (`2026-06-02`)

#### 🎨 Frontend & UX

  - email digest revamp — Yesterday in Baseball hero, logo branding, Team Pulse with game results, K leader min-IP fix, remove Hot/Cold, reorder sections (`2026-06-02`)
  - admin dashboard — /admin route, /api/admin/stats endpoint, isAdmin flag on coletrammell7@gmail.com (`2026-05-31`)
  - push.sh — guard build failure, fix banner printf for zsh compat (`2026-05-31`)

#### ⚡ API & Performance

  - fix double-load on player profile: prevent reload when setSelectedTeam/Year set from API response (`2026-06-07`)
  - gamelog-keys as Redis SETs + SADD in ingest + precompute includes 2026 (`2026-06-07`)
  - cache game-log SCAN keys 1h + profile TTL 5min→30min + warmer detail 150→30 sequential (`2026-06-06`)
  - current-year player list Redis cache 2min + parallelize critical warmer URLs (`2026-06-06`)
  - correct 6 wrong team codes in warmer (TBR/KCR/SDP/SFG/ARI/WSN) + parallelize TeamDetail schedule fetch (`2026-06-06`)
  - fix ProPagePrefetcher 404s + historical profile TTL 30d + precompute profile phase 2 (`2026-06-06`)
  - 15-min nginx TTL + Redis profile cache + expand warmer + historical precompute (`2026-06-06`)
  - fix thundering herd — sequential team SCAN loop + redisCache in-flight deduplication (`2026-06-06`)
  - fix team/player detail latency — shared Redis scan cache + parallel warmer batches (`2026-06-06`)
  - NLQ streaming — text appears token-by-token, blinking cursor while generating (POST /v2/nlq/stream SSE endpoint) (`2026-06-03`)
  - Team Pulse boxscore — fetch per-game boxscore individually (/game/pk/boxscore) since schedule API never hydrates player stats (`2026-06-03`)
  - admin — Send Test Digest button per user row, POST /api/admin/send-test-digest endpoint (`2026-06-02`)
  - cacheWarmer URL accuracy — match exact nginx cache keys for teams, players, team rosters, player profiles (`2026-06-02`)
  - warm 30 team detail pages + top-100 player detail pages every 20min (separate detail loop in cacheWarmer) (`2026-06-01`)
  - routes check precomputed:pro:* keys first before scanning Redis (`2026-05-31`)
  - routes check precomputed:pro:* keys first before scanning Redis (`2026-05-31`)

#### 🔧 Data Pipeline

  - warmer detail loop 20min→4min, raise player ceiling 100→150 (fix cold team/player detail pages) (`2026-06-03`)
  - email digest — correct player profile URLs, salary-based fantasy edge, standings in team pulse, stats in hot/cold rows (`2026-06-01`)
  - Admin page — wait for auth hydration before isAdmin check (was redirecting on null user) (`2026-05-31`)
  - Admin page — useAuth() instead of AuthContext (not exported) (`2026-05-31`)
  - forgot/reset password — email flow, /reset-password page, AuthModal forgot link (`2026-05-31`)

#### 🐛 Bug Fixes

  - eliminate double-load skeleton flash — don't call setLoading(true) inside loadPlayerData (`2026-06-07`)
  - spring-training filter — also skip players with missing gameTypeBreakdown (stale keys) (`2026-06-02`)
  - WBC team logos 404 (skip non-MLB codes), p.tier object crash in suggestions table (`2026-05-31`)

#### 📝 General

  - revert preventReloadRef fix (`2026-06-07`)
  - /scores/:gamePk deep-link, email links to app, warmer pre-warms today+yesterday schedule (`2026-06-03`)
  - email digest — all game scores in Yesterday section, Team Pulse shows yesterday's boxscore (AB/H/HR/RBI + SP line) (`2026-06-02`)
  - swap mailer from Mailgun to Resend (`2026-05-31`)

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
