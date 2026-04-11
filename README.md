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

<sub>Auto-updated changelog • Last updated: 2026-04-11 • 50 recent changes</sub>

<details>
<summary><b>📋 Recent Development Activity</b></summary>

#### 💰 Trade Intelligence

  - Frontend polish: SettingsContext, expanded Settings page, CompareYears tab, DTV→CVR branding, Navigation breadcrumb (`2026-04-04`)
  - update Trades page to CTS 6-pillar system - fix broken mock trade evaluation (`2026-04-04`)
  - Trade engine overhaul: 6-pillar CVR Trade Score (CTS) replacing WAR-based scoring. Pillars: CVR Production 30%, ACVR Efficiency 20%, Versatility 10%, Scarcity 15%, Age Trajectory 15%, Team Control 10%. Adds positionsPlayed tracking, CVR/ACVR benchmarks, fixes position overwrite bug, updates frontend for 0-100 scale. (`2026-04-04`)

#### 📊 Advanced Metrics

  - dynamic Stuff+ baselines, Explorer page, dashboard highlights (`2026-04-06`)

#### ⚾ Splits & Pitch Analytics

  - Major UI overhaul: ESPN/PitcherList-quality visual upgrade (`2026-04-11`)
  - massive feature drop - 11 new features including Live Games, Compare, Spray Charts, Streaks, Fantasy/DFS, Sequencing, Tunneling, Expected Stats (`2026-04-10`)
  - parse TEAM-Name in opponentPitcher correctly (don't normalize hyphens) (`2026-04-10`)
  - WPA-based improbable wins, PitcherStuffCard, Explorer UX polish (`2026-04-08`)
  - revamp dashboard highlights — nastiest pitches, longest HRs, luckiest wins (`2026-04-07`)
  - Fix pitcher movement chart + full zone layout in CompareYears (`2026-04-05`)
  - CompareYears: add Pitch Analysis mode with multi-year whiff/contact tracking (`2026-04-04`)
  - Add Stuff+ metric for pitchers (L/R handedness-aware) (`2026-04-04`)
  - Add hB, vB, spin to play-by-play pitch-by-pitch table (`2026-04-04`)
  - Live game enhancements: runners/outs/count on scoreboard cards + interactive play-by-play with pitch location charts (`2026-04-04`)
  - pitcher-vs-lineup shows batting stats (PA, AVG, OBP, HR, H, K%, BB%) instead of pitching stats (`2026-04-03`)
  - pitcher-vs-lineup matchup shows pitching stats (IP/H/K/BB/ERA) instead of all zeros (`2026-04-03`)
  - Phase 4: Player Matchup Splits (`2026-04-03`)
  - InteractiveZoneChart zone width ±0.833ft + hB/vB aggregation in PitchAnalysisTab (`2026-04-03`)
  - Add all pitch event fields + vs-player filter for At-Bat Explorer (`2026-04-02`)
  - Rename toggle to Pitching/Batting, fix opponent hand label (RHB vs RHP) (`2026-04-02`)
  - At-Bat Explorer - pitch-by-pitch at-bat viewer with zone chart (`2026-04-02`)
  - correct PA inflation in splits ingestion (`2026-04-02`)
  - Fix splits: AB-based matchup stats for pitcher vs_batters/handedness and batter vs_pitchers, fix OBP to include HBP (`2026-04-02`)
  - Fix pitcher splits IP parsing: handle decimal vs baseball notation, proper IP display format (`2026-04-02`)
  - Fix pitcher splits: expand isPitchingSection to include vs_handedness/vs_batters, add location aggregation to extractPitching (`2026-04-02`)

#### 🎨 Frontend & UX

  - data loading bugs + full UI overhaul for new feature pages (`2026-04-11`)
  - tunneling/xStats field mismatches, dashboard cleanup, DFS timeout, explorer UI polish (`2026-04-10`)
  - career matchup history + fix dashboard deep links (`2026-04-10`)
  - Wave 2: commercial polish — remove dev strings, version suffixes, console.logs, tech stack exposure, hardcoded year, soften proprietary language, fix timezone, bump ticker fonts, clean dashboard (`2026-04-05`)
  - wave1: UX polish - silent refresh, boxscore fixes, remove dev tells, standardize fallbacks (`2026-04-05`)
  - Add team abbreviations to win probability bars on scoreboard cards (`2026-04-04`)

#### ⚡ API & Performance

  - single SCAN for career matchup instead of per-player SCAN (`2026-04-10`)
  - per-worker cache warming + in-memory cache in getRollingPlayerDataCached (`2026-04-05`)
  - increase SCAN COUNT from 200 to 10000 (0.6s vs 5.5s on 1.17M keys) (`2026-04-05`)
  - add ip_hash sticky sessions to nginx upstream (`2026-04-05`)
  - PM2 cluster mode (6 workers), KEYS→SCAN, cache TTL 300s (`2026-04-05`)

#### 🔧 Data Pipeline

  - Wave 3: player & team detail polish — fix fallbacks (---/?/N-A → em dash), remove version suffixes, sanitize error messages, fix dateRange labels, remove dev text (Log5/mlbId), park factor labels, dead code cleanup (`2026-04-05`)

#### 🐛 Bug Fixes

  - remove duplicate pregame WP chip from ticker (`2026-04-04`)
  - drill-down selects correct at-bat by inning, not just first AB of the day (`2026-04-03`)
  - Fix strike zone accuracy: correct width to 17" plate + ball radius, add per-batter szT/szB (`2026-04-02`)
  - perspective logic for two-way players (Kershaw, Ohtani, etc) (`2026-04-02`)

#### 📝 General

  - Replace logo with square gradient version across entire app (`2026-04-06`)
  - revert: SCAN back to KEYS — SCAN overhead worse than blocking at this scale (`2026-04-05`)
  - Teams mobile: grade stats in tinted top block, remaining stats below (`2026-04-04`)
  - pre-game win probability on scheduled games (Log5 model) (`2026-04-04`)
  - Phase 5: Live Win Probability + WPA chart (`2026-04-03`)
  - Phase 3: Projected Record display in OverviewTab (`2026-04-03`)
  - Phase 2: Pre-game Win Probability (Log5 model) (`2026-04-03`)
  - Phase 1: Future Games Scheduler in TeamDetail (`2026-04-03`)
  - rework InteractiveZoneChart - dynamic zone, rich tooltip, drill-down to At-Bat Explorer (`2026-04-03`)

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
