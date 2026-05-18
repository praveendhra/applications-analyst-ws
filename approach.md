# Approach — Report Writer / Applications Developer (BMCHS Clinical Analytics)

## 1. Role Breakdown by Section

### Position Summary
- Designing, developing, and maintaining **reports, extracts, and dashboards** within Epic EHR
- Primary tools: **Slicer Dicer, Reporting Workbench, Radar dashboards**
- Manage a personal queue of requests, incidents, and projects — must multitask and self-prioritize
- Communicate directly with end users to define specs and set delivery timelines
- Collaborate with application and clinical teams to understand underlying workflows
- Understand **Chronicles** (Epic's hierarchical data store) to locate the right data
- Participate in weekly team meetings and all-staff standups

### Key Responsibilities
1. Build Reporting Workbench templates for recurring operational and quality reports
2. Create Radar/Dashboard components (run charts, metric counts, snapshot tables)
3. Support self-service analytics via curated Slicer Dicer data models
4. Write extracts for downstream systems (ETL handoffs, population health platforms)
5. Validate data quality against source-of-truth workflows
6. Maintain documentation on report specs and Chronicles sources
7. Handle ad-hoc Community Connect multi-site reporting (spoke-site variations)

---

## 2. Gap Analysis

| What I Know Well | Gap / Brush-Up Area |
|---|---|
| SQL querying and data modeling | Epic's proprietary Chronicles data model (INI/items, master files) |
| Python scripting for data automation | Reporting Workbench template structure (IDX records) |
| NGINX/load balancer performance tuning | Slicer Dicer model configuration |
| CI/CD automation reducing manual work by ~40% | Radar component types (run charts, metric counts) |
| Inventory drift detection (config comparison framework) | Community Connect multi-site reporting nuances |
| 99.9%+ uptime / data continuity discipline | HL7/FHIR data feeds into Epic |
| Chaos engineering / DR validation | ClinDoc workflows and flowsheet data structures |

**Mitigation plan:** Epic offers free online learning through **Epic UserWeb**. I'll pursue:
- Reporting Workbench Fundamentals (self-paced cert)
- Slicer Dicer Fundamentals
- Chronicles Concepts for Analysts

---

## 3. Certification Roadmap

| Timeline | Certification/Training |
|---|---|
| Month 1 | Epic Reporting Workbench — Fundamentals certification |
| Month 1 | Epic Slicer Dicer for Analysts |
| Month 2 | Epic Radar Foundation certification |
| Month 2 | HIPAA Workforce training (org-sponsored) |
| Month 3 | Epic Chronicles Concepts for Report Writers |
| Month 4 | Epic Community Connect — Satellite Site Reporting |
| Month 6 | Certified Associate in Project Management (CAPM) — optional |

---

## 4. Domain-Specific Considerations

### HIPAA Compliance
- Reports containing PHI must be role-scoped — distribute only to users with Need-to-Know
- Epic's **Security Point** and **Break-the-Glass** logging must be verified before report deployment
- Apply **minimum necessary standard** — strip PHI fields not needed for the reporting purpose
- All report extracts sent outside Epic must be encrypted at rest and in transit
- Distribution via secure channels only (not plain email; use SFTP or Epic Report Broker with auth)

### HL7 / FHIR Context
- Community Connect spokes may feed ADT (Admit/Discharge/Transfer) events via HL7 v2
- FHIR APIs may be used for population health platform integration
- Understanding interface message routing helps trace missing encounter data in reports

### Community Connect Multi-Site Reporting
- Community Connect (CC) spoke sites share a single Epic production environment with the host
- Report templates must explicitly filter or parameterize by **Department ID** or **CC Site** to avoid cross-site data bleed
- Spoke-specific service areas may have different workflow configurations — validate filters independently

---

## 5. KPIs and Metrics for Work Sample

| KPI | Target | Source |
|---|---|---|
| Report request resolution rate | 90% within 30 business days | Request queue |
| Report delivery on-time | 95%+ of committed dates met | Jira/ServiceNow tracker |
| Data accuracy rate | 99.5%+ match vs. source encounters | QA validation counts |
| Analyst response time to new requests | 2 business days for initial spec meeting | Intake timestamp |
| Self-service Slicer Dicer adoption | 30% reduction in ad-hoc requests in 6 months | Comparative request volume |
| Monthly report generation time | Reduce from 18 hrs to < 4 hrs via automation | Time tracking |

---

## 6. Tooling Stack the Employer Uses

| Category | Tool |
|---|---|
| EHR Platform | Epic (Community Connect model) |
| Clinical Reporting | Reporting Workbench, Slicer Dicer, Radar |
| Data Store | Chronicles (Epic's proprietary NoSQL-like hierarchical DB) |
| Secondary Analytics | Caboodle (Epic's analytics data warehouse), Cogito |
| Distribution | Epic Report Broker, Crystal Reports, Tableau (possible) |
| Ticketing | Likely ServiceNow or Jira (enterprise healthcare standard) |
| Collaboration | Microsoft Teams / SharePoint |
| Data Quality Checks | Manual QA + Python scripting |
| Documentation | SharePoint wiki or Confluence |

---

## 7. Top 3 Pain Points This Role Is Solving

1. **Report backlog**  — large queue of unresolved requests from clinical departments, aging 90+ days. The team needs systematic prioritization and fast delivery.
2. **Data inconsistency across sites** — Community Connect spoke sites have variations in workflow configuration, leading to metric discrepancies in multi-site dashboards.
3. **Manual reporting burden** — recurring monthly quality reports require excessive manual analyst hours that could be automated via Report Broker or scheduled recurring delivery.
