---
name: cross-channel-reporting
description: "When the user wants to pull marketing performance data across channels and build a unified report. Also use when the user mentions 'marketing report,' 'campaign performance,' 'what's working,' 'channel performance,' 'ROI report,' 'marketing dashboard,' 'pull my metrics,' 'weekly report,' 'monthly report,' 'ROAS,' 'CAC,' 'attribution,' or 'how are my campaigns doing.' This recipe actually pulls live data from ad platforms, CRM, and analytics and builds a formatted report. For setting up tracking, see analytics-tracking."
metadata:
  version: 1.0.0
  author: composio
  requires: [composio]
  complements: [analytics-tracking, ab-test-setup, paid-ads, revops]
---

# Cross-Channel Reporting

You are a marketing reporting engine. You pull live performance data from multiple platforms, calculate key metrics, identify trends, and deliver a formatted report — all through direct API calls.

## Prerequisites

1. At least 2 marketing channels connected via Composio
2. GA4 or analytics tracking in place (use `analytics-tracking` skill first if not)
3. Reporting period defined (last 7 days, last 30 days, etc.)

## Connected Tools

| Tool | Purpose | Key Actions |
|------|---------|-------------|
| GA4 | Website analytics | Get report, list metrics |
| Meta Ads | Facebook/Instagram performance | Get campaign insights, get account insights |
| Google Ads | Search/display performance | Get campaign stats, get keyword performance |
| LinkedIn Ads | Professional channel | Get campaign analytics |
| HubSpot | Email + CRM metrics | Get email stats, get deal pipeline |
| Google Sheets | Report output | Create/update spreadsheet |
| Slack | Report delivery | Send formatted message |
| Notion | Report archive | Create page |

## Workflow

### Step 1: Define Reporting Period

Ask the user for:
- **Period:** Last 7 days, last 30 days, last quarter, custom range
- **Comparison:** vs. previous period, vs. same period last year
- **Channels:** Which platforms to include
- **Goals:** What KPIs matter most (leads, revenue, traffic, ROAS)

### Step 2: Pull Channel Data

#### GA4 (Website)
Use `GA4_GET_REPORT` for:
- Sessions, users, new users
- Conversion events (signups, demos, purchases)
- Traffic by source/medium
- Top landing pages by conversion rate
- Bounce rate, session duration

#### Meta Ads
Use `FACEBOOKADS_GET_ACCOUNT_INSIGHTS` for:
- Spend, impressions, clicks, CTR
- Conversions, cost per conversion
- ROAS (return on ad spend)
- Breakdown by campaign, ad set, age, placement

#### Google Ads
Use `GOOGLEADS_GET_CAMPAIGN_STATS` for:
- Spend, impressions, clicks, CTR
- Conversions, cost per conversion
- Search impression share
- Top keywords by performance

#### LinkedIn Ads
Use `LINKEDIN_GET_CAMPAIGN_ANALYTICS` for:
- Spend, impressions, clicks, CTR
- Lead form submissions
- Cost per lead
- Engagement rate

#### HubSpot (Email + Pipeline)
- `HUBSPOT_GET_EMAIL_STATS` → open rate, click rate, unsubscribes
- `HUBSPOT_SEARCH_DEALS` → new deals created, pipeline value, deals won

### Step 3: Calculate Aggregate Metrics

Compute across all channels:

```
📊 OVERVIEW
Total spend: ${sum_all_channels}
Total conversions: {sum_conversions}
Blended CAC: ${total_spend / total_conversions}
Blended ROAS: {total_revenue / total_spend}

📈 CHANNEL COMPARISON
| Channel   | Spend    | Conv. | CAC      | ROAS  | Δ vs Prior |
|-----------|----------|-------|----------|-------|------------|
| Meta Ads  | ${x}     | {n}   | ${x/n}   | {r}x  | {%}        |
| Google    | ${x}     | {n}   | ${x/n}   | {r}x  | {%}        |
| LinkedIn  | ${x}     | {n}   | ${x/n}   | {r}x  | {%}        |
| Email     | $0       | {n}   | $0       | ∞     | {%}        |
| Organic   | $0       | {n}   | $0       | ∞     | {%}        |

🏆 TOP PERFORMERS
- Best CAC: {channel} at ${cac}
- Best ROAS: {channel} at {roas}x
- Most volume: {channel} with {n} conversions

⚠️ ATTENTION NEEDED
- {channel}: CAC increased {%} vs prior period
- {campaign}: CTR below {threshold}%, consider refreshing creative
- {channel}: Spend up {%} but conversions flat — check targeting
```

### Step 4: Deliver Report

#### Slack Summary
Use `SLACK_SEND_MESSAGE` to #marketing-metrics:
- Formatted summary with key metrics
- Top 3 wins, top 3 concerns
- Recommended actions

#### Google Sheets Dashboard
Use `GOOGLESHEETS_UPDATE_SPREADSHEET`:
- Write raw data to a "Data" tab
- Summary metrics to a "Dashboard" tab
- Historical data appended for trend tracking

#### Notion Report (Detailed)
Use `NOTION_CREATE_PAGE` in reports database:
- Full report with all metrics
- Channel breakdowns
- Recommendations
- Tagged with date for archival

### Step 5: Automated Insights

Beyond raw numbers, surface:

1. **Trend detection:** Is CAC trending up or down over 4 weeks?
2. **Budget reallocation suggestions:** Shift $ from highest CAC to lowest
3. **Creative fatigue signals:** CTR declining on same creative > 2 weeks
4. **Funnel analysis:** Where are leads dropping off (click → land → convert)?
5. **Attribution notes:** Organic often assists paid — don't cut organic if it's "low converting" in last-click

## Report Templates

### Weekly Flash Report (Slack)
Quick 5-line summary for executives:
```
📊 Week of {date}: ${spend} spent → {conversions} conversions (${cac} CAC)
📈 Best: {channel} ({metric})
📉 Watch: {channel} ({concern})
💡 Action: {recommendation}
```

### Monthly Deep Dive (Notion/Sheets)
Full analysis with charts and recommendations.

### Quarterly Business Review (Google Docs)
Board-ready with trends, benchmarks, and strategic recommendations.

## Related Skills

- `analytics-tracking` — For setting up the measurement this recipe reads
- `ab-test-setup` — For experiments that improve the metrics you're tracking
- `paid-ads` — For detailed ad platform management
- `revops` — For pipeline and revenue metrics
