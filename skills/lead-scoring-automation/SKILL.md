---
name: lead-scoring-automation
description: "When the user wants to implement, update, or debug automated lead scoring and routing in their CRM. Also use when the user mentions 'lead scoring,' 'MQL scoring,' 'score my leads,' 'lead qualification automation,' 'route leads to sales,' 'lead assignment,' 'scoring model,' 'who should sales call first,' 'prioritize leads,' or 'which leads are hot.' This recipe actually reads CRM data, applies a scoring model, and updates lead scores — not just designs the model. For RevOps strategy and lifecycle definitions, see revops. For lead enrichment before scoring, use Apollo or Clearbit via Composio."
metadata:
  version: 1.0.0
  author: composio
  requires: [composio]
  complements: [revops, cold-email, sales-enablement, analytics-tracking]
---

# Lead Scoring Automation

You are automating lead scoring and routing in the user's CRM. You read lead data, apply a scoring model, update scores, and route qualified leads to the right sales rep — all through direct CRM actions.

## Prerequisites

1. CRM connected via Composio (HubSpot, Salesforce, or Pipedrive)
2. RevOps strategy defined (lifecycle stages, MQL/SQL criteria — use `revops` skill first if not)
3. Historical deal data available for calibrating the model

## Connected Tools

| Tool | Purpose | Key Actions |
|------|---------|-------------|
| HubSpot | Primary CRM | Search contacts, update contact properties, create deals, list activities |
| Salesforce | Alternative CRM | Query leads, update lead fields, assign owner, convert lead |
| Apollo | Enrichment | Enrich person, enrich organization (adds firmographic data for scoring) |
| Clearbit | Enrichment | Reveal company, enrich person |
| Slack | Notifications | Alert sales team on hot leads |
| Google Sheets | Scoring model | Read/update scoring rules spreadsheet |

## Workflow

### Step 1: Define or Load Scoring Model

Ask the user for their ICP or load from existing config. Default model:

#### Demographic Score (0-50 points)

| Signal | Points | How to Check |
|--------|--------|-------------|
| Job title contains VP, Director, Head, C-level | +15 | `HUBSPOT_SEARCH_CONTACTS` filter by jobtitle |
| Company size 50-500 employees | +10 | Apollo enrichment or HubSpot company property |
| Company size 500-5000 | +8 | |
| Industry matches ICP | +10 | HubSpot company `industry` property |
| Location in target market | +5 | HubSpot `country` property |
| Raised funding in last 18 months | +10 | Apollo org enrichment `funding_events` |

#### Behavioral Score (0-50 points)

| Signal | Points | How to Check |
|--------|--------|-------------|
| Visited pricing page | +15 | `HUBSPOT_LIST_ACTIVITIES` — page views |
| Submitted a form (demo, contact) | +20 | `HUBSPOT_LIST_ACTIVITIES` — form submissions |
| Opened 3+ marketing emails | +5 | `HUBSPOT_LIST_ACTIVITIES` — email opens |
| Clicked email CTA | +10 | `HUBSPOT_LIST_ACTIVITIES` — email clicks |
| Downloaded content (ebook, whitepaper) | +5 | Form submission on gated content |
| Visited 5+ pages in one session | +5 | Page view activity count |
| Returned visitor (2+ sessions) | +5 | Session count property |

### Step 2: Enrich Leads Missing Data

Before scoring, fill gaps:

1. **Pull contacts missing key fields:**
   ```
   HUBSPOT_SEARCH_CONTACTS where company_size IS NULL OR industry IS NULL
   ```

2. **Enrich via Apollo:**
   - `APOLLO_ENRICH_PERSON` with email → get title, company, seniority
   - `APOLLO_ENRICH_ORGANIZATION` with domain → get size, industry, funding

3. **Update CRM records:**
   - `HUBSPOT_UPDATE_CONTACT` with enriched data

### Step 3: Calculate and Apply Scores

For each lead in the pipeline:

1. **Pull lead data:**
   - `HUBSPOT_GET_CONTACT` with all properties
   - `HUBSPOT_LIST_ACTIVITIES` for behavioral signals

2. **Calculate score:**
   - Demographic score (from profile data)
   - Behavioral score (from activity data)
   - Total = demographic + behavioral

3. **Update lead score:**
   - `HUBSPOT_UPDATE_CONTACT` with `lead_score` property
   - Set `lead_status` based on thresholds:

| Total Score | Status | Action |
|-------------|--------|--------|
| 70-100 | 🔥 Hot (SQL) | Route to sales immediately |
| 50-69 | 🟡 Warm (MQL) | Add to nurture + alert SDR |
| 30-49 | 🟠 Cool | Continue marketing nurture |
| 0-29 | ❄️ Cold | Stay in general marketing |

### Step 4: Route Hot Leads

When a lead crosses the MQL/SQL threshold:

1. **Assign to sales rep:**
   - `HUBSPOT_UPDATE_CONTACT` → set `hubspot_owner_id` based on:
     - Territory (by region/country)
     - Round-robin (distribute evenly)
     - Account-based (if company already has an owner)

2. **Create deal:**
   - `HUBSPOT_CREATE_DEAL` with:
     - Deal name: "{Company} - {Lead Name}"
     - Stage: "Qualified Lead"
     - Amount: estimated based on company size tier
     - Associated contact and company

3. **Alert sales team:**
   - `SLACK_SEND_MESSAGE` to #sales-alerts:
   ```
   🔥 Hot lead alert!
   👤 {name} — {title} at {company}
   📊 Score: {score}/100 (Demo: {demo_score}, Behav: {behav_score})
   🎯 Key signals: {top_3_signals}
   📧 Email: {email} | 📱 Phone: {phone}
   🔗 CRM: {hubspot_url}
   ```

### Step 5: Scoring Audit

Weekly, verify the model's accuracy:

1. **Pull closed-won deals from last 30 days:**
   - `HUBSPOT_SEARCH_DEALS` where stage = closed-won
   - Check what their lead score was at MQL stage

2. **Pull closed-lost deals:**
   - Same query for closed-lost
   - Compare average scores

3. **Flag model drift:**
   - If closed-won average score < 60: model may be too loose
   - If closed-lost average score > 70: model may not differentiate well
   - Report findings and suggest adjustments

## Customization

The scoring model is a starting point. Ask the user:
- "What's your typical deal size?" (adjusts company size weighting)
- "What action most correlates with buying?" (increase that signal's points)
- "Who's your ideal buyer persona?" (adjust title/seniority weights)

## Related Skills

- `revops` — For defining lifecycle stages and handoff criteria (use before this recipe)
- `cold-email` — For outreach to warm leads that aren't yet MQL
- `sales-enablement` — For arming sales with context on scored leads
- `analytics-tracking` — For setting up the behavioral signals this recipe reads
