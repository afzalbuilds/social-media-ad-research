# Social Media & Ad Library Research for Claude and other AI agents

Research what competitors advertise and what audiences say, without leaving Claude. Ask about a brand, a website or a niche and the plugin searches the **public ad libraries of Meta (Facebook and Instagram), TikTok, LinkedIn and Google (Search, YouTube, Shopping, Display)**, finds the **top YouTube videos and channels, Instagram's trending Reels, Threads posts and Reddit discussions**, reads **YouTube transcripts and Reddit comments**, and turns it into tables, patterns and quoted evidence.

It works in Claude chat, in Cowork and in Claude Code, and the same repository also installs as a native plugin in OpenAI Codex, Gemini CLI, GitHub Copilot, Grok, Cursor, Hermes Agent and OpenCode, with the skills usable in many more agents (see [Other AI agents](#other-ai-agents-codex-gemini-cli-copilot-and-more)). There are no developer accounts to set up and nothing to configure. Lookups run as pay-per-result Apify Actors on your own Apify account, and you only pay for results that come back.

## What you can ask

- "What ads is Allbirds running on Meta, TikTok, LinkedIn and Google right now? Which have run longest?"
- "Show me active Facebook and Instagram ads for 'meal kit' in the UK, video only."
- "Which companies advertise on LinkedIn for 'marketing automation', and what do their ads say?"
- "Top 20 YouTube videos about espresso machine reviews this year, sorted by views."
- "Compare these three YouTube channels: subscribers, views per video and how often they post."
- "What is trending on Instagram Reels this week with more than 100k likes?"
- "What do people on Reddit complain about with budget espresso machines? Give me quotes."
- "Summarize the comments on this Reddit thread and the transcript of this YouTube video."
- "Build a weekly report on our three competitors' ads and tell me what changed since last week."
- "What will a 100-ad check on all four ad libraries cost?"

Claude picks the right sources, estimates the cost, runs them, waits for the results and shows one table per platform, with links to every ad, video and post. When fewer results come back than you asked for, it tells you why.

## Sources covered

| Need | Source used | Per run | What you get |
|---|---|---|---|
| Meta ads (Facebook, Instagram, Messenger, Audience Network), 50+ countries | Facebook Ads Library Scraper | up to 1,000 ads | copy, headline, call to action, landing page, images, videos, carousel cards, placements, start date, days running, active flag, page likes, EU reach and spend where published |
| TikTok ads shown in Europe and the UK | TikTok Ads Library Scraper | up to 100 ads | caption, advertiser, first and last shown, unique users seen (range), landing page, target countries, video links |
| LinkedIn ads | LinkedIn Ads Library Scraper | up to 100 ads | advertiser, ad type, text, headline, image or video, paid-for-by |
| Google ads on Search, YouTube, Shopping, Maps, Play and Display | Google Ads Transparency Scraper | up to 50,000 ads | advertiser and legal name, verification, format, first and last shown, preview, regions, times shown (EU), audience criteria on request |
| YouTube search and channel stats | YouTube Video & Channel Scraper | up to 700 results per query | title, views, length, upload date, channel; for channels subscribers, total views, video count, join date, latest uploads |
| What a video says | YouTube Transcript Extractor | any number of videos | full transcript text, language, manual or automatic captions, optional timestamps |
| What's trending on Instagram | Instagram Trending Reels Scraper | up to 100 Reels | caption, hashtags, creator, plays, likes, comments, engagement per hour |
| Threads | Threads Posts, Profiles & Search Scraper | up to 500 rows | keyword search, an account's posts, profile stats, a post's replies, with likes, replies and reposts |
| Reddit discussions | Reddit Buyer-Intent & Brand Monitor | up to 500 posts | title, text, subreddit, upvotes, comment count, brand mentions, buyer-intent signals |
| Reddit comments | Reddit Comment Scraper | up to 5,000 comments | comment text, score, depth, date, link |

Not included: private accounts or groups, direct messages, follower lists, personal profiles of private individuals, or anything behind a login.

## How to set it up

1. Install the plugin from the Claude directory (claude.ai > Customize > Plugins) or in Claude Code with `claude plugin install social-media-ad-research@imzlab-social-research` after adding the marketplace `afzalbuilds/social-media-ad-research`.
2. Open the plugin's **Connectors** tab and connect the **social-research** server. It is Apify's official MCP server (`mcp.apify.com`) limited to the ten research Actors, so a browser window opens for you to sign in or create a free Apify account. Free accounts include monthly usage credit, so you can try it without adding a card. Claude may note that the server is not a separately registered directory connector; that is expected, because it is Apify's hosted server rather than one Imzlab runs.
3. Ask Claude.

In Claude Code, `/mcp` shows the connection state, and `/social-media-ad-research:competitor-ads allbirds.com` runs a lookup directly.

## Other AI agents: Codex, Gemini CLI, Copilot and more

The skills and the **social-research** server are the same in every app; only the manifest each app reads differs. Every app signs you in to Apify in the browser, and results are billed to your own Apify account exactly as in Claude.

| App | Install | What loads |
|---|---|---|
| Claude (chat, Cowork, Claude Code) | [How to set it up](#how-to-set-it-up) | skills, commands, competitor-intel-reporter agent, social-research server |
| OpenAI Codex (CLI and app) | [OpenAI Codex](#openai-codex) | skills, social-research server |
| Gemini CLI (and Qwen Code) | [Gemini CLI](#gemini-cli) | skills, commands, competitor-intel-reporter subagent, social-research server |
| GitHub Copilot CLI and VS Code | [GitHub Copilot](#github-copilot-cli-and-vs-code) | skills, commands, competitor-intel-reporter agent, social-research server |
| Grok Build | [Grok Build](#grok-build) | skills, commands, competitor-intel-reporter agent, social-research server |
| Cursor and Grok Bot | [Grok Bot and Cursor](#grok-bot-and-cursor) | skills, commands, competitor-intel-reporter agent, social-research server |
| Hermes Agent | [Hermes Agent](#hermes-agent) | skills, social-research server |
| OpenCode | [OpenCode](#opencode) | skills, commands, competitor-intel-reporter subagent, social-research server |
| Antigravity, Kiro, Devin, Factory Droid, Augment, Junie, Mistral Vibe | [More agents](#more-agents) | skills and social-research server, plus commands and agent where the app supports them |
| Any other agent with skills support | [More agents](#more-agents) | skills, plus the server URL added by hand |

Checked on 2026-11-25: the package passes the plugin validators of Claude Code, Gemini CLI, Grok Build and Hermes Agent, and every Actor behind the social-research server was run live the same day. Install, Apify sign-in and a live request were not repeated inside each app; the formats are the ones the sibling Imzlab plugins were install-tested with on 2026-11-16 (Codex 0.167, Gemini CLI 0.68.0, GitHub Copilot CLI 1.0.112, OpenCode 1.18.27).

### OpenAI Codex

Needs Codex CLI 0.142 or newer (`codex update`). The Codex app uses the same settings, so the plugin appears there too.

```bash
codex plugin marketplace add afzalbuilds/social-media-ad-research
```

```bash
codex plugin add social-media-ad-research@imzlab-social-research
```

```bash
codex mcp login social-research
```

The last command opens Apify's sign-in page in your browser. Then ask Codex in plain words. You can also call a skill by name, for example `$competitor-ad-research`. Codex loads the skills and the social-research server, but not the plugin's commands or the competitor-intel-reporter agent.

### Gemini CLI

```bash
gemini extensions install https://github.com/afzalbuilds/social-media-ad-research
```

When Gemini CLI first connects to the social-research server it opens Apify's sign-in page in your browser; if it does not, run `/mcp auth social-research` inside Gemini CLI. The extension adds the skills, the `/competitor-ads`, `/social-trends`, `/audience-insights` commands and the competitor-intel-reporter subagent. Update it later with `gemini extensions update social-media-ad-research`.

Qwen Code installs the same extension: `qwen extensions install afzalbuilds/social-media-ad-research`.

### GitHub Copilot CLI and VS Code

```bash
copilot plugin marketplace add afzalbuilds/social-media-ad-research
```

```bash
copilot plugin install social-media-ad-research@imzlab-social-research
```

In a Copilot CLI session, run `/mcp auth social-research` to sign in to Apify. The plugin adds the skills, the `/competitor-ads`, `/social-trends`, `/audience-insights` commands, the competitor-intel-reporter agent and the social-research server.

In VS Code, turn on the `chat.plugins.enabled` setting, run **Chat: Install Plugin from Source** from the Command Palette and enter `afzalbuilds/social-media-ad-research`.

### Grok Build

```bash
grok plugin install afzalbuilds/social-media-ad-research
```

Grok asks you to confirm that you trust the plugin; its social-research server stays off until you do. Then open `/mcps`, select **social-research** and press `i` to sign in to Apify. Then ask Grok about a brand's ads.

### Grok Bot and Cursor

The plugin is not listed in their marketplaces yet. Once it is, add it from the marketplace and sign in to Apify when the social-research server asks.

On grok.com you can use the Actors today without the skills: open Connectors, add a custom connector and paste the server URL below.

### Hermes Agent

Needs Hermes 0.21.6 or newer, which loads Agent Plugins packages (a root `plugin.json`).

```bash
hermes plugins install afzalbuilds/social-media-ad-research --no-enable
```

```bash
hermes plugins enable social-media-ad-research
```

The package declares the social-research server, but Hermes signs in to a remote server only when your own configuration asks for it. Register the server under the same name with browser sign-in, so Hermes uses your Apify account for it. Run this in a terminal, not from a script, because Hermes starts the sign-in only when it can talk to you:

```bash
hermes mcp add social-research --auth oauth --url "https://mcp.apify.com/?tools=muhammadafzal/facebook-ads-library-scraper,muhammadafzal/tiktok-ads-library-scraper,muhammadafzal/linkedin-ads-library-scraper,muhammadafzal/google-ads-library-scraper,muhammadafzal/youtube-video-channel-scraper,muhammadafzal/youtube-transcript-extractor,muhammadafzal/instagram-trending-reels-research-scraper,muhammadafzal/threads-posts-profiles-search-scraper,muhammadafzal/reddit-buyer-intent-brand-monitor,muhammadafzal/reddit-comment-scraper"
```

Hermes opens Apify's consent page in your browser. After you allow access it lists the server's tools (the ten Actors plus the run, dataset and record tools); press Enter to enable them all. `hermes mcp test social-research` confirms the connection, and `hermes mcp login social-research` signs in again later if the access is ever revoked.

Every request is a paid Actor run on your Apify account, so the package marks the server `untrusted`: Hermes asks before each call and refuses them in unattended runs. Your own server entry replaces the package's entry, including that setting; add `trust: untrusted` under `mcp_servers.social-research` in `~/.hermes/config.yaml` to keep the prompts.

To install only one skill instead of the whole package: `hermes skills install afzalbuilds/social-media-ad-research/skills/competitor-ad-research`. The social-research server still has to be added as above.

### OpenCode

OpenCode has no plugin bundle, so the skills, the server and the extras are added separately:

```bash
npx skills add afzalbuilds/social-media-ad-research -a opencode -g
```

```bash
opencode mcp add social-research --url "https://mcp.apify.com/?tools=muhammadafzal/facebook-ads-library-scraper,muhammadafzal/tiktok-ads-library-scraper,muhammadafzal/linkedin-ads-library-scraper,muhammadafzal/google-ads-library-scraper,muhammadafzal/youtube-video-channel-scraper,muhammadafzal/youtube-transcript-extractor,muhammadafzal/instagram-trending-reels-research-scraper,muhammadafzal/threads-posts-profiles-search-scraper,muhammadafzal/reddit-buyer-intent-brand-monitor,muhammadafzal/reddit-comment-scraper"
```

```bash
opencode mcp auth social-research
```

For the `/competitor-ads`, `/social-trends`, `/audience-insights` commands and the competitor-intel-reporter subagent, copy the `opencode/commands` and `opencode/agents` folders of this repository into `~/.config/opencode/`.

### More agents

| App | How to install |
|---|---|
| Google Antigravity | `agy plugin install https://github.com/afzalbuilds/social-media-ad-research` |
| Kiro | Powers panel, **Add Custom Power**, **Import power from GitHub**, then `https://github.com/afzalbuilds/social-media-ad-research` |
| Devin (formerly Windsurf) | `devin plugins install afzalbuilds/social-media-ad-research` |
| Factory Droid | `droid plugin marketplace add afzalbuilds/social-media-ad-research`, then `droid plugin install social-media-ad-research@imzlab-social-research --scope user` |
| Augment (Auggie CLI) | `auggie plugin marketplace add afzalbuilds/social-media-ad-research`, then enable the plugin in `/plugins` |
| JetBrains Junie CLI | `/extensions marketplace add afzalbuilds/social-media-ad-research`, then `/extensions install social-media-ad-research` |
| Mistral Vibe | clone this repository into `~/.vibe/plugins/social-media-ad-research`, run `/reload-plugins`, then `/mcp login social-research` |
| Cline, Kilo Code, Amp, Goose and other agents that read Agent Skills | `npx skills add afzalbuilds/social-media-ad-research` installs the skills; then add the server URL below as a remote MCP server with OAuth in that app |

### Server URL

Any MCP client can use the Actors directly by adding this URL as a remote (streamable HTTP) server with OAuth:

```
https://mcp.apify.com/?tools=muhammadafzal/facebook-ads-library-scraper,muhammadafzal/tiktok-ads-library-scraper,muhammadafzal/linkedin-ads-library-scraper,muhammadafzal/google-ads-library-scraper,muhammadafzal/youtube-video-channel-scraper,muhammadafzal/youtube-transcript-extractor,muhammadafzal/instagram-trending-reels-research-scraper,muhammadafzal/threads-posts-profiles-search-scraper,muhammadafzal/reddit-buyer-intent-brand-monitor,muhammadafzal/reddit-comment-scraper
```

## Pricing

The plugin itself is free. Each result is billed by Apify on your account at the Actor's pay-per-event price. Prices below are the Apify Store prices on the FREE tier as of 2026-11-25. Paid Apify plans get a discount, and searches that find nothing charge no results.

| Source | Per result | Per run start | Example |
|---|---|---|---|
| Meta ad library | USD 0.003 per ad | USD 0.015 | 20 ads: about USD 0.075 |
| TikTok ad library | USD 0.005 per ad | USD 0.01 | 20 ads: about USD 0.11 |
| LinkedIn ad library | USD 0.005 per ad | USD 0.01 | 20 ads: about USD 0.11 |
| Google ads transparency | USD 0.0015 per ad (USD 0.0045 with details) | USD 0.005 | 20 ads: about USD 0.035 |
| YouTube videos and channels | USD 0.003 | USD 0.005 | 20 search results: about USD 0.065 |
| YouTube transcripts | USD 0.003 | USD 0.005 | 10 transcripts: about USD 0.035 |
| Instagram trending Reels | USD 0.005 | USD 0.016 | 20 Reels: about USD 0.12 |
| Threads | USD 0.01 | USD 0.02 | 20 posts: about USD 0.22 |
| Reddit search | USD 0.005 per post | USD 0.006 | 25 posts: about USD 0.13 |
| Reddit comments | USD 0.002 per comment | USD 0.006 | 200 comments: about USD 0.41 |

One brand on all four ad libraries at 20 ads each costs about USD 0.33. Before anything estimated above USD 0.50, Claude tells you the estimate and asks before running. Ask "what will this cost" to get an estimate without running anything.

## Data and privacy

- Your request (brand, website, keyword, links) is sent to Apify's hosted MCP server (`mcp.apify.com`), which runs the Actor on your Apify account. The Actors read public ad libraries and public posts, videos, transcripts and comments.
- Results are stored in your Apify account's datasets for your plan's retention period, where you can download them as CSV, Excel or JSON. The plugin stores nothing itself and sends nothing to any other service.
- The skills quote content rather than people: Reddit and Threads usernames are shown only when you ask who said something, and requests to track a private person are declined.
- Ad creatives, videos and posts belong to their owners. Use them for research and inspiration, not to copy.
- You authorize Apify through OAuth in the plugin's Connectors tab. The plugin itself holds no Apify account details.
- Full policy: https://imzlab.com/privacy#social-research. Terms: https://imzlab.com/terms#social-research. Support: https://imzlab.com/support#social-research

## Frequently asked questions

**Why does TikTok show no ads for a US brand?** TikTok's public ad library lists ads shown in Europe (EU, EEA, Switzerland) and the UK. A brand that advertises only in the US is not in it.

**Do I get spend and impressions?** Only where the platform publishes them: Meta for political and issue ads and EU reach, TikTok as a unique-users range, Google as times-shown ranges for ads shown in the EU. Everything else is not public.

**Can it search Instagram for a hashtag or account?** No. The Instagram source samples the current trending Reels feed and can filter it by caption words. For a brand's Instagram ads, use the Meta ad library, which covers Instagram placements.

**How does it find "winning" ads?** It sorts by how long an ad has been running and whether it is still active, the most reliable public signal that an ad pays for itself. The Meta source also gives its own 0 to 100 score; the plugin labels it as a heuristic.

**Is this an official Meta, TikTok, LinkedIn, Google, YouTube or Reddit tool?** No. This plugin and the Actors are independent and not affiliated with Meta, TikTok, LinkedIn, Google, YouTube, Instagram, Threads, Reddit or Apify.

## Skills and commands in this plugin

- **competitor-ad-research**: a brand's or niche's ads in the Meta, TikTok, LinkedIn and Google ad libraries, sorted by days running, with patterns and CSV export.
- **social-content-research**: top YouTube videos and channels, trending Reels, Threads posts and Reddit threads, with benchmarks and content ideas.
- **audience-insights**: pain points, objections, desired outcomes and customer language from Reddit comments, YouTube transcripts and Threads replies, with quotes and links.
- **social-research-cost-estimate**: estimate the Apify cost before running.
- **/social-media-ad-research:competitor-ads**, **/social-media-ad-research:social-trends** and **/social-media-ad-research:audience-insights** commands (Claude Code, Cowork, Gemini CLI, Copilot, Grok Build, OpenCode).
- **competitor-intel-reporter** agent (Claude Code, Cowork, Gemini CLI, Copilot, Grok Build, OpenCode): unattended report on several competitors with a spending cap, a CSV of every ad and a Markdown report on disk, including what changed since the last report.

Codex and Hermes Agent load the skills and the social-research server only; there, ask in plain words. In Codex the skills can also be called by name, for example `$competitor-ad-research`.

## Source and support

Source: https://github.com/afzalbuilds/social-media-ad-research. Issues and feature requests: https://github.com/afzalbuilds/social-media-ad-research/issues. The Actors are published on Apify Store by Imzlab Technologies under the `muhammadafzal` account.

Last verified: 2026-11-25. All ten Actors were run live with small inputs on that date; the output fields in the skills' reference files come from those runs.
