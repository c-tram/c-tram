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

<sub>Auto-updated changelog • Last updated: 2026-04-02 • 50 recent changes</sub>

<details>
<summary><b>📋 Recent Development Activity</b></summary>

#### 💰 Trade Intelligence

  - recompute ACVR in rolling stats from blended universalCVR (`2026-04-01`)
  - add WAS→WSH alias in salary ingestion for ACVR calculation (`2026-03-31`)
  - Fix rolling CVR: only override CVR/ACVR (not rWAR/stats) + career splits + vs_batters for pitchers (`2026-03-30`)
  - Add Stats Glossary page + ACVR column in Trades dashboard (`2026-03-30`)
  - Add team control column to Trade Assets roster table (`2026-03-30`)
  - recompute team CVR/ACVR in post-processing reaggregation (`2026-03-30`)
  - preserve acvr + teamPayroll in post-processing team reaggregation (`2026-03-30`)
  - proper team CVR (raw performance) + ACVR (bang for buck) (`2026-03-30`)
  - team CVR uses avgACVR (0-100 scale) + sort no longer resets view (`2026-03-30`)
  - overhaul: trade value engine v4 — prior-year benchmarks + simple pace scaling (`2026-03-29`)

#### 📊 Advanced Metrics

  - ui: new Cycle logo, remove bWAR/pWAR boxes, remove teams search bar (`2026-04-02`)
  - Convert Glossary to floating popup + switch all CVR to rolling window (`2026-03-30`)
  - WAR to 2 decimal places everywhere (player detail, team detail, roster, contracts, backend storage) (`2026-03-30`)
  - show WAR with 2 decimal places on team detail page (`2026-03-30`)
  - contracts tab crash (typeof null === 'object') + enrich contracts with WAR/CVR (`2026-03-30`)
  - single rolling fetch + fix wOBA field casing (`2026-03-30`)

#### ⚾ Splits & Pitch Analytics

  - aggregatePitchingTotals NaN bug - use safe ( (`|0) accumulation|2026-04-01`)
  - pitcher splits explorer - backend merge + pitching stat columns (`2026-04-01`)
  - Fix 6 issues: boxscore mobile scroll, pitching leaders fallback, players/teams mobile card layouts, standings clickable teams + postseason error handling (`2026-04-01`)
  - per-game pitch analysis filter on player detail page (`2026-03-31`)
  - remove: /splits page (covered by player detail) (`2026-03-31`)
  - add play-by-play coverage note to career splits (`2026-03-30`)
  - restore G column in splits, remove game counts from gameType filter buttons (`2026-03-30`)
  - career splits gameType filtering + remove games column from splits (`2026-03-30`)
  - proper gameType filtering for all splits sections (`2026-03-30`)
  - supplement on-the-fly splits with precomputed play-by-play data for vs_pitchers/vs_batters (`2026-03-30`)

#### 🎨 Frontend & UX

  - update header: use new logo SVG + navy-to-orange text gradient (`2026-04-02`)
  - dashboard leaders use season stats + game type filter aggregates all game logs (`2026-04-02`)
  - Fix navigation: back buttons use history, tab switches use replace (`2026-04-01`)
  - Add inning to dashboard scoreboard + boxscore popup on click (`2026-04-01`)
  - mobile UX + spring training leaders bug (`2026-03-31`)
  - mobile UX improvements, remove search/compare, fix standings filtering (`2026-03-31`)
  - mobile-first responsive overhaul across all pages (`2026-03-31`)
  - Mobile responsiveness fixes across 6 pages (`2026-03-30`)
  - Mobile responsiveness: comprehensive useMediaQuery guards across all pages (`2026-03-30`)
  - composite /dashboard endpoint — 13 API calls → 1 (`2026-03-30`)

#### 🔧 Data Pipeline

  - Fix salary mismatch: always prefer full salary-key totals on /teams listing, add WAS/WSH team code mapping (`2026-04-02`)
  - Fix horizontal scroll overflow across all pages (`2026-04-01`)
  - add team alias mapping for salary lookups (ATH↔OAK, AZ↔ARI, WSH↔WAS) (`2026-03-31`)
  - add hover tooltips explaining every stat column on Teams page (`2026-03-30`)

#### 🐛 Bug Fixes

  - Fix counting stats missing in slim mode + mobile stat group buttons (`2026-04-01`)
  - boxscore date bug — use officialDate instead of UTC gameDate (`2026-03-31`)
  - Fix UTC date rollover bug in live game monitor (`2026-03-31`)
  - close JSX comment syntax (`2026-03-31`)
  - show hamburger menu icon on desktop (`2026-03-31`)
  - stop appending mlbId to player slugs for non-disambiguated players (`2026-03-29`)

#### 📝 General

  - update favicon.ico to match new logo (`2026-04-02`)
  - Leaders: use full season stats instead of rolling window (`2026-03-31`)
  - Date range dropdown for PlayerDetail + move glossary to chat position (`2026-03-30`)
  - Replace calendar date range picker with simple dropdown presets (7d/14d/30d) (`2026-03-30`)

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
