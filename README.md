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

<sub>Auto-updated changelog • Last updated: 2026-03-27 • 44 recent changes</sub>

<details>
<summary><b>📋 Recent Development Activity</b></summary>

#### 💰 Trade Intelligence

  - Fix 7 trade engine bugs: double period, value bar negatives, narrative rendering, stale breakdown keys, futureOutlook surplus, control years fallback, multi-team support (`2026-03-24`)
  - active roster filtering for trade analysis (`2026-03-23`)
  - Next-best option analysis, control in DTV, improved roster depth (`2026-03-23`)
  - Redesign Trade Results & Compare UI: charts, structured narrative, fix negative bar bug (`2026-03-23`)
  - Fix trade dashboard: remove Players Ranked/Negative boxes, fix category filter TV bug (client-side filtering) (`2026-03-23`)
  - Redesign Mock Trade UI: team dropdowns → roster player selection (`2026-03-23`)
  - Prominent Benchmarks hero card on Dashboard + URL tab routing for Trades (`2026-03-22`)
  - Highlight key pages — DTV hero banner, featured nav cards, sidebar badges (`2026-03-22`)
  - Position-based trade target suggestions in Mock Trade Builder (`2026-03-22`)
  - Replace trade engine with DTV-powered v3 — full integration (`2026-03-22`)
  - Dynamic Trade Value (DTV) system - positional benchmarks, market rates, team-context impact analysis (`2026-03-22`)
  - Trades UI v4: negative values, decline tax, full aesthetic refresh (`2026-03-22`)
  - Trades v3.1: Polish Mock Trades, Leaderboard, Compare tabs to match app aesthetic (`2026-03-21`)

#### 📊 Advanced Metrics

  - Fix 6 bugs: run differential, leaders, pitch analysis, game type classification, CVR thresholds (`2026-03-27`)
  - Remove Splits tab from TeamDetail, add bWAR/pWAR tooltips (`2026-03-24`)
  - Fix TV=8/10 bug: benchmark cache collision, remove hero tiles, fix team sort order (`2026-03-23`)

#### ⚾ Splits & Pitch Analytics

  - Fix pitch events merge duplication + add --clean flag for reingest (`2026-03-27`)
  - Simplify Splits tab to All Splits only + fix gt field missing from pitch events API (`2026-03-27`)
  - pitch analysis spring training ingestion + advanced analytics filter recalculation (`2026-03-27`)
  - frontend pitch analysis now fetches all game types for client-side filtering (`2026-03-27`)
  - zone heatmap and pitch arsenal re-aggregation - zones: use 'pitches' field (not 'count'), add swingRate/whiffRate/hitRate/inPlay/hits - zones: convert pre-aggregated object to array for ZoneHeatmap component - pitchTypes: add usage%, swingRate, contactRate, spin, maxVelo, battingAvg - pitchTypes: track pitch name from events (`2026-03-24`)

#### 🎨 Frontend & UX

  - Fix double *100 on kRate/bbRate in AdvancedAnalyticsTab filtered view (`2026-03-27`)
  - Dashboard: dynamic leader thresholds + AL/NL standings filter (`2026-03-26`)
  - Fix dashboard: show 2026 data when teams have games played (`2026-03-26`)
  - comprehensive mobile responsiveness pass across all pages (`2026-03-25`)
  - Merge leaderboard into dashboard with sortable table, filters, consistent tab headers (`2026-03-23`)
  - Smooth scoreboard refresh — no more layout bounce (`2026-03-23`)

#### ⚡ API & Performance

  - Fix --clean flag to use script's own Redis client instead of utils module (`2026-03-27`)
  - normalize team abbreviations for Redis key lookups (ARI→AZ, OAK→ATH) (`2026-03-25`)

#### 🔧 Data Pipeline

  - Fix reaggregation: reset teams with 0 regular season games instead of skipping (`2026-03-27`)
  - Fix reaggregation: also check flat date/gameType fields for older spring training games (`2026-03-27`)
  - Fix reaggregation: filter to regular season only, exclude spring training/postseason (`2026-03-27`)
  - Fix ARI/OAK salary normalization: bridge ARI↔AZ and OAK↔ATH team codes (`2026-03-27`)
  - Fix spring_training misclassification on player detail pages (`2026-03-26`)
  - add game type filtering to team reaggregation (`2026-03-26`)
  - data engine now monitors and ingests ALL game types (not just Regular Season) (`2026-03-22`)

#### 🐛 Bug Fixes

  - move useMemo before early return to fix React hooks order (`2026-03-27`)
  - Fix hasRegularSeasonGames: empty {} gameTypeBreakdown should fallback to gameCount (`2026-03-26`)
  - Fix classifyGameType: March 25+ is regular season, prefer stored gameType (`2026-03-26`)
  - default year switches to current year on March 25 (regular season start) (`2026-03-26`)
  - game type detection for 2026 regular season (`2026-03-26`)
  - prefer stats-year data over current-year Spring Training for roster fallback (`2026-03-23`)

#### 📝 General

  - Add batter silhouette to zone charts, dynamic Inside/Outside labels by handedness (`2026-03-27`)
  - proper game type system across all data layers (`2026-03-27`)

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
