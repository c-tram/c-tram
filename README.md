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

<sub>Auto-updated changelog • Last updated: 2026-04-21 • 48 recent changes</sub>

<details>
<summary><b>📋 Recent Development Activity</b></summary>

#### 💰 Trade Intelligence

  - Fix wRC+/wOBA sort, add CVR+aCVR chips, expose acvr in leaders endpoint (`2026-04-21`)

#### ⚾ Splits & Pitch Analytics

  - Fix hero stats: fetch full player profile for complete batting/pitching stats (`2026-04-21`)
  - Live AB tracking: currentPlay in boxscore API + pulsing in-progress AB card in play-by-play (`2026-04-21`)
  - Add at-bat/pitch explorer popup to matchup history tables (`2026-04-18`)
  - Add hit location field chart to play-by-play expanded detail (`2026-04-18`)
  - Replace game spray chart with live at-bat tracker: strike zone + hit field (`2026-04-17`)
  - Add MLB-style live tracker + game spray chart to Boxscore (`2026-04-17`)
  - Add Matchups tab to Boxscore — career batter vs pitcher stats for both lineups (`2026-04-17`)
  - Add pitcher matchup info to Dashboard ticker and Scores page (`2026-04-15`)
  - restore: bring back SprayChart page (`2026-04-15`)
  - comprehensive bug fixes - player slugs, ROY filter, dashboard, SprayChart removal, 19 files (`2026-04-15`)

#### 🎨 Frontend & UX

  - Fix hero search: consolidated player stats map so all stats show regardless of top-10 rank (`2026-04-21`)
  - Task 4: StatMuse hero search — autocomplete + giant hero stat + secondaries on Dashboard (`2026-04-21`)
  - Fix live AB card: always show currentPlay, dedup vs completed, 5s poll, LAST AB state (`2026-04-21`)
  - Fix player slug navigation in Leaders — use name-only slug, add slug field to leaders response (`2026-04-21`)
  - Task 2 polish: 2-row controls layout, stepper qualifier, stable useCallback filter, fixed imports (`2026-04-21`)
  - correct all backend data shapes across every page — standings divisions, game team objects, dashboard leaders, leaderboard normalization, players stat flattening, salaries data key, stuffplus leaders key (`2026-04-20`)
  - move expected stats card to overview tab (`2026-04-20`)
  - add Savant-style percentile bars to expected stats card (`2026-04-20`)
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

#### ⚡ API & Performance

  - use mlb-live schedule API instead of Redis cache for scoreboard; fix game key to use id (`2026-04-20`)
  - wire all pages to real backend endpoints with correct routes and response shapes (`2026-04-20`)
  - update nginx root to web-frontend-v2/dist (`2026-04-20`)
  - complete web-frontend-v2 rebuild - all 18 pages, full MLB analytics platform (`2026-04-20`)
  - add QA test suite — API smoke tests, route consistency, page render tests (`2026-04-15`)

#### 🔧 Data Pipeline

  - players page — remove breadcrumb, merge dual search bars, flatten controls into single toolbar (`2026-04-15`)

#### 🏗️ Infrastructure

  - nginx.conf must include sites-enabled for SSL/443 to bind (`2026-04-21`)
  - wire QA checks into push.sh — runs before every deploy (`2026-04-15`)

#### 🐛 Bug Fixes

  - Fix boxscore live updates: poll all tabs, use fresh linescore for score/inning display (`2026-04-21`)
  - replace index.css with actual Tailwind + design system styles (`2026-04-20`)
  - proper title and favicon in index.html (`2026-04-20`)
  - fix win-probability/undefined bug + commercial stats.sh analytics (`2026-04-18`)
  - Fix pre-game detection: check linescore.innings instead of truthy linescore (`2026-04-17`)
  - Fix 9 stale-state bugs: key remount on game switch, ref-sync on auto-refresh, stable callback (`2026-04-17`)
  - remove duplicate lines in HotColdStreaks causing syntax error (`2026-04-15`)

#### 📝 General

  - Task 3: MLB App scoreboard — team color accent bars, enlarged diamond panel, inning half ▲▼, team-colored WP bar (`2026-04-21`)
  - Task 2: FanGraphs-style dense leaderboard — col groups, team filter, min PA/IP, active sort column band (`2026-04-21`)
  - revert: serve web-frontend v1 build (`2026-04-21`)
  - Game click opens Boxscore dialog directly, skip inline analytics panel (`2026-04-18`)

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
