<h1 align="center">Marketing Recipes</h1>

<p align="center">
Executable marketing workflow recipes for AI agents. Each recipe connects real tools via <a href="https://composio.dev/toolkits">Composio</a> to automate end-to-end GTM workflows.
</p>

<p align="center">
  <a href="https://awesome.re">
    <img src="https://awesome.re/badge.svg" alt="Awesome" />
  </a>
  <a href="https://makeapullrequest.com">
    <img src="https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square" alt="PRs Welcome" />
  </a>
  <a href="LICENSE">
    <img src="https://img.shields.io/badge/License-MIT-blue.svg?style=flat-square" alt="License: MIT" />
  </a>
</p>

Built to complement [coreyhaines31/marketingskills](https://github.com/coreyhaines31/marketingskills). Where Corey's skills teach your agent *how to think* about marketing, these recipes teach it *how to execute* — actually publishing campaigns, pulling reports, scoring leads, and monitoring competitors through real API calls.

---

## How This Is Different

**Corey's skills** (knowledge-based):
```
"Write homepage copy using the PAS framework with social proof..."
→ Agent writes great copy in the chat
→ You still have to copy-paste it, go to HubSpot, format it, schedule it
```

**These recipes** (execution-based):
```
"Launch this campaign across email, LinkedIn, and Meta Ads"
→ Agent creates the HubSpot email, schedules it, posts to LinkedIn,
  sets up Meta Ads campaign, applies UTMs, and alerts the team in Slack
```

Same quality thinking. Actually gets done.

---

## Available Recipes

| Recipe | What It Automates | Tools Used |
|--------|-------------------|------------|
| [campaign-launch](skills/campaign-launch/) | Coordinate and publish campaigns across email, social, and paid ads simultaneously | HubSpot, LinkedIn, Meta Ads, Google Ads, Buffer, Slack |
| [content-distribution](skills/content-distribution/) | Take one piece of content and publish adapted versions across every channel | LinkedIn, Twitter/X, Buffer, HubSpot, Reddit, Slack, Notion |
| [lead-scoring-automation](skills/lead-scoring-automation/) | Score leads in your CRM based on firmographic + behavioral signals, route hot leads to sales | HubSpot, Salesforce, Apollo, Clearbit, Slack |
| [competitive-monitoring](skills/competitive-monitoring/) | Track competitor pricing, features, SEO, and social mentions — auto-update battle cards | Ahrefs, SEMrush, Firecrawl, Exa, Reddit, Twitter, Google Sheets, Slack |
| [cross-channel-reporting](skills/cross-channel-reporting/) | Pull live data from all channels and build unified marketing performance reports | GA4, Meta Ads, Google Ads, LinkedIn Ads, HubSpot, Google Sheets, Slack |
| [social-listening](skills/social-listening/) | Monitor Reddit, Twitter, and forums for brand mentions, competitor complaints, and content opportunities | Reddit, Twitter, Exa, Slack, Google Sheets, Notion |
| [webinar-automation](skills/webinar-automation/) | Full webinar lifecycle — promotion, reminders, follow-up sequences, ROI reporting | Demio, HubSpot, LinkedIn, Meta Ads, Buffer, Slack, Google Sheets |
| [customer-journey-orchestration](skills/customer-journey-orchestration/) | Multi-channel lifecycle automation from first touch through retention | HubSpot, ActiveCampaign, Customer.io, Intercom, OneSignal, Slack |

## How Skills + Recipes Work Together

These recipes are designed to plug into Corey's existing skills:

```
                        ┌──────────────────────┐
                        │ product-marketing-    │
                        │ context (Corey's)     │
                        └──────────┬───────────┘
                                   │
              ┌────────────────────┼────────────────────┐
              │                    │                     │
     ┌────────▼────────┐  ┌───────▼───────┐  ┌─────────▼────────┐
     │ THINK (Corey's) │  │ PLAN (Corey's)│  │ EXECUTE (Recipes)│
     │                  │  │               │  │                  │
     │ copywriting      │  │ launch-       │  │ campaign-launch  │
     │ page-cro         │  │   strategy    │  │ content-         │
     │ ad-creative      │  │ content-      │  │   distribution   │
     │ cold-email       │  │   strategy    │  │ lead-scoring-    │
     │ marketing-       │  │ pricing-      │  │   automation     │
     │   psychology     │  │   strategy    │  │ competitive-     │
     │ revops           │  │ ab-test-setup │  │   monitoring     │
     └─────────────────┘  └───────────────┘  └──────────────────┘
```

**Example flow:**
1. Use `content-strategy` (Corey's) to plan what to write
2. Use `copywriting` (Corey's) to write the blog post
3. Use `content-distribution` (this repo) to publish it everywhere
4. Use `cross-channel-reporting` (this repo) to measure performance

---

## Installation

### Option 1: npx skills (Recommended)

```bash
# Install all recipes
npx skills add sujayjayjay/marketing-recipes

# Install specific recipes
npx skills add sujayjayjay/marketing-recipes --skill campaign-launch lead-scoring-automation

# List available recipes
npx skills add sujayjayjay/marketing-recipes --list
```

### Option 2: Clone and Copy

```bash
git clone https://github.com/sujayjayjay/marketing-recipes.git
cp -r marketing-recipes/skills/* .agents/skills/
```

### Option 3: Git Submodule

```bash
git submodule add https://github.com/sujayjayjay/marketing-recipes.git .agents/marketing-recipes
```

### Prerequisites: Connect Your Tools

These recipes use Composio for tool integrations. Set up once:

```bash
# Install Composio MCP server
npx @composio/mcp@latest setup

# Connect tools you'll use
# (each tool authenticates via OAuth in your browser)
```

When you first use a recipe that needs a tool, the agent will prompt you to authenticate it. The connection persists across sessions.

---

## How Recipes Use Composio

Each recipe references specific Composio actions like `HUBSPOT_CREATE_MARKETING_EMAIL` or `LINKEDIN_CREATE_POST`. These aren't pseudo-code — they're real API actions the agent calls through the Composio MCP server.

**You don't need to know the APIs.** Just tell the agent what you want:
```
"Launch my blog post across LinkedIn, Twitter, and our email newsletter"
```

The agent reads the recipe, identifies the right Composio actions, authenticates if needed, and executes.

---

## Contributing

Found a gap? Have a workflow you've automated? PRs welcome!

### Recipe Quality Checklist

- [ ] Follows SKILL.md format with YAML frontmatter
- [ ] `complements` field links to related Corey's skills
- [ ] Specific Composio action names (not generic "call the API")
- [ ] Step-by-step workflow with clear triggers
- [ ] Edge cases covered
- [ ] Related skills section

### What We're Looking For

- 🔴 **Ad spend optimization** — auto-pause underperformers, reallocate budget
- 🔴 **SEO content pipeline** — keyword research → outline → draft → publish → track rankings
- 🔴 **Affiliate/partner management** — track referrals, calculate commissions, send payouts
- 🔴 **Product-led growth automation** — usage-based triggers for upgrade nudges
- 🔴 **Event marketing beyond webinars** — conferences, meetups, sponsorships

---

## License

[MIT](LICENSE) — Use these however you want.
