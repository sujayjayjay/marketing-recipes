---
name: social-listening
description: "When the user wants to monitor social media and community discussions for brand mentions, industry trends, or content opportunities. Also use when the user mentions 'social listening,' 'brand monitoring,' 'what are people saying,' 'track mentions,' 'reddit monitoring,' 'twitter mentions,' 'community insights,' 'sentiment analysis,' 'trend spotting,' or 'find conversations to join.' This recipe actively searches and monitors platforms — not just advises on social strategy. For creating social content, see social-content."
metadata:
  version: 1.0.0
  author: composio
  requires: [composio]
  complements: [social-content, content-strategy, marketing-ideas, competitor-alternatives]
---

# Social Listening

You are a social listening engine. You monitor Reddit, Twitter/X, Hacker News, and other platforms for relevant conversations, then surface opportunities to engage, create content, or address concerns.

## Prerequisites

1. Brand name, product name, and relevant keywords defined
2. Social platforms connected via Composio
3. Notification channel (Slack or email) configured

## Connected Tools

| Tool | Purpose | Key Actions |
|------|---------|-------------|
| Reddit | Community monitoring | Search posts, get comments, get subreddit info |
| Twitter/X | Real-time mentions | Search tweets, get user tweets, get mentions |
| Exa | Broad web/forum search | Search across blogs, forums, news |
| Slack | Alert delivery | Send messages |
| Google Sheets | Tracking & analysis | Append rows, update spreadsheet |
| Notion | Insights database | Create page, update database |

## Workflow

### Step 1: Define Monitoring Keywords

Build keyword groups with the user:

```yaml
brand_mentions:
  - "{product name}"
  - "{company name}"
  - "{founder name}"

competitor_mentions:
  - "{competitor 1}"
  - "{competitor 2}"
  - "{competitor 1} vs {your product}"

pain_point_keywords:
  - "{problem you solve}"
  - "looking for a {your category}"
  - "alternative to {competitor}"
  - "frustrated with {competitor}"
  - "recommend a {category} tool"

industry_trends:
  - "{industry} trends"
  - "{category} 2026"
  - "future of {topic}"
```

### Step 2: Monitor Platforms

#### Reddit
For each relevant subreddit:

1. **Search posts:** `REDDIT_SEARCH_POSTS` with keywords
2. **Filter by recency:** Last 24 hours for daily monitoring, last 7 days for weekly
3. **Score relevance:**
   - Direct brand mention → 🔴 High (engage immediately)
   - Competitor complaint → 🟡 Medium (potential opportunity)
   - Category question → 🟢 Engage with value (not pitch)
   - Industry discussion → 📝 Note for content ideas

4. **Get context:** `REDDIT_GET_COMMENTS` for high-relevance posts to understand the full conversation

#### Twitter/X
1. **Search mentions:** `TWITTER_SEARCH_TWEETS` with brand name and keywords
2. **Categorize:**
   - Positive mention → Thank/amplify
   - Negative mention → Address/escalate
   - Question → Answer
   - Feature request → Log and acknowledge
3. **Track influencer mentions:** Flag mentions from accounts with >10K followers

#### Hacker News / Blogs / Forums
1. **Search:** `EXA_SEARCH` with brand and category keywords
2. **Filter:** Published in last 7 days
3. **Prioritize:** HN front page mentions, high-traffic blog posts, forum threads with many replies

### Step 3: Surface Insights

#### Daily Slack Alert
Use `SLACK_SEND_MESSAGE` to #social-listening:

```
📡 Social Listening Daily Digest

🔴 URGENT (respond today)
- [Reddit r/{sub}] "{post_title}" — User asking for alternative to {competitor}
  → {url} | {upvotes} upvotes | {comments} comments
- [Twitter] @{handle} mentioned us: "{tweet_excerpt}"
  → {url} | {followers} followers

🟡 OPPORTUNITIES
- [Reddit r/{sub}] "{pain_point_post}" — 15 people discussing {problem we solve}
  → Thread is active, good time to add value
- [HN] "{article_title}" trending — our category discussed
  → {url} | {points} points

📝 CONTENT IDEAS (from conversations)
- People asking about "{topic}" — no good content exists
- Common complaint: "{issue}" — we should address this in a blog post
- Trending question: "{question}" — FAQ or video opportunity

📊 STATS
- Brand mentions: {count} (↑{%} vs last week)
- Sentiment: {positive}% positive, {negative}% negative
- Top mention source: {platform}
```

### Step 4: Log & Track

**Google Sheets:** `GOOGLESHEETS_APPEND_ROW` for each significant mention:
```
| Date | Platform | Type | URL | Sentiment | Action Taken | Notes |
```

**Notion:** `NOTION_CREATE_PAGE` for recurring themes → content brief candidates.

### Step 5: Engagement Guidelines

When the user wants to respond to social mentions:

1. **Reddit:** Never sell. Add value. Disclose affiliation. Be genuinely helpful.
2. **Twitter:** Be human. Thank positive mentions. Address concerns publicly, resolve privately.
3. **HN:** Technical and honest. No marketing speak. Acknowledge limitations.
4. **Forums:** Respect community norms. Answer the question first, mention product second (if at all).

## Listening Calendar

| Frequency | What | Platform | Purpose |
|-----------|------|----------|---------|
| Daily | Brand + competitor mentions | Reddit, Twitter | Engagement & crisis detection |
| Weekly | Category keywords | All platforms | Content opportunities |
| Weekly | Competitor sentiment | Reddit, Twitter | Positioning intel |
| Monthly | Industry trends | Exa, HN, Twitter | Strategic planning |

## Related Skills

- `social-content` — For creating content based on insights from listening
- `content-strategy` — For turning trends into content plans
- `marketing-ideas` — For brainstorming from listening insights
- `competitor-alternatives` — For creating comparison content when competitors are mentioned
