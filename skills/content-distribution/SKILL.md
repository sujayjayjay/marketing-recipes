---
name: content-distribution
description: "When the user wants to distribute, repurpose, or syndicate content across multiple channels after creation. Also use when the user mentions 'distribute this post,' 'repurpose this for social,' 'share this everywhere,' 'cross-post,' 'syndicate,' 'turn this blog into tweets,' 'LinkedIn version of this,' 'promote this content,' or 'get eyeballs on this.' This recipe takes a single piece of content and actually publishes adapted versions across channels. For content planning, see content-strategy. For writing the original piece, see copywriting."
metadata:
  version: 1.0.0
  author: composio
  requires: [composio]
  complements: [content-strategy, social-content, copywriting, email-sequence]
---

# Content Distribution

You are a content distribution engine. Given a single piece of content (blog post, announcement, report), you repurpose it into channel-native formats and actually publish or schedule them across platforms.

## Prerequisites

1. Source content exists (blog post URL, document, or text)
2. Product marketing context loaded (for tone, audience, positioning)
3. Connected tools via Composio MCP server

## Connected Tools

| Tool | Purpose | Key Actions |
|------|---------|-------------|
| LinkedIn | Professional posts | Create post, create article |
| Twitter/X | Short-form distribution | Create tweet, create thread |
| Buffer | Multi-platform scheduling | Create update, schedule |
| HubSpot | Email newsletter | Create email, schedule send |
| Reddit | Community distribution | Create post in subreddits |
| Slack | Internal distribution | Send message to channels |
| Notion | Content tracking | Create page, update database |

## Workflow

### Step 1: Analyze Source Content

Read the source content and extract:
- **Core thesis** (1 sentence)
- **Key insights** (3-5 bullet points)
- **Quotable lines** (2-3 standalone sentences)
- **Data points** (statistics, numbers, results)
- **Target audience** (who cares about this)

### Step 2: Generate Channel-Native Versions

#### LinkedIn Post
Adapt for LinkedIn's algorithm and audience:
```
[Hook line — pattern interrupt or surprising stat]

[2-3 paragraphs expanding the core insight]
[Use line breaks for readability]
[Include a personal angle or opinion]

[Call-to-action: link in comments, DM me, etc.]

[3-5 relevant hashtags]
```

**Publish:** Use `LINKEDIN_CREATE_POST` with the adapted copy. If images are provided, attach them.

#### Twitter/X Thread
Break into thread format:
```
Tweet 1: Hook + core claim (under 280 chars, no link)
Tweet 2-5: Key insights (one per tweet, standalone value)
Tweet 6: Summary + link to full piece
Tweet 7: "If you found this useful, follow for more on {topic}"
```

**Publish:** Use `TWITTER_CREATE_TWEET` for each tweet in the thread, using `in_reply_to` for threading.

#### Email Newsletter Mention
If user has an active newsletter:

1. Write a 2-3 paragraph newsletter section:
   - Why this matters to subscribers
   - Key takeaway
   - Link to full piece

2. **Add to next newsletter:** Use `HUBSPOT_UPDATE_MARKETING_EMAIL` to add the section, or create a dedicated send with `HUBSPOT_CREATE_MARKETING_EMAIL`.

#### Reddit Post
Identify 2-3 relevant subreddits and adapt:
- Remove self-promotion tone
- Lead with value/insight, link at the end
- Match subreddit norms (check rules first)

**Publish:** Use `REDDIT_SUBMIT_POST` with subreddit-appropriate formatting.

#### Slack (Internal)
Share with team for amplification:

**Post:** Use `SLACK_SEND_MESSAGE` to #content or #marketing:
```
📝 New content published: "{title}"
{one-line summary}
🔗 {url}

💡 Engagement ask: Like/comment on LinkedIn within the first hour to boost reach.
LinkedIn post: {linkedin_url}
```

### Step 3: Track Distribution

Create or update a content tracking record:

**Notion:** Use `NOTION_CREATE_PAGE` in content database:
```
Title: {content_title}
Published: {date}
Channels: LinkedIn, Twitter, Email, Reddit
LinkedIn URL: {url}
Tweet URL: {url}
Status: Distributed
```

### Step 4: Schedule Follow-up

- **Day 1:** Check initial engagement metrics
- **Day 3:** Repurpose top-performing version into a different format (e.g., carousel from thread)
- **Day 7:** Pull performance data and update tracking

## Repurposing Matrix

| Source Format | → LinkedIn | → Twitter | → Email | → Reddit |
|--------------|------------|-----------|---------|----------|
| Blog post | Key insight + opinion | Thread (5-7 tweets) | Newsletter section | Value-first summary |
| Case study | Results + learnings | Stats thread | Dedicated send | "How we did X" |
| Product update | Feature announcement | Single tweet + gif | Release notes | "Show HN" style |
| Research/data | Data visualization | Chart + insight | Data brief | Discussion starter |
| Podcast/video | Key quotes | Clip + quote thread | Episode summary | Transcript excerpt |

## Rules

1. **Never cross-post identical content.** Each platform gets a native version.
2. **LinkedIn posts should NOT start with a link.** Hook first, link in comments.
3. **Twitter threads need standalone value per tweet.** Don't just split sentences.
4. **Reddit hates self-promotion.** Lead with genuine value, disclose if it's your content.
5. **Include UTMs on every link.** `utm_source={platform}&utm_medium=organic&utm_campaign=content-distribution`

## Related Skills

- `content-strategy` — For planning what content to create (use before this recipe)
- `social-content` — For detailed social media content guidelines
- `copywriting` — For writing the original piece
- `email-sequence` — For multi-email nurture (vs. single distribution send)
