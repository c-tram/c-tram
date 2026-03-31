# Hi there, I'm Cole 👋

<div align="center">
  
  ![Profile Views](https://komarev.com/ghpvc/?username=c-tram&color=1a1a1a&style=flat-square)
  
  **Inbound Team Leader @ Target | Full-Stack Developer**
  
</div>

---

## 🌟 Featured Projects

<!-- CHANGELOG:START -->
### ⚾ The Cycle — MLB Analytics Platform
**See it live:** [thecycle.online](https://thecycle.online)

<sub>Auto-updated changelog • Last updated: 2026-03-30 • 48 recent changes</sub>

<details>
<summary><b>📋 Recent Development Activity</b></summary>

#### 💰 Trade Intelligence

  - Add team control column to Trade Assets roster table (`2026-03-30`)
  - recompute team CVR/ACVR in post-processing reaggregation (`2026-03-30`)
  - preserve acvr + teamPayroll in post-processing team reaggregation (`2026-03-30`)
  - proper team CVR (raw performance) + ACVR (bang for buck) (`2026-03-30`)
  - team CVR uses avgACVR (0-100 scale) + sort no longer resets view (`2026-03-30`)
  - overhaul: trade value engine v4 — prior-year benchmarks + simple pace scaling (`2026-03-29`)
  - trade engine WAR prorating + DTV benchmark fallback (`2026-03-29`)
  - prorate WAR in trade value for partial seasons (v6.1) (`2026-03-29`)
  - add team control column to trade value dashboard (`2026-03-29`)
  - pass mode=rolling to all trade tab endpoints (`2026-03-28`)
  - Fix trade value: pass mode through + use prev year benchmarks for rolling (`2026-03-28`)
  - Trade leaders: warm cache for rolling mode too (`2026-03-28`)

#### 📊 Advanced Metrics

  - WAR to 2 decimal places everywhere (player detail, team detail, roster, contracts, backend storage) (`2026-03-30`)
  - show WAR with 2 decimal places on team detail page (`2026-03-30`)
  - contracts tab crash (typeof null === 'object') + enrich contracts with WAR/CVR (`2026-03-30`)
  - single rolling fetch + fix wOBA field casing (`2026-03-30`)
  - lower isPitcher threshold in recalculateCVR + respect playerType in WAR routing (`2026-03-29`)
  - CVR calculation bugs - falsy trap, control formula, rolling recomputation (`2026-03-29`)
  - use rolling data everywhere — dashboard, leaders, benchmarks (`2026-03-28`)

#### ⚾ Splits & Pitch Analytics

  - Add pitcher consistency (Game Score) and pitcher streak analysis (`2026-03-28`)
  - Fix analytics: detectPitcher SP/RP, year-doubling keys, rolling mode everywhere, salary merge for rolling (`2026-03-28`)
  - Rolling window system: 162-game (batters) / 30-game (pitchers) replaces year selectors (`2026-03-28`)

#### 🎨 Frontend & UX

  - Mobile responsiveness: comprehensive useMediaQuery guards across all pages (`2026-03-30`)
  - composite /dashboard endpoint — 13 API calls → 1 (`2026-03-30`)
  - card standings on dashboard, team logos in standings, card default on teams (`2026-03-29`)

#### ⚡ API & Performance

  - add in-memory caching to analytics/leaderboard + startup cache warming (`2026-03-29`)
  - deduplicate rolling cache reads, filter AL/NL from cache path (`2026-03-28`)
  - Rolling cache: pre-compute overnight + restore year selectors (`2026-03-28`)

#### 🔧 Data Pipeline

  - add hover tooltips explaining every stat column on Teams page (`2026-03-30`)
  - remove 9 obsolete backfill/fix scripts (~2,177 lines) (`2026-03-29`)
  - rewrite backfill script with team filtering, add cleanup script (`2026-03-29`)
  - Fix backfillMissingGames doubleheader detection: match by gamePk not date (`2026-03-29`)
  - Add fast backfill script (single SCAN + pipeline writes) (`2026-03-29`)
  - smarter hasRegularSeasonGames heuristic for pre-backfill data (`2026-03-29`)
  - exclude spring training from career stats + backfill gameTypeBreakdown (`2026-03-29`)
  - Phase 1+2: dead code removal, page fixes, advanced stats, contracts tab (`2026-03-28`)

#### 🐛 Bug Fixes

  - stop appending mlbId to player slugs for non-disambiguated players (`2026-03-29`)
  - include 0-AB games with walks/HBP/SF/SB in data pipeline (`2026-03-29`)
  - use current-year-only for counting stat leaders, rolling for rate stats (`2026-03-29`)
  - team logos 404 (ESPN size 40 invalid) + early-season leader fallback (`2026-03-29`)
  - Fix 3 career stat bugs: classifyPlayerType drops 0-AB games, career OBP uses wrong field name, career SLG uses missing singles field (`2026-03-29`)
  - filter All-Star game keys (AL/NL) from rolling stats computation (`2026-03-28`)
  - Fix consistency/streaks: calculate OPS from raw counting stats in game logs (`2026-03-28`)

#### 📝 General

  - disambiguate players with same name using mlbId (`2026-03-29`)
  - use MLB qualifier-rate scaling for leader minimums instead of year fallback (`2026-03-29`)
  - Add min thresholds for League Leaders, fix defaults for stats leaders endpoint (`2026-03-28`)
  - Recalibrate consistency for per-game OPS variance, fix periodOPS to aggregate raw stats (`2026-03-28`)
  - temp (`2026-03-28`)

</details>

---

<!-- CHANGELOG:END -->

The Cycle pulls data from the MLB Stats API and processes it into a full-featured analytics dashboard. During the season, it monitors live games every 2 minutes and automatically ingests completed box scores — no manual updates needed.

**What it does:**
- **Live Scoreboard** — Real-time scores and game states across the league
- **Player Profiles** — Season stats, advanced metrics (wOBA, FIP, BABIP), and a proprietary Cycle Value Rating (CVR) that grades overall player value
- **Splits Explorer** — Situational breakdowns (home/away, vs L/R, by count, runners on, etc.) for both players and teams
- **Pitch Analysis** — Pitch-type breakdowns, zone heatmaps, and spray charts built from play-by-play data
- **Team Analytics** — Roster overviews, team batting/pitching profiles, and head-to-head matchup history
- **Standings & Leaderboards** — League-wide rankings with sortable stat categories

#### Scheduled Tasks (Server: UTC | CST)

| Task | UTC | CDT (UTC-5) | Description |
|------|-----|-------------|-------------|
| Live game monitor | Every 90s | Every 90s | Polls MLB schedule, ingests boxscores + splits when games go final |
| Season refresh | 4:00 AM | 11:00 PM | Full re-pull of current season boxscores |
| Player index rebuild | 4:30 AM | 11:30 PM | Rebuilds searchable player index |
| Splits refresh | 5:00 AM | 12:00 AM | Full play-by-play splits recompute for current season |

```
Stack: React · Node.js · Express · Redis · Nginx · MLB Stats API
```

---

## 🎯 About Me

I'm an Inbound Team Leader at Target with a passion for building software that makes complex data accessible. 

- 🎯 **Current Role:** Inbound Team Leader at Target
- 🚀 **Career Goal:**  Building the bridge between complex data and intuitive information systems
- 🔨 **Building:** [The Cycle](https://thecycle.online) — a self-updating MLB analytics platform
- 🌱 **Learning:** Frontend architectures · GenAI techniques · Advanced system design
- 💡 **Specialty:** Turning messy data into clean, useful interfaces

## 💼 Professional Focus

<div align="center">

| 🎯 Software Engineering | ☁️ Cloud & Infrastructure | 📊 Data & Analytics |
|------------------------|---------------------------|---------------------|
| Full-Stack Development | Linux VPS & Nginx | Statistical Computing |
| AI-Assisted Architecture | Performance Optimization | Predictive Analytics |
| Enterprise Systems | CI/CD & DevOps | Real-Time Data Processing |
| RESTful API Design | Redis Caching Strategies | Business Intelligence |

</div>

## 🎓 Core Competencies

- **AI-Accelerated Development:** Advanced prompt engineering and AI-assisted architecture design for rapid application development
- **Full-Stack System Architecture:** Building scalable platforms processing millions of data points with sub-millisecond response times
- **Performance Engineering:** Redis batch operations, parallel processing, and optimization techniques achieving significant load time reductions
- **Cloud Infrastructure & DevOps:** Linux server management, Nginx, Docker containerization, Redis caching, and automated deployment pipelines
- **Data Pipeline Development:** Real-time APIs, statistical computing, and automated data ingestion from external sources
- **Cross-Functional Leadership:** 6+ years driving operational excellence and mentoring high-performing teams
- **Proprietary Algorithm Development:** Creating analytical systems that integrate multiple data sources into composite player and team valuations

---

## 📊 GitHub Statistics

<div align="center">
  
  ![GitHub Stats](https://github-readme-stats.vercel.app/api?username=c-tram&show_icons=true&theme=dark&hide_border=true&count_private=true&bg_color=0d1117&title_color=f0e68c&text_color=c9d1d9&icon_color=f0e68c)
  
  ![Top Languages](https://github-readme-stats.vercel.app/api/top-langs/?username=c-tram&layout=compact&theme=dark&hide_border=true&bg_color=0d1117&title_color=f0e68c&text_color=c9d1d9)
  
  ![GitHub Streak](https://github-readme-streak-stats.herokuapp.com/?user=c-tram&theme=dark&hide_border=true&background=0d1117&ring=f0e68c&fire=f0e68c&currStreakLabel=f0e68c)

</div>

## 📈 Contribution Activity

![Activity Graph](https://github-readme-activity-graph.vercel.app/graph?username=c-tram&theme=github-dark&hide_border=true&area=true&bg_color=0d1117&color=f0e68c&line=f0e68c&point=c9d1d9)
