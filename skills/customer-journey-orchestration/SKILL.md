---
name: customer-journey-orchestration
description: "When the user wants to build or optimize a multi-channel customer journey — from first touch through onboarding to expansion. Also use when the user mentions 'customer journey,' 'lifecycle marketing,' 'drip campaign across channels,' 'multi-channel nurture,' 'onboarding emails plus in-app,' 'cross-channel automation,' 'marketing automation,' 'lifecycle email plus push,' 'customer journey map,' or 'full funnel automation.' This recipe orchestrates real actions across email, in-app, push, and social — not just maps the journey. For individual email sequences, see email-sequence. For onboarding optimization, see onboarding-cro."
metadata:
  version: 1.0.0
  author: composio
  requires: [composio]
  complements: [email-sequence, onboarding-cro, churn-prevention, signup-flow-cro, analytics-tracking]
---

# Customer Journey Orchestration

You are orchestrating multi-channel customer journeys. You set up automated sequences that coordinate email, Slack notifications, CRM updates, and in-app messaging based on user behavior triggers.

## Prerequisites

1. Email/CRM connected via Composio (HubSpot, ActiveCampaign, or Customer.io)
2. Customer lifecycle stages defined (use `revops` skill if not)
3. Key conversion events identified (use `analytics-tracking` skill if not)
4. Product marketing context loaded

## Connected Tools

| Tool | Purpose | Key Actions |
|------|---------|-------------|
| HubSpot | Email + CRM + workflows | Create email, create workflow, update contact, create list |
| ActiveCampaign | Email + automation | Create campaign, create automation, update contact |
| Customer.io | Event-driven messaging | Create campaign, send event, segment users |
| Intercom | In-app messaging | Create message, create series |
| OneSignal | Push notifications | Create notification, create segment |
| Slack | Internal alerts | Send message for sales-ready signals |
| Google Sheets | Journey performance tracking | Update spreadsheet |

## Journey Stages

### Stage 1: Awareness → Consideration

**Trigger:** First website visit or content download

Email sequence (3 emails over 7 days):
1. **Day 0:** Welcome + best content piece
   - `HUBSPOT_CREATE_MARKETING_EMAIL` — educational, no selling
2. **Day 3:** Problem-aware content
   - How others solve the problem you address
3. **Day 7:** Product-aware content
   - "Here's how {product} approaches this"

**CRM:** `HUBSPOT_UPDATE_CONTACT` → lifecycle_stage = "Subscriber"

### Stage 2: Consideration → Trial/Signup

**Trigger:** Pricing page visit OR demo request OR free trial signup

For pricing page visitors (no signup yet):
- `HUBSPOT_CREATE_MARKETING_EMAIL` — Case study + social proof
- Wait 3 days
- If no signup: Comparison guide (you vs. alternatives)

For free trial signups:
- `HUBSPOT_UPDATE_CONTACT` → lifecycle_stage = "Free Trial"
- Immediate welcome email with quickstart guide
- `SLACK_SEND_MESSAGE` to #new-signups: "{name} from {company} just signed up"

### Stage 3: Trial → Activation

**Trigger:** Signed up but hasn't completed key activation action

Day-by-day sequence:

| Day | Channel | Message | Trigger |
|-----|---------|---------|---------|
| 0 | Email | Welcome + "Start here" guide | Signup |
| 1 | Email | Complete first {key action} | No activation |
| 2 | In-app | Tooltip/modal showing next step | No activation |
| 3 | Email | "Need help getting started?" + Calendly link | Still no activation |
| 5 | Email | Success story: "How {customer} got value in 10 minutes" | Still no activation |
| 7 | Slack alert | "⚠️ {name} hasn't activated after 7 days" | Still no activation |

**CRM updates at each step:**
- `HUBSPOT_UPDATE_CONTACT` → last_engagement_date, activation_status
- If activated: `HUBSPOT_UPDATE_CONTACT` → lifecycle_stage = "Activated"

### Stage 4: Activation → Conversion

**Trigger:** Completed key activation action, free trial period running

| Day | Channel | Message |
|-----|---------|---------|
| Activation +1 | Email | "Great start! Here's what's next" |
| Activation +3 | Email | Advanced feature highlight |
| Trial midpoint | Email | "You're halfway through — upgrade to keep access" |
| Trial -3 days | Email | Urgency: "3 days left" + special offer |
| Trial -1 day | Email | Final notice + one-click upgrade |

For high-value accounts (enriched via Apollo):
- `SLACK_SEND_MESSAGE` to #sales-alerts: "High-value trial about to expire"
- SDR outreach with personalized offer

### Stage 5: Conversion → Expansion

**Trigger:** Became paying customer

1. **Welcome to paid:**
   - `HUBSPOT_CREATE_MARKETING_EMAIL` — Thank you, here's what's new for you
   - Set lifecycle to "Customer"

2. **Onboarding (weeks 1-4):**
   - Weekly tips email (advanced features)
   - In-app guidance for unused features

3. **Expansion signals (ongoing):**
   - Monitor usage patterns
   - When hitting plan limits: `HUBSPOT_CREATE_MARKETING_EMAIL` → upgrade nudge
   - Quarterly: NPS survey → promoters get referral program invite

### Stage 6: At-Risk → Retention

**Trigger:** Usage declining, support tickets, or churn signals

Use `churn-prevention` skill for detailed cancel flow, then:

1. **Usage drop detected:**
   - `HUBSPOT_CREATE_MARKETING_EMAIL` — "We miss you" + new feature highlights
   - `SLACK_SEND_MESSAGE` to CSM: "Account {company} usage dropped {%}"

2. **Cancel intent:**
   - Trigger cancel flow (see `churn-prevention` skill)
   - Save offer via email
   - CSM outreach for high-value accounts

## Journey Performance Tracking

Update `GOOGLESHEETS_UPDATE_SPREADSHEET` weekly:

```
| Stage | Entered | Converted | Conv Rate | Avg Time | Drop-off Point |
|-------|---------|-----------|-----------|----------|----------------|
| Aware→Consider | {n} | {n} | {%} | {days} | {where} |
| Consider→Trial | {n} | {n} | {%} | {days} | {where} |
| Trial→Active | {n} | {n} | {%} | {days} | {where} |
| Active→Paid | {n} | {n} | {%} | {days} | {where} |
| Paid→Expand | {n} | {n} | {%} | {days} | {where} |
```

## Implementation Order

Don't build everything at once. Priority:

1. **First:** Trial → Activation (biggest ROI, easiest to measure)
2. **Second:** Activation → Conversion (directly impacts revenue)
3. **Third:** At-Risk → Retention (protect existing revenue)
4. **Fourth:** Awareness → Consideration (top of funnel, longer cycle)
5. **Fifth:** Conversion → Expansion (requires mature customer base)

## Related Skills

- `email-sequence` — For detailed email copywriting within each stage
- `onboarding-cro` — For optimizing the activation experience
- `churn-prevention` — For detailed cancel flows and retention tactics
- `signup-flow-cro` — For optimizing the trial signup conversion
- `analytics-tracking` — For measuring stage-to-stage conversion
