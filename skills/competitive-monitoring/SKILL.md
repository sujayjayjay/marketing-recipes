---
name: competitive-monitoring
description: "When the user wants to set up ongoing competitive intelligence gathering and monitoring. Also use when the user mentions 'watch competitors,' 'competitor monitoring,' 'track competitor changes,' 'competitive intel,' 'what are they doing,' 'competitor pricing changed,' 'battle cards,' 'competitive analysis automation,' or 'stay on top of competitors.' This recipe actually scrapes, monitors, and reports on competitor activity — not just frameworks for analysis. For creating competitor comparison pages, see competitor-alternatives."
metadata:
  version: 1.0.0
  author: composio
  requires: [composio]
  complements: [competitor-alternatives, content-strategy, paid-ads, pricing-strategy]
---

# Competitive Monitoring

You are setting up and running automated competitive intelligence. You actively monitor competitor websites, social presence, SEO positions, and product changes, then deliver actionable reports.

## Prerequisites

1. List of competitors to monitor (3-5 primary, 5-10 secondary)
2. Monitoring tools connected via Composio
3. A Slack channel or notification destination

## Connected Tools

| Tool | Purpose | Key Actions |
|------|---------|-------------|
| Ahrefs | SEO & backlink monitoring | Get keywords, get backlinks, domain overview |
| SEMrush | Keyword tracking & ad intelligence | Get keywords, domain overview, ad copies |
| Firecrawl | Website change detection | Scrape URL, crawl site |
| Exa | Press & mention tracking | Search for competitor mentions |
| Reddit | Community sentiment | Search posts, get comments |
| Twitter/X | Social monitoring | Search tweets, get user tweets |
| Google Sheets | Battle card database | Read/update/append |
| Slack | Alert distribution | Send messages |
| Notion | Competitive wiki | Create/update pages |

## Workflow

### Step 1: Set Up Competitor Profiles

For each competitor, gather baseline data:

1. **Website data via Firecrawl:**
   - `FIRECRAWL_SCRAPE_URL` on pricing page → store current pricing
   - `FIRECRAWL_SCRAPE_URL` on features page → store feature list
   - `FIRECRAWL_SCRAPE_URL` on changelog/blog → store latest updates

2. **SEO baseline via Ahrefs:**
   - `AHREFS_GET_DOMAIN_OVERVIEW` → organic traffic, domain rating
   - `AHREFS_GET_KEYWORDS` → top ranking keywords
   - `AHREFS_GET_BACKLINKS` → new backlink sources

3. **Store in Google Sheets:**
   - `GOOGLESHEETS_UPDATE_SPREADSHEET` with competitor matrix:
   ```
   | Competitor | Pricing (date) | Features count | Traffic | DR | Top keywords |
   ```

### Step 2: Monitor Changes

Run these checks on schedule (suggest daily for pricing, weekly for others):

#### Pricing Changes
```
For each competitor:
1. FIRECRAWL_SCRAPE_URL → competitor pricing page
2. Compare with stored version
3. If changed → SLACK_SEND_MESSAGE alert:
   "⚠️ {Competitor} pricing changed! {diff summary}"
4. Update stored version in Google Sheets
```

#### Feature & Product Changes
```
For each competitor:
1. FIRECRAWL_SCRAPE_URL → changelog/release notes page
2. Identify new entries since last check
3. If new features → summarize and alert
```

#### SEO Position Changes
```
1. AHREFS_GET_KEYWORDS or SEMRUSH_GET_KEYWORDS for competitor domains
2. Compare with baseline
3. Flag: new keywords they're ranking for (opportunities)
4. Flag: keywords where they overtook you (threats)
```

#### Press & Mentions
```
1. EXA_SEARCH → "{competitor name}" in last 7 days
2. Filter for: funding, partnerships, product launches, executive hires
3. Summarize and alert on significant mentions
```

#### Social Sentiment
```
1. REDDIT_SEARCH_POSTS → competitor name in relevant subreddits
2. TWITTER_SEARCH_TWEETS → competitor mentions
3. Categorize: praise, complaints, feature requests, comparisons to you
4. Surface top complaints (positioning opportunities)
```

### Step 3: Battle Card Maintenance

Auto-update battle cards in Google Sheets or Notion:

```markdown
# {Competitor} Battle Card
Last updated: {auto-date}

## Quick Facts (auto-updated)
- Pricing: {current_pricing}
- Est. Traffic: {monthly_organic}
- Recent changes: {last_3_updates}

## How We Win
{manual — sales team maintains this section}

## Where They Win
{manual — updated quarterly}

## Recent Changes (auto-populated)
- {date}: {change_description}
- {date}: {change_description}

## Customer Complaints (from Reddit/Twitter)
- "{quote}" — r/{subreddit}, {date}
- "{quote}" — @{handle}, {date}
```

**Update:** Use `NOTION_UPDATE_PAGE` or `GOOGLESHEETS_UPDATE_SPREADSHEET` to keep "Quick Facts" and "Recent Changes" current.

### Step 4: Competitive Digest

Weekly, compile a digest:

**Post to Slack** via `SLACK_SEND_MESSAGE`:
```
📊 Weekly Competitive Intel Digest

🏢 {Competitor 1}
  - Pricing: No change / Changed: {details}
  - New features: {list}
  - SEO: {gained/lost} keywords for {topic}
  - Mentions: {count} ({sentiment})

🏢 {Competitor 2}
  ...

🎯 Opportunities
  - {Competitor} customers complaining about {issue} → we solve this
  - {Competitor} not ranking for "{keyword}" → content opportunity

⚠️ Threats
  - {Competitor} now ranks #1 for "{keyword}" (we're #{position})
  - {Competitor} launched {feature} we don't have
```

## Monitoring Schedule

| Check | Frequency | Tools |
|-------|-----------|-------|
| Pricing pages | Daily | Firecrawl |
| Changelog/releases | Daily | Firecrawl |
| SEO keywords | Weekly | Ahrefs/SEMrush |
| Press mentions | Weekly | Exa |
| Social sentiment | Weekly | Reddit, Twitter |
| Backlinks | Monthly | Ahrefs |
| Full battle card refresh | Monthly | All |

## Related Skills

- `competitor-alternatives` — For creating comparison pages based on this intel
- `content-strategy` — For turning competitor gaps into content opportunities
- `paid-ads` — For competitive ad intelligence (what ads they're running)
- `pricing-strategy` — For responding to competitor pricing changes
