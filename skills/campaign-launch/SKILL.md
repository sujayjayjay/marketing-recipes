---
name: campaign-launch
description: "When the user wants to launch a marketing campaign across multiple channels simultaneously — email, social media, and paid ads. Also use when the user mentions 'launch campaign,' 'go live,' 'campaign rollout,' 'multi-channel launch,' 'coordinated push,' 'campaign across channels,' 'launch this everywhere,' or 'push this live.' This recipe actually creates and schedules content across platforms — not just plans it. For campaign strategy and planning, see launch-strategy. For individual channel content, see social-content or email-sequence."
metadata:
  version: 1.0.0
  author: composio
  requires: [composio]
  complements: [launch-strategy, social-content, email-sequence, ad-creative, copywriting]
---

# Campaign Launch

You are executing a coordinated multi-channel campaign launch. Your job is to take approved campaign assets and actually publish, schedule, or queue them across all target channels using real tool integrations.

## Prerequisites

Before running this recipe, ensure:
1. Campaign copy and creative are approved (use `copywriting` or `ad-creative` skill first)
2. Product marketing context exists (use `product-marketing-context` skill first)
3. Required tools are connected via Composio MCP server

## Connected Tools

This recipe uses the following tools via Composio:

| Tool | Purpose | Key Actions |
|------|---------|-------------|
| HubSpot | Email campaigns | Create email, create list, schedule send |
| LinkedIn | Organic social posts | Create post, schedule post |
| Meta Ads | Paid campaigns | Create campaign, create ad set, create ad |
| Google Ads | Search/display ads | Create campaign, add keywords, create ad |
| Buffer/Hootsuite | Social scheduling | Schedule posts across platforms |
| Slack | Team notifications | Post launch status updates |

## Workflow

### Step 1: Gather Campaign Brief

Ask the user for:
- **Campaign name** and objective (awareness, leads, sales)
- **Target audience** (existing segments or new criteria)
- **Channels** to activate (email, LinkedIn, Meta, Google, Twitter/X)
- **Timeline** (launch date, duration, follow-up dates)
- **Assets** (copy variations, images, landing page URL)
- **Budget** (for paid channels)

### Step 2: Email Channel (HubSpot)

If email is included:

1. **Create or select contact list**
   - Use `HUBSPOT_SEARCH_CONTACTS` to find matching contacts
   - Or use `HUBSPOT_GET_LIST` for an existing segment

2. **Create email campaign**
   - Use `HUBSPOT_CREATE_MARKETING_EMAIL` with:
     - Subject line (A/B variant if specified)
     - Body HTML from approved copy
     - From name/address
     - List ID for recipients

3. **Schedule send**
   - Use `HUBSPOT_SCHEDULE_EMAIL` with launch date/time
   - Confirm: "Email scheduled for {date} to {count} contacts"

### Step 3: LinkedIn Organic

If LinkedIn is included:

1. **Create post(s)**
   - Use `LINKEDIN_CREATE_POST` with:
     - Post text (optimized for LinkedIn — hook in first 2 lines, line breaks, hashtags)
     - Image or link preview
     - Visibility settings

2. **Schedule if not launching immediately**
   - Queue via Buffer: `BUFFER_CREATE_UPDATE` with scheduled time

### Step 4: Meta Ads (Facebook/Instagram)

If Meta Ads is included:

1. **Create campaign**
   - Use `FACEBOOKADS_CREATE_CAMPAIGN` with:
     - Campaign name
     - Objective (CONVERSIONS, LEAD_GENERATION, TRAFFIC, AWARENESS)
     - Budget (daily or lifetime)
     - Status: PAUSED (review before activating)

2. **Create ad set**
   - Use `FACEBOOKADS_CREATE_ADSET` with:
     - Targeting (location, age, interests, custom audiences)
     - Placement (automatic or manual)
     - Schedule (start/end dates)
     - Bid strategy

3. **Create ad(s)**
   - Use `FACEBOOKADS_CREATE_AD` with:
     - Creative (image/video URL, headline, body, CTA)
     - Tracking (UTM parameters, pixel)

4. **Activate when ready**
   - Confirm with user before switching status to ACTIVE

### Step 5: Google Ads

If Google Ads is included:

1. **Create campaign**
   - Use `GOOGLEADS_CREATE_CAMPAIGN` with:
     - Campaign type (Search, Display, Performance Max)
     - Budget, bidding strategy
     - Location and language targeting

2. **Add keywords** (for Search)
   - Use `GOOGLEADS_ADD_KEYWORDS` with keyword list and match types

3. **Create ad copy**
   - Use `GOOGLEADS_CREATE_AD` with:
     - Headlines (up to 15), descriptions (up to 4)
     - Final URL with UTM parameters
     - Sitelinks if provided

### Step 6: Tracking Setup

For all channels:

1. **Generate UTM parameters**
   ```
   utm_source={channel}
   utm_medium={paid|organic|email}
   utm_campaign={campaign-slug}
   utm_content={variant-id}
   ```

2. **Confirm pixel/tracking**
   - Verify landing page has GA4 and Meta Pixel
   - Check conversion events are configured

### Step 7: Launch Notification

After all channels are live or scheduled:

1. **Post to Slack**
   - Use `SLACK_SEND_MESSAGE` to #marketing channel:
   ```
   🚀 Campaign "{name}" launched!
   📧 Email: Scheduled for {date} → {count} contacts
   📱 LinkedIn: Posted/scheduled
   💰 Meta Ads: {budget} budget, targeting {audience}
   🔍 Google Ads: {keywords} keywords live
   📊 Tracking: UTMs applied, dashboard link: {url}
   ```

2. **Create follow-up tasks**
   - Day 1: Check email open rates
   - Day 3: Review ad performance, pause underperformers
   - Day 7: Full performance report

## Checklist Before Launch

- [ ] All copy reviewed and approved
- [ ] Landing page live and tracking verified
- [ ] UTM parameters consistent across all channels
- [ ] Email list cleaned (no bounced/unsubscribed)
- [ ] Ad creative meets platform specs (dimensions, text limits)
- [ ] Budget confirmed for paid channels
- [ ] Team notified of launch timing

## Related Skills

- `launch-strategy` — For planning the launch approach (use before this recipe)
- `copywriting` — For writing the campaign copy
- `ad-creative` — For generating ad variations
- `email-sequence` — For multi-email drip campaigns (use this recipe for single-send campaigns)
- `analytics-tracking` — For setting up measurement
- `ab-test-setup` — For running experiments within the campaign
