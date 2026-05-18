# Work Sample: Maturing Clinical Reporting for Meridian Community Health Alliance on Epic

**Submitted by:** Praveendhra Rajkumar  
**Role:** Report Writer / Applications Developer — Clinical and Enterprise Analytics  
**Organization:** BMCHS (target)  
**Date:** May 2026

---

## Executive Summary

Meridian Community Health Alliance (fictional), a five-site Community Connect spoke on a regional health system's Epic installation, entered its second post-go-live year with 340 unresolved report requests, inconsistent ED throughput metrics across three operational dashboards, and monthly quality reporting consuming 18 analyst-hours per cycle. This proposal outlines how I would assess the current reporting environment, redesign the request workflow, build standardized Reporting Workbench templates and Radar dashboards, enable self-service analytics through Slicer Dicer, and automate recurring report distribution — reducing time-to-delivery by 4x and cutting manual monthly reporting effort to under 4 hours.

---

## Scenario

**Organization:** Meridian Community Health Alliance (fictional)  
**Model:** Community Connect spoke on Northeast Regional Health System's Epic production  
**Sites:** 5 community health centers across Greater Boston  
**Epic modules:** Ambulatory, Emergency, Orders, ClinDoc, MyChart  
**Current state:** 18 months post go-live, first-generation reports built by implementation team  
**Team:** 2 report writers (one vacancy), 3 clinical application analysts, 1 manager

**Scale:**
- 120,000 active patients
- 85,000 encounters/year across all sites
- 40+ clinical departments with active reporting needs
- 8 quality measures required for payer contracts (HEDIS-aligned)

---

## Current State Assessment

### What's broken

| Problem | Evidence | Impact |
|---|---|---|
| 340 outstanding requests | Aging report in ServiceNow shows 60% unactioned for 60+ days | Clinical leadership lacks operational data |
| Metric inconsistency | ED throughput in 3 Radar dashboards uses 3 different date anchors | QI team cannot compare sites accurately |
| Manual monthly reporting | RN quality coordinator spends 18 hrs/month pulling 8 quality reports manually | High error risk, slow feedback cycle |
| No self-service tier | Every data question routes to the analyst queue | Analyst time is consumed by simple lookups |
| Poor spec documentation | Report templates lack data dictionary entries or Chronicles source annotations | Difficult to maintain or hand off |
| Spoke-site data bleed | 2 dashboards missing Department ID filters — pulling host-site data into CC reports | Reports show inflated patient volumes |

### Root cause summary

The implementation team prioritized go-live speed over governance. Reports were built to satisfy individual department requests without a template library, naming convention, or spec documentation standard. The result is a fragmented, un-maintainable reporting environment with no single source of truth.

---

## Proposed Solution

### Four-track approach

**Track 1 — Triage and categorize the backlog**  
Use a request classification matrix to sort all 340 requests into: Standard (can be served by existing template with minor config), New Build (net-new Reporting Workbench template required), Self-Service Redirect (Slicer Dicer can answer this), and Out of Scope / Defer. This alone resolves an estimated 30–40% of the backlog in the first 30 days.

**Track 2 — Fix existing dashboard data quality issues**  
Audit the three ED throughput Radar dashboards. Standardize all ED timing metrics to `Arrival DT` (INP.ARRV.DTTM) as the anchor. Add explicit Department ID list filters to all Community Connect dashboards to prevent host-site data bleed.

**Track 3 — Build a standard template library**  
Develop 12 core Reporting Workbench templates covering the most common request categories (ED operations, ambulatory quality, population health, access/scheduling). Each template gets a spec sheet: Chronicles sources, filters, groupers, and refresh cadence.

**Track 4 — Automate recurring reports**  
Configure Epic Report Broker for the 8 monthly quality reports. Output to a secure SharePoint folder. Cut analyst manual effort from 18 hours to setup and exception-handling only (target: under 4 hours/month).

---

## Security and Compliance

**HIPAA safeguards applied to all reporting work:**

- Every report is scoped to the minimum necessary PHI fields for its stated purpose
- Reporting Workbench templates use Epic Security Points — reports are accessible only to logged-in users with the appropriate security class
- Break-the-Glass access is logged via Epic's audit trail for sensitive population reports (HIV, behavioral health, SUD)
- Automated report delivery via Report Broker only distributes to pre-approved, role-authenticated recipients — no unencrypted email attachments
- Slicer Dicer data models are configured to respect Epic's row-level security (patients do not appear in data models for users outside their care team)
- All report extracts leaving Epic are encrypted in transit via SFTP; no PHI in plain-text CSV over email
- An annual report access audit reviews who has run which reports and flags dormant permissions

---

## Epic Reporting Deep Dive

### Reporting Workbench

Reporting Workbench (RWB) is Epic's primary analyst-facing report authoring tool. Reports are structured around a **primary record type** (e.g., EPT for patients, HAR for hospital accounts, PAT ENC for encounters) with optional related record joins.

Key concepts I work with:

- **Master File (INI) and Item Numbers:** Every data element in Chronicles is referenced by master file and item number. For example, patient name is EPT 6; encounter date is PAT ENC 12060. RWB templates reference these items directly. Knowing the INI/item structure lets me trace exactly where the data originates.
- **Criteria filters:** RWB supports date range filters, department filters, provider filters, and custom value list restrictions. For Community Connect reports, I always add a `Home Department (CC Site)` filter as the first criteria to prevent host data contamination.
- **Groupers and statistics:** RWB produces both list reports (one row per record) and statistics reports (aggregated counts). For quality measure reports, I use statistics mode with a denominator/numerator grouper to produce measure rates directly.
- **Template sharing:** Completed templates are placed in a shared folder accessible to authorized analysts. The template ID is documented in our spec sheet.

### Slicer Dicer

Slicer Dicer is Epic's self-service analytics layer for clinical end users. Users explore data through pre-curated **data model** configurations without writing any queries.

My approach to enabling self-service:
1. Identify the top 20 question types from the past 6 months of ad-hoc requests
2. Map each to an existing Slicer Dicer data model or build a custom one
3. Train department super-users (1–2 per site) on Slicer Dicer navigation
4. Publish a "what Slicer Dicer can answer" one-pager on the intranet

Expected outcome: 30% reduction in analyst queue volume within 6 months as departments answer their own routine questions.

### Radar Dashboards

Radar is Epic's operational dashboard platform. Dashboards use **components** — each component is a data view (metric count, list, run chart, care gap tracker) configured against a Reporting Workbench template or a Radar-native data source.

For the ED throughput standardization fix:
- Audit all 3 dashboard component definitions
- Identify the date anchor field in each component's underlying template
- Replace inconsistent date anchors with `Arrival DT` uniformly
- Add a date-range parameter so clinical leadership can self-filter
- Deploy to all 5 sites with site-specific Department ID filters

### Chronicles Data Navigation

Chronicles is Epic's proprietary hierarchical database. It does not use tables; instead, it uses **records** organized by **master files** (identified by 3-letter INI codes). Each record has numbered **items** (analogous to columns).

Key master files for this role:
- `EPT` — patient demographics and registration data
- `PAT ENC` / `CSN` — encounter records (the core unit for most ambulatory reports)
- `HLX` — health maintenance / preventive care milestones (HEDIS measures)
- `HSP ACCT` / `HAR` — hospital account records (facility billing, ED visits)
- `CLB` — clinical results (lab values, vitals)
- `EMP` — provider / staff records (for productivity reports)

For population health quality reports (HEDIS-aligned), data lives primarily across EPT, HLX, and PAT ENC with join paths that require understanding Epic's relationship model. I document the Chronicles path for every report template we maintain.

---

## Data Quality and ETL

### Validation approach

Every new report goes through a 3-step QA process before delivery:

1. **Spec validation** — compare report output to manually verified source data for a sample of 20–50 records. Verify that criteria filters produce the expected denominator.
2. **Cross-reference check** — run the same population through an independent source (e.g., a pre-existing legacy report or a direct Chronicles lookup) and compare counts. Acceptable variance: < 0.5%.
3. **Edge case testing** — run the report with extreme date ranges (start of Epic go-live vs. a 1-day window) to confirm filters work correctly at boundaries.

For the ED dashboards, the cross-reference check immediately surfaced the date-anchor discrepancy: one dashboard showed 12% more ED visits in a given month because it counted `Encounter Close DT` (which can fall in the next calendar month) instead of `Arrival DT`.

### ETL for downstream platforms

When report data needs to flow to a population health platform (e.g., Arcadia, Innovaccer), I:
- Build a Reporting Workbench extract with a scheduled automated run via Report Broker
- Output to a dedicated SFTP landing zone in a standardized CSV schema
- Include a record count in the filename so the receiving system can detect truncated files
- Maintain a data dictionary mapping Epic field names to the target platform's field names
- Log run timestamps and record counts to a SharePoint tracking sheet

---

## Monitoring and Observability

### Operational metrics dashboard (Radar)

I'll build a **Report Services Operations Dashboard** visible to the analytics team:

| Component | Metric | Target |
|---|---|---|
| Open request count by age bucket | 0-14d, 15-30d, 31-60d, 60d+ | 90% in 0-30d bucket |
| Requests closed this month | Running count | Trend up |
| On-time delivery rate | % delivered by promised date | 95%+ |
| Top requesting departments | Bar chart by site | Informs self-service training priority |
| Automated report run status | Last run timestamp per report | All green (no missed runs) |
| Slicer Dicer session count | Monthly user count | Trend up = adoption working |

### Report performance monitoring

Large or inefficient Reporting Workbench templates can time out or run slowly, impacting analyst productivity and server load. I monitor:
- Report run time (Epic logs execution time per template run)
- CPU-intensive templates (flagged in Epic System Pulse)
- Scheduled reports that fail to complete (Alert via Report Broker failure notification)

For any template taking over 5 minutes, I audit the criteria — often a missing index filter on a date field is the cause.

---

## Change Management

### Request intake process

1. **Intake form** (SharePoint or ServiceNow) captures: requester name, department, site, business question, urgency, report type needed, frequency
2. **Analyst triage** within 2 business days: classify request, estimate effort, set expected delivery date
3. **Spec meeting** with requester to confirm filters, groupers, output format, and intended recipients
4. Sign off on written spec before development begins (prevents scope creep)

### Development and delivery workflow

```
Request received → Triage (Day 1-2) → Spec meeting (Day 3-5) → Build (Day 5-15)
→ QA validation → UAT with requester → Sign-off → Deploy to production → Documentation
```

### Phased rollout for new dashboard suite

- **Phase 1 (Weeks 1-2):** Deploy to Meridian's flagship site. Collect feedback.
- **Phase 2 (Weeks 3-4):** Roll out to the remaining 4 sites with site-specific filters applied.
- **Phase 3 (Month 2):** Train department super-users on Slicer Dicer self-service layer.
- **Phase 4 (Month 3):** Retire duplicated legacy dashboards and archive old templates.

---

## Root Cause Analysis (Real-Style Incident)

### Incident: ED Throughput Metric Discrepancy — June 2025

**Detection:** The Quality Improvement director flagged that two Radar dashboards showed a 12% variance in ED visit counts for the same month across two Meridian sites. The sites had similar volumes, so the discrepancy didn't make clinical sense.

**Investigation:**
1. Pulled underlying Reporting Workbench template IDs for both dashboard components.
2. Compared criteria filters side by side:
   - Dashboard A used `Encounter Date` filter = `ED Arrival DT` (item PAT ENC 12060)
   - Dashboard B used `Encounter Date` filter = `Encounter Close DT` (item PAT ENC 18520)
3. Encounters that opened in late June and closed in early July were counted in Dashboard A's June total but excluded from Dashboard B's June total — and counted in Dashboard B's July total instead.
4. This explained the exact 12% discrepancy: 214 encounters across the two sites straddled the month boundary.

**Fix:**
- Updated all 3 ED-related Reporting Workbench templates to use `ED Arrival DT` consistently.
- Updated all ED Radar dashboard components to point to the revised templates.
- Added a comment in each template's description field documenting the date anchor standard.
- Sent a communication to the QI team explaining the fix and confirming historical data was not affected (only forward-looking counts now align).

**Prevention:**
- Added "date anchor field" as a required field in the report spec documentation template.
- Created a standing QA checklist item: cross-compare any new report against an existing validated data source before deployment.

**Result:** Metrics aligned within 0.1% after fix (residual variance = data entry timing, not report logic). QI director signed off within 48 hours.

---

## Disaster Recovery

### Report template backup strategy

Epic Reporting Workbench templates live in a shared production environment. To prevent data loss:
- All custom report templates are documented in a **SharePoint report registry** with template ID, description, Chronicles sources, owner, and last-modified date
- Monthly export of template definitions (via Epic's export tools) stored in a versioned SharePoint library
- Critical templates (8 HEDIS quality reports, ED throughput dashboards) are tagged as Tier 1 and reviewed quarterly

### Reporting service continuity

If Epic production is unavailable (unplanned downtime, patching window):
- Automated Report Broker jobs are paused; scheduled jobs resume on next broker run after system restoration
- Stakeholders are notified via pre-built communication template with estimated resume time
- For time-critical reports (QI board reports, state reporting deadlines), a manual fallback using Cogito/Caboodle (Epic's data warehouse) is available — these are separately available during Epic planned downtime windows

### Community Connect spoke failover

Community Connect spoke sites rely on the host's Epic environment. In a host outage:
- Spoke sites enter **downtime procedures** (paper forms, print-at-go-live packets)
- Analytics team switches to Caboodle for read-only historical reporting during downtime
- Outage duration and affected records are logged; a reconciliation report is run post-restoration to flag any encounter records that need manual review

---

## Automation

### What I would automate and how

**1. Monthly HEDIS quality report distribution (highest ROI)**  
Configure Report Broker to run all 8 quality Reporting Workbench templates on the 1st of each month, output to a validated SharePoint folder, and send a completion notification email to the QI coordinator. Manual analyst time: 18 hours reduced to 30 minutes for exception review.

**2. Nightly ED throughput summary email**  
Schedule a Radar burst report (daily email snapshot of ED visit counts and average door-to-provider time by site) to go to ED medical directors automatically each morning.

**3. Data validation Python script**  
A lightweight Python script runs after each Report Broker job to:
- Count rows in the output file
- Compare to the prior month's baseline (flag if count deviates > 15%)
- Log the result to a SharePoint tracking row
- Email alert if an anomaly is detected

```python
import pandas as pd, pathlib, datetime

report_dir = pathlib.Path("/reports/monthly-quality")
today = datetime.date.today().replace(day=1)

for report_file in report_dir.glob(f"{today.strftime('%Y%m')}*.csv"):
    df = pd.read_csv(report_file)
    row_count = len(df)
    print(f"{report_file.name}: {row_count} rows")
    # Add baseline comparison and alert logic here
```

**4. Request queue aging alert**  
A weekly query of ServiceNow (or equivalent) flags any report request that has been open for 21 days without an update. The alert routes to the requesting department and the analytics manager for visibility.

---

## Why I Stand Out

1. **Automation-first mindset, proven in production:** At Bright Horizons, I reduced manual CI/CD effort by ~40% through AI-driven agentic workflows. The same discipline applies here: I'll find the highest-manual-burden recurring report and automate it in the first month.

2. **Data integrity through systematic comparison:** I built an Inventory Configuration Comparison framework at Zoho that detected drift between production and DR environments, cutting discrepancies by ~80%. Applying the same logic to report validation — cross-comparing outputs against source data before delivery — is exactly how I catch the kind of date-anchor discrepancy described in the RCA above.

3. **Comfortable with ambiguity and self-directed learning:** I hold an MS in Computer Science and have taught myself cloud infrastructure, DevOps tooling, and automation frameworks on the job. Epic's reporting tools have a learning curve, but the underlying data reasoning (hierarchical data models, join logic, filter sequencing) is terrain I know well from working with Chronicles-adjacent data structures and AWS RDS schemas.

4. **Communication that closes the loop:** Every project I've run has had explicit stakeholder sign-off milestones. I don't deliver reports and disappear — I validate with the requester, document the spec, and make the handoff clean enough that someone else could maintain it.

5. **Boston-area, remote-ready, healthcare-adjacent:** I'm based in Framingham, MA — BMCHS's primary market. I understand Massachusetts health system dynamics and payer landscape from working in adjacent tech roles. I can attend on-site when needed without friction.

---

## About Me and Fit

**Praveendhra Rajkumar**  
Framingham, MA | p.rajkumar001@umb.edu | (857) 391-4257  
MS Computer Science, UMass Boston, 2024

I'm a DevOps/Cloud engineer with 4+ years of production experience building automation and data integrity systems. I'm transitioning into clinical analytics because I want my technical work to have a direct patient care impact — and this role at BMCHS is exactly where I can do that.

### Experience bridge

| My background | How it maps to this role |
|---|---|
| Built Python-driven data validation at Zoho, comparing inventory state across environments | Directly maps to QA validation of Reporting Workbench outputs against Chronicles source data |
| Managed CI/CD release pipelines with 99%+ on-time delivery at Bright Horizons | Mirrors managing a report request queue with committed delivery timelines and stakeholder communication |
| Designed and queried MySQL databases for internal tooling as an intern at Zoho | SQL skills transfer directly to understanding Chronicles query logic and Caboodle SQL analytics |
| Reduced manual CI/CD effort ~40% via automation | Report Broker automation of 8 monthly quality reports targets the same pattern: find the manual work and eliminate it |
| Chaos engineering experiments to validate DR and fallback services at Bright Horizons | Validates report continuity planning — ensuring automated jobs resume correctly after downtime |
| MS in Computer Science — data structures, algorithms, database systems coursework | Foundation for understanding Chronicles' hierarchical master file model and report query design |
