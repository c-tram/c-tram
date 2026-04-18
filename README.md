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

<sub>Auto-updated changelog • Last updated: 2026-04-18 • 50 recent changes</sub>

<details>
<summary><b>📋 Recent Development Activity</b></summary>

#### 📊 Advanced Metrics

  - dynamic Stuff+ baselines, Explorer page, dashboard highlights (`2026-04-06`)

#### ⚾ Splits & Pitch Analytics

  - Add hit location field chart to play-by-play expanded detail (`2026-04-18`)
  - Replace game spray chart with live at-bat tracker: strike zone + hit field (`2026-04-17`)
  - Add MLB-style live tracker + game spray chart to Boxscore (`2026-04-17`)
  - Add Matchups tab to Boxscore — career batter vs pitcher stats for both lineups (`2026-04-17`)
  - Add pitcher matchup info to Dashboard ticker and Scores page (`2026-04-15`)
  - restore: bring back SprayChart page (`2026-04-15`)
  - comprehensive bug fixes - player slugs, ROY filter, dashboard, SprayChart removal, 19 files (`2026-04-15`)
  - Add park-specific stadium filter to Spray Chart Explorer (`2026-04-12`)
  - Merge LiveGames into Scores, extract SprayChartExplorer for PlayerDetail (`2026-04-12`)
  - Fix all 5 functional issues: HotColdStreaks loading, SprayChart scatter dots + season toggle, FantasyDFS search/filter/pagination, LiveGames analytical depth (`2026-04-12`)
  - Major UI overhaul: ESPN/PitcherList-quality visual upgrade (`2026-04-11`)
  - massive feature drop - 11 new features including Live Games, Compare, Spray Charts, Streaks, Fantasy/DFS, Sequencing, Tunneling, Expected Stats (`2026-04-10`)
  - parse TEAM-Name in opponentPitcher correctly (don't normalize hyphens) (`2026-04-10`)
  - WPA-based improbable wins, PitcherStuffCard, Explorer UX polish (`2026-04-08`)
  - revamp dashboard highlights — nastiest pitches, longest HRs, luckiest wins (`2026-04-07`)

#### 🎨 Frontend & UX

  - Redesign Scores page: MLB-app style date ribbon + linescore cards (`2026-04-18`)
  - Overhaul Boxscore: MLB-style layout, 6 tabs → 3 (`2026-04-17`)
  - Fix matchups tab: reset data when switching games, prevent stale cache (`2026-04-17`)
  - Remove mobile bottom tab bar — sidebar handles navigation (`2026-04-17`)
  - Dashboard ticker: always show day navigation arrows (`2026-04-15`)
  - Allow viewing tomorrow's and future games in Dashboard and Scores (`2026-04-15`)
  - Teams: fix division card scrolling, remove filler info (`2026-04-15`)
  - Player detail: replace Trends & Projections with compact Streaks card (`2026-04-15`)
  - dashboard — remove breadcrumb tag, remove freshness card, link highlights to at-bat explorer (`2026-04-15`)
  - remove Dashboard league context cards section (`2026-04-15`)
  - Explorer redesign + ComparePlayers analytics + FantasyDFS enhancements + sidebar fix (`2026-04-12`)
  - data loading bugs + full UI overhaul for new feature pages (`2026-04-11`)
  - tunneling/xStats field mismatches, dashboard cleanup, DFS timeout, explorer UI polish (`2026-04-10`)
  - career matchup history + fix dashboard deep links (`2026-04-10`)
  - Wave 2: commercial polish — remove dev strings, version suffixes, console.logs, tech stack exposure, hardcoded year, soften proprietary language, fix timezone, bump ticker fonts, clean dashboard (`2026-04-05`)
  - wave1: UX polish - silent refresh, boxscore fixes, remove dev tells, standardize fallbacks (`2026-04-05`)

#### ⚡ API & Performance

  - add QA test suite — API smoke tests, route consistency, page render tests (`2026-04-15`)
  - single SCAN for career matchup instead of per-player SCAN (`2026-04-10`)
  - per-worker cache warming + in-memory cache in getRollingPlayerDataCached (`2026-04-05`)
  - increase SCAN COUNT from 200 to 10000 (0.6s vs 5.5s on 1.17M keys) (`2026-04-05`)
  - add ip_hash sticky sessions to nginx upstream (`2026-04-05`)

#### 🔧 Data Pipeline

  - players page — remove breadcrumb, merge dual search bars, flatten controls into single toolbar (`2026-04-15`)
  - Phase 3-5 overhaul: Salary Explorer, Awards Tracker, CountUp animations, favorites, CompareTeams, DFS scatter, ComparePlayers 4-player, league context, data freshness (`2026-04-14`)
  - Wave 3: player & team detail polish — fix fallbacks (---/?/N-A → em dash), remove version suffixes, sanitize error messages, fix dateRange labels, remove dev text (Log5/mlbId), park factor labels, dead code cleanup (`2026-04-05`)

#### 🏗️ Infrastructure

  - wire QA checks into push.sh — runs before every deploy (`2026-04-15`)

#### 🐛 Bug Fixes

  - fix win-probability/undefined bug + commercial stats.sh analytics (`2026-04-18`)
  - Fix pre-game detection: check linescore.innings instead of truthy linescore (`2026-04-17`)
  - Fix 9 stale-state bugs: key remount on game switch, ref-sync on auto-refresh, stable callback (`2026-04-17`)
  - remove duplicate lines in HotColdStreaks causing syntax error (`2026-04-15`)

#### 📝 General

  - Game click opens Boxscore dialog directly, skip inline analytics panel (`2026-04-18`)
  - Replace letter grades with numerical rankings (teams 1-30, players 1-N with T-prefix ties) (`2026-04-14`)
  - Comprehensive frontend audit fixes: data integrity, mobile UX, accessibility (`2026-04-12`)
  - Replace logo with square gradient version across entire app (`2026-04-06`)
  - revert: SCAN back to KEYS — SCAN overhead worse than blocking at this scale (`2026-04-05`)

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
