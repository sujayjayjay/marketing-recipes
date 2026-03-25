---
name: webinar-automation
description: "When the user wants to plan, promote, execute, and follow up on a webinar or virtual event. Also use when the user mentions 'webinar,' 'virtual event,' 'live session,' 'online workshop,' 'webinar promotion,' 'webinar follow-up,' 'event registration,' 'webinar funnel,' or 'post-webinar nurture.' This recipe handles the full lifecycle — registration page, promotion, reminders, and post-event follow-up sequences. For general launch strategy, see launch-strategy. For email sequences, see email-sequence."
metadata:
  version: 1.0.0
  author: composio
  requires: [composio]
  complements: [launch-strategy, email-sequence, social-content, copywriting, analytics-tracking]
---

# Webinar Automation

You are automating the full webinar lifecycle — from promotion to post-event nurture. You handle registrations, reminder sequences, social promotion, and follow-up campaigns through real tool integrations.

## Prerequisites

1. Webinar platform connected (Demio, Livestorm, or Zoom via Composio)
2. CRM connected (HubSpot or Salesforce)
3. Email tool connected (HubSpot, Mailchimp, or Resend)
4. Topic, date, speakers, and target audience defined

## Connected Tools

| Tool | Purpose | Key Actions |
|------|---------|-------------|
| Demio | Webinar hosting | Create event, get registrants, get attendees |
| HubSpot | Registration + nurture | Create form, create email, create workflow, search contacts |
| LinkedIn | Promotion | Create post, create event |
| Meta Ads | Paid promotion | Create campaign for registrations |
| Buffer | Social scheduling | Schedule promotion posts |
| Slack | Internal coordination | Send messages |
| Google Sheets | Tracking | Update registration/attendance data |
| Calendly | Speaker scheduling | Create event type |

## Workflow

### Phase 1: Setup (2-3 weeks before)

1. **Create webinar event:**
   - `DEMIO_CREATE_EVENT` with title, date/time, description, speakers
   - Get registration URL

2. **Create registration tracking:**
   - `HUBSPOT_CREATE_CONTACT_PROPERTY` for `webinar_{slug}_registered` and `webinar_{slug}_attended`
   - `HUBSPOT_CREATE_LIST` for registrants

3. **Write promotional copy** (use `copywriting` skill):
   - Email invitation (3 versions: cold list, warm list, VIP)
   - LinkedIn post
   - Twitter post
   - Landing page copy (if hosting custom page)

### Phase 2: Promotion (2 weeks before → day of)

#### Email Invitations
Schedule 3 invitation waves:

1. **T-14 days:** Main invitation to full list
   - `HUBSPOT_CREATE_MARKETING_EMAIL` with webinar invite copy
   - `HUBSPOT_SCHEDULE_EMAIL` for target date

2. **T-7 days:** Reminder to non-registrants
   - `HUBSPOT_SEARCH_CONTACTS` where `webinar_registered = false`
   - Fresh angle: speaker spotlight, agenda preview, or social proof ("{n} already registered")

3. **T-3 days:** Final push with urgency
   - "Only {n} spots left" or "Last chance to register"

#### Social Media
Schedule organic posts:

1. **T-14 days:** Announcement post
   - `LINKEDIN_CREATE_POST` and `BUFFER_CREATE_UPDATE` for Twitter
   
2. **T-7 days:** Speaker quote or agenda tease
3. **T-3 days:** Social proof (registrant count)
4. **T-1 day:** "Tomorrow!" reminder
5. **Day of:** "Starting in 1 hour" with direct join link

#### Paid Promotion (if budget available)
- `FACEBOOKADS_CREATE_CAMPAIGN` targeting:
  - Lookalike of existing customers
  - Interest-based targeting for webinar topic
  - Retargeting website visitors
- Objective: Lead Generation (with registration form)

### Phase 3: Reminders (day before + day of)

1. **T-24 hours:** Email reminder
   - `HUBSPOT_CREATE_MARKETING_EMAIL` to registrant list
   - Include: calendar invite, join link, what to expect

2. **T-1 hour:** Final reminder
   - Short email: "We're going live in 60 minutes — join here: {link}"

3. **Internal Slack alert:**
   - `SLACK_SEND_MESSAGE` to #marketing:
   ```
   🎙️ Webinar in 1 hour: "{title}"
   📊 {registrant_count} registered
   🔗 Host link: {host_url}
   📋 Run of show: {agenda}
   ```

### Phase 4: Post-Event Follow-up (within 24 hours)

1. **Pull attendance data:**
   - `DEMIO_GET_ATTENDEES` → who showed up, how long, engagement score
   - Update HubSpot: `HUBSPOT_UPDATE_CONTACT` → set `webinar_attended = true`

2. **Segment follow-up:**

   **Attended (hot leads):**
   - `HUBSPOT_CREATE_MARKETING_EMAIL` with:
     - Recording link
     - Slides/resources
     - CTA: "Book a demo" or "Start free trial"
     - Personalized based on engagement (high engagers get direct outreach)

   **Registered but didn't attend (warm leads):**
   - Different email: "Sorry we missed you — here's the recording"
   - Lower-pressure CTA
   - Include key takeaways so they get value without watching

   **High engagers (sales-ready):**
   - `SLACK_SEND_MESSAGE` to #sales-alerts with list of high-engagement attendees
   - Include: name, title, company, engagement score, questions asked
   - SDRs should follow up within 24 hours

3. **Update tracking:**
   - `GOOGLESHEETS_UPDATE_SPREADSHEET` with:
     - Registrations, attendance, attendance rate
     - Engagement metrics
     - Follow-up email performance

### Phase 5: Reporting

Compile webinar ROI:

```
📊 Webinar Report: "{title}"

📝 Registration
- Total registrants: {n}
- Registration sources: Email {%}, Social {%}, Ads {%}, Direct {%}
- Cost per registrant: ${total_promo_spend / registrants}

🎙️ Attendance
- Attended: {n} ({attendance_rate}%)
- Avg watch time: {minutes} min
- Peak concurrent: {n}
- Questions asked: {n}

📈 Follow-up
- Follow-up email open rate: {%}
- CTA clicks: {n}
- Demos booked: {n}
- Pipeline generated: ${amount}

💰 ROI
- Total cost: ${speakers + ads + tools}
- Pipeline generated: ${amount}
- Pipeline/cost ratio: {x}x
```

## Related Skills

- `launch-strategy` — For broader launch campaigns that include webinars
- `email-sequence` — For multi-email nurture sequences post-webinar
- `social-content` — For ongoing social promotion
- `copywriting` — For writing compelling webinar copy
- `analytics-tracking` — For measuring registration and conversion funnels
