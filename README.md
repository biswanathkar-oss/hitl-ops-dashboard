⚡ HiTL Ops Command Centre
> **Interactive Human-in-the-Loop Data Labelling Operations Dashboard**
🔗 Launch Live Demo
![Demo Mode](https://img.shields.io/badge/Mode-Demo-7c3aed?style=flat-square)
![Agents](https://img.shields.io/badge/Agents-80-00e5ff?style=flat-square)
![Projects](https://img.shields.io/badge/Projects-15-f97316?style=flat-square)
![Agencies](https://img.shields.io/badge/Agencies-2-10b981?style=flat-square)
![Stack](https://img.shields.io/badge/Stack-HTML%20%2B%20Vanilla%20JS-fbbf24?style=flat-square)
---
What is this?
A fully interactive HiTL (Human-in-the-Loop) annotation operations dashboard built to simulate real-world data labelling pipeline management at scale — the kind used in AI/ML product development across e-commerce, autonomous vehicles, NLP, and medical imaging.
The dashboard models the operational complexity of managing 80 annotation agents across 2 vendor agencies, running 15 concurrent labelling projects with live SLA tracking, idle risk prediction, priority-driven reallocation, and configurable cost modelling.
Built as a portfolio artefact demonstrating senior-level Product & AI/ML Operations thinking — system design, capacity planning, vendor management, quality ops, and data-driven decision-making.
---
🖥 Dashboard Tabs
Tab	What it does
📊 Overview	Live KPI strip — active agents, P0 projects, idle risk count, overall progress. Project progress cards with per-project idle alerts. Agent utilisation doughnut.
📁 Projects	Full project table with REQ ID ↔ PRJ ID mapping, team, priority, SLA dates, days-to-complete, risk status, progress bars
🔀 Reallocation	Move agents between projects — configurable transfer %, ramp period (0–10 days), transfer strategy. Live impact preview shows TPD delta and SLA risk before confirming
💰 Cost Config	Toggle per agency between HC-Based / Transaction / Hybrid billing. Edit rate card (₹ per unit by task type and complexity). Rush premium toggle. Live cost recomputation
🏢 Agency	Agency Alpha vs Beta health panels — utilisation %, QA pass rate, SLA adherence, vendor health score. Per-project agency contribution with dependency risk flags
⚠ Idle Risk	7-day heatmap showing which projects run out of work (✓ Active / ⚠ Last day / IDLE). Recommended redeployment actions per project
🔍 QA / Rework	Batch QA log — auto-computed 5% sample size, error rates, PASS/FAIL, rework trigger (>10% error threshold), root cause tracking
📄 Reports	Custom CSV report builder — 20 selectable fields, filter by status/priority/risk, one-click download
---
🔑 Key Operational Concepts Demonstrated
Priority-driven allocation
P0 projects claim agents first. High-APT tasks (≥18 min/unit, e.g. Lidar at 65 min/frame) get 100% dedicated agents — no splitting. Low-APT tasks (e.g. Sentiment at 1.5 min/post) allow 70/30 primary/secondary splits across two projects.
Idle risk prediction
Days-to-complete = Remaining Units ÷ (Agents × Net Units/Agent/Day). Dashboard warns 7 days before a project runs out of work — giving Ops Manager time to redeploy agents rather than paying for idle hours.
Net productive minutes model
`NET_MINS = 480 × 85% efficiency × (1 − 2% shrinkage) = 399.84 mins/agent/day`
All throughput calculations use this, not raw shift hours.
Partial reallocation with ramp modelling
When agents move projects, a configurable ramp period (0–10 days) applies a reduced starting efficiency (default 40%), stepping up to 85%. This prevents over-optimistic TPD projections for newly assigned agents.
Dual agency cost modelling
HC-Based: fixed monthly retainer per agent (₹45,000 Alpha / ₹38,000 Beta)
Transaction: per-unit rate card by task type and complexity
Hybrid: fixed base % + transaction for remainder — configurable split slider
5% QA sampling
Every project has 5% of labelled output sampled by a dedicated QC agent. Error rate >10% triggers full batch rework. QC agents (4 of 80) are reserved separately from the labeller pool.
---
🏗 System Architecture (Production Version)
The production version connects to a Google Sheets backend via the Sheets API. The spreadsheet has 18 tabs:
```
CONFIG                   Global assumptions (efficiency, shrinkage, SLA targets)
1_Request_Tracker        Data scientists submit labelling requests here
2_TnM_APT_Repository     Time & Motion study — avg processing time per task type
3_Agent_Roster           80 agents — leave, shift, specialisation, availability
4_Project_Distribution   Project-level allocation, SLA tracking, rework log
5_Capacity_Calendar      Week-by-week agent availability (W/L/T/M/D/H codes)
6_Predictive_Planning    Formula-driven SLA computation, idle alerts, HC gap
7_Allocation_Matrix      80 × 15 agent-project allocation (priority-driven)
8_Rework_QA_Log          QA batch records, error rates, rework triggers
9_Ops_Summary            Daily standup snapshot — live COUNTIF/SUMIF KPIs
DASHBOARD_DATA           Flat one-row-per-project feed for dashboard reads
AGENT_ALLOCATION_FLAT    Long-format agent×project table for write-back
AGENCY_VENDOR_TRACKER    Agency utilisation, cost, QA, vendor health score
RATE_CARD                Editable billing model config + transaction rate card
REQ_PRJ_MAPPING          Request ID ↔ Project ID lookup
AGENT_DAILY_LOG          Daily timesheet — active/idle/downtime/output per agent
UTILISATION_SUMMARY      Agent-level performance scores and cost efficiency
PROJECT_UTILISATION      Project-level hour efficiency and cost-per-unit analysis
```
Dashboard actions write back to Sheets via OAuth 2.0:
Reallocation confirm → appends to `AGENT_ALLOCATION_FLAT`, updates `DASHBOARD_DATA`
Rate card edits → overwrites `RATE_CARD`
Cost config changes → writes billing model and rates to `RATE_CARD`
---
📐 Core Formulas
```
Utilisation %       = Active Hours / Total Logged Hours
Idle %              = Idle Hours / Total Logged Hours
Downtime %          = (Tool DT + Task Queue Empty) / Total Logged Hours
Availability %      = Days Present / Scheduled Days
Hourly Throughput   = Units Completed / Active Hours
Task Completion %   = Units Completed / Units Attempted
Cost per Unit       = (Hours Logged × Hourly Rate) / Units Completed
Wasted Pay %        = Idle Cost / Gross Pay
SLA (days)          = CEILING(Remaining Units / Daily Throughput, 1)
Performance Score   = Utilisation(35%) + QA Pass(30%) + Throughput Index(20%) + Availability(15%)
```
---
🛠 Tech Stack
Layer	Technology
Frontend	Pure HTML5 + CSS3 + Vanilla JavaScript
Charts	Chart.js 4.4
Backend (prod)	Google Sheets API v4
Auth (prod)	Google OAuth 2.0 (GIS)
Hosting	GitHub Pages
Fonts	Google Fonts — DM Sans + Space Mono
No frameworks. No build step. No dependencies beyond Chart.js. Opens as a single HTML file.
---
🚀 Run Locally
```bash
# Clone
git clone https://github.com/biswanathkar-oss/hitl-ops-dashboard.git
cd hitl-ops-dashboard

# Open directly in browser
open index.html

# Or serve locally (avoids any CORS issues)
python3 -m http.server 8080
# Then visit http://localhost:8080
```
---
📌 Demo vs Production
Feature	Demo (this repo)	Production
All 8 dashboard tabs	✅	✅
Reallocation panel	✅ In-memory	✅ Writes to Sheets
Cost config + rate card	✅ In-memory	✅ Writes to Sheets
CSV report download	✅	✅
Google Sign-in	❌ Not required	✅ OAuth 2.0
Live Sheets read	❌ Baked-in data	✅ Reads on load
Write-back	❌	✅
Data persistence	❌ Resets on refresh	✅
---
👤 Author
Biswanath Kar
Senior Product & AI/ML Operations Leader | 20+ years across Search, Catalog, NLP, Computer Vision
![LinkedIn](https://img.shields.io/badge/LinkedIn-biswanathkar-0077B5?style=flat-square&logo=linkedin)
![GitHub](https://img.shields.io/badge/GitHub-biswanathkar--oss-181717?style=flat-square&logo=github)
---
📄 Licence
MIT — free to use, adapt, and build on with attribution.
