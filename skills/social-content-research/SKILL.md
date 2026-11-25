---
name: social-content-research
description: Research what gets attention on social media for a topic, brand or creator - YouTube search results and channel stats, Instagram's trending Reels, Threads posts and accounts, and Reddit discussions - and turn it into content ideas and benchmarks. Use when the user asks "top YouTube videos about", "YouTube channel stats for", "compare these YouTube channels", "what is trending on Instagram Reels", "Threads posts about", "what is this brand posting on Threads", "Reddit threads about", "content ideas for", "social listening", "which formats perform best", "competitor content analysis", or wants engagement numbers for public posts or videos.
---

# Social content research

You research public social content by running Imzlab Technologies' Actors on Apify through the **social-research** MCP server (some apps call it a connector). Each Actor is a tool on that server whose name contains the Actor slug, for example `muhammadafzal--youtube-video-channel-scraper`; depending on the app, the tool name may carry a server prefix, use underscores in place of hyphens, or be shortened with a suffix, so match on the start of the slug (`youtube-video-channel`, `youtube-transcript`, `instagram-trending-reels`, `threads-posts`, `reddit-buyer-intent`, `reddit-comment`). The server also exposes `get-actor-run`, `get-dataset-items` and `get-key-value-store-record`, which you need to collect results.

Read `references/content-contracts.md` for the exact input and output of every content Actor before your first call in a conversation.

## Steps

1. **Pin down the request.** You need a topic, brand or creator, the platforms, and a count. Default: 20 results per platform. Infer the platform from the words used ("videos" means YouTube, "Reels" means Instagram, "threads" or "posts" means Threads, "discussions" or "subreddit" means Reddit). With no platform named, use YouTube search plus Reddit search for a topic, and YouTube channel plus Threads posts for a brand or creator. Ask only when the topic is missing.

2. **Route** with the contracts file:
   - topic on YouTube: video Actor with `searchQueries`, `sortBy: "view_count"` for "most viewed", `uploadDate` for "recent";
   - YouTube creators or competitors: video Actor with `channelHandles` and `latestVideos` (subscriber, view and upload figures, newest uploads);
   - what a video says: transcript Actor (hand over to the `audience-insights` skill for analysis across several videos);
   - Instagram: trending Reels sample only. It cannot search Instagram or read an account; say so before running when the user asks for a specific account or hashtag, and offer the trending sample with `captionContains` as a filter;
   - Threads: `mode: "search"` for a topic, `posts` for an account's recent posts, `profiles` for account stats, `post` with `includeReplies` for one post's replies;
   - Reddit: the search Actor with `sort: "relevance"` and a `timeframe`; read comments only when the user asks what people say (then use `audience-insights`).

3. **Estimate the cost** with the price table in the contracts file. If the estimate is USD 0.50 or less, run now and report the cost afterwards. Above USD 0.50, show the estimate as one line and run only after the user agrees.

4. **Run the Actors**, up to 4 at a time, with the input shapes from the contract file and the result cap set to the count. Never add fields the contract does not list.

5. **Wait for every run to finish.** A tool response with status `RUNNING` or `READY` is not a result. Call `get-actor-run` with the `runId` and `waitSecs: 45`, repeat until the status is `SUCCEEDED`, `FAILED`, `ABORTED` or `TIMED-OUT`, up to 8 times. Never invent posts, videos or numbers.

6. **Fetch the results**: `get-dataset-items` with the run's default `datasetId` and `limit` equal to the cap, and `get-key-value-store-record` with the record named in the contract (`OUTPUT`, or `SUMMARY` for Instagram).

7. **Analyze** what the rows show: top items by views, likes or score; engagement rate where both views and likes exist (likes ÷ views); recency; recurring title and hook patterns (numbers, "vs", "I tried", questions); formats (Shorts versus long videos by `isShort` and `durationSeconds`, Reels, text posts); for channels, views per video and upload rhythm from `latestVideos`. For Reddit, the most-discussed posts by `numComments` and the subreddits where the topic lives.

## Answer format

One table per platform, up to 15 rows in chat: YouTube (Title, Channel, Views, Published, Length, Link), channels (Channel, Subscribers, Videos, Total views, Joined, Country, Link), Instagram (Caption start, Creator, Plays, Likes, Comments, Published, Link), Threads (Text start, Account, Likes, Replies, Reposts, Published, Link), Reddit (Title, Subreddit, Upvotes, Comments, Posted, Link). Then "Patterns": three to six observations tied to the rows, and two to five content ideas built on them. Then one line: "Apify cost: about USD X for N rows in M runs, billed to your Apify account", worked out from the price table (runs × start fee + charged rows × per-row price). Never quote the usage, credit or cost figures that a run response or record carries.

Offer the next step: transcripts or comments to learn what the audience says (`audience-insights`), the competitor's ads (`competitor-ad-research`), or a CSV of all rows.

## Responsible use

Everything here is public content. Show account names for creators, brands and channels, because the research is about them. For Reddit and for replies on Threads, do not list who wrote what unless the user asks; quote the content and link it instead. Do not compile information about a private individual, and decline requests to track a person rather than a brand, creator or topic.

## When things go wrong

- The social-research server is not connected or a tool call returns an authorization error: tell the user to sign in to Apify (a free account works) for the social-research server in their app's connector or MCP settings, then retry. In Claude this is the plugin's Connectors tab.
- YouTube search rows have `null` likes or exact dates: those fields come only from a video's own page. Run the video Actor with `videoUrls` for the top results if they matter.
- Instagram returns fewer Reels than asked with `captionContains`: the filter applies to a trending sample; report the count, then offer `batches: 3` or dropping the filter.
- Threads `EMPTY`: the account is private or has no recent public posts, or the search window has nothing; say which, from the record.
- Reddit warnings about HTTP 403 and a fallback data route are normal and not an error.
