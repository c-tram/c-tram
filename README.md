# Hi there, I'm Cole 👋

<div align="center">
  
  ![Profile Views](https://komarev.com/ghpvc/?username=c-tram&color=1a1a1a&style=flat-square)
  
  **Inbound Team Leader @ Target | Full-Stack Developer**
  
</div>

---

## 🌟 Featured Projects

### ⚾ [The Cycle — MLB Analytics Platform](https://thecycle.online)
> A live MLB analytics platform that automatically tracks every game throughout the season

**See it live:** [thecycle.online](https://thecycle.online)

<!-- CHANGELOG:START -->
### ⚾ The Cycle — MLB Analytics Platform

<sub>Auto-updated changelog • Last updated: 2026-03-23 • 44 recent changes</sub>

<details>
<summary><b>📋 Recent Development Activity</b></summary>

#### 💰 Trade Intelligence

  - Redesign Mock Trade UI: team dropdowns → roster player selection (`2026-03-23`)
  - Prominent Benchmarks hero card on Dashboard + URL tab routing for Trades (`2026-03-22`)
  - Highlight key pages — DTV hero banner, featured nav cards, sidebar badges (`2026-03-22`)
  - Position-based trade target suggestions in Mock Trade Builder (`2026-03-22`)
  - Replace trade engine with DTV-powered v3 — full integration (`2026-03-22`)
  - Dynamic Trade Value (DTV) system - positional benchmarks, market rates, team-context impact analysis (`2026-03-22`)
  - Trades UI v4: negative values, decline tax, full aesthetic refresh (`2026-03-22`)
  - Trades v3.1: Polish Mock Trades, Leaderboard, Compare tabs to match app aesthetic (`2026-03-21`)
  - Trades v3: Complete from-scratch rewrite — fix evaluateTrade bug, match app design language, standings-style team selector, premium charts, intuitive mock trade builder, MUI tables (`2026-03-21`)
  - Trades v2: premium visuals, division cards, glow charts, rank badges (`2026-03-21`)
  - Replace Analytics with premium Trades hub — 5 tabs: Dashboard (bar/scatter/donut charts), Mock Trade Builder (radar eval), Leaderboard (sortable + player detail sidebar), Team Assets (WAR dist + position groups), Player Comparison (head-to-head radar). Powered by WAR v2, CVR v5, ACVR, surplus value, aging curves. Routes /analytics and /trade redirect to /trades. (`2026-03-21`)
  - Add Spotrac contract enrichment: backfill script, pipeline integration, clean trade_v2 fallbacks (`2026-03-20`)
  - Add age to player detail, fix salary labels, enhance analytics leaderboard with age/ACVR/contract (`2026-03-19`)
  - Add age enrichment to ingestion script and prefer cached age in trade API (`2026-03-19`)
  - Fix trade leaders timeout: parallelize MLB API age lookups with concurrency cap (`2026-03-19`)
  - Trade system overhaul: MLB API age enrichment, salary data, share options, player comparison tab (`2026-03-19`)
  - enriched contract intelligence from Spotrac + contract-aware ACVR (`2026-03-19`)
  - Fix 6 issues: warByYear filter+dropdown, pitch analysis game type filter, OPS>1 splits fix, career tab all MLB years+WAR columns, ACVR column on players table, cross-year search fallback, remove wOBA (`2026-03-18`)
  - WAR recalibration, pWAR/ACVR/postseason tracking, DataEngine real-time ingestion, remove SmartDataAutomator (`2026-03-18`)

#### 📊 Advanced Metrics

  - set position before WAR calc in reaggregation path (`2026-03-21`)
  - WAR v2: Hybrid wRAA + positional adjustment + discipline/speed bonuses (`2026-03-21`)
  - recalculateCVR fallback when gameTypeBreakdown missing (`2026-03-19`)
  - preserve modern CVR fields in reaggregation + add recalculateCVR script (`2026-03-19`)
  - Fix career WAR/pWAR: always load all seasons for career totals, filter separately for response (`2026-03-18`)
  - Rework Players page: hero search, WAR/CVR leaderboards, compact layout (`2026-03-18`)
  - Fix CVR: use nullish check so universalCVR=0 doesn't fallback to legacy 0-2 scale (`2026-03-18`)

#### ⚾ Splits & Pitch Analytics

  - Enable daily splits automation in DataEngine (`2026-03-20`)
  - Fix ReferenceError: key not defined in splits_macro_v2.js (`2026-03-20`)
  - Fix pullPlayByPlaySplits: support --types flag for spring training games (`2026-03-20`)
  - Fix left-handed splits: store batSide/pitchHand, fix SprayChart orientation, fix on-the-fly builder (`2026-03-20`)
  - UI cleanup: remove page titles, nav buttons, fix Matchups→Analytics, modernize splits with team mode (`2026-03-18`)

#### 🎨 Frontend & UX

  - Merge leaderboard into dashboard with sortable table, filters, consistent tab headers (`2026-03-23`)
  - Smooth scoreboard refresh — no more layout bounce (`2026-03-23`)
  - Fix: remove middle dot from player cards, fix game type filter refresh (`2026-03-18`)

#### ⚡ API & Performance

  - Fix spring training: add gameTypes to all MLB API calls, fix dataAutomation flags, fix Redis key patterns for ST/postseason (`2026-03-20`)

#### 🔧 Data Pipeline

  - data engine now monitors and ingests ALL game types (not just Regular Season) (`2026-03-22`)
  - Fix controlYears: use contractYearsRemaining from Spotrac contracts (`2026-03-21`)
  - Add Data Engine scheduled task times to README (`2026-03-20`)
  - Fix controlYears: count forward salary keys for FA/extension/foreign contracts instead of returning 0 (`2026-03-20`)
  - Add standalone age backfill script (`2026-03-19`)
  - Fix classifyGameType not defined in reaggregation scope - inline date classification (`2026-03-18`)
  - Fix double-counting: skip season updates for existing games, filter spring training from reaggregation (`2026-03-18`)

#### 🐛 Bug Fixes

  - Remove stray SSH command pasted into source (`2026-03-18`)

#### 📝 General

  - Update GitHub link to profile URL (`2026-03-20`)

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
