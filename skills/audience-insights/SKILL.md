---
name: audience-insights
description: Mine what real people say about a product, brand, competitor or problem from Reddit discussions and comments, YouTube video transcripts and Threads replies, and turn it into pain points, objections, desired outcomes, the exact words customers use, and ad or content angles with quoted evidence. Use when the user asks "what do people say about", "customer pain points", "voice of customer", "objections to", "why do people choose", "what do customers complain about", "summarize the comments on this post", "summarize this YouTube video", "get the transcript of", "audience research for my ads", "words my customers use", or "Reddit research on".
---

# Audience insights

You mine public conversations by running Imzlab Technologies' Actors on Apify through the **social-research** MCP server (some apps call it a connector). Match tools on the start of the Actor slug: `reddit-buyer-intent` (Reddit search), `reddit-comment` (comments of given posts), `youtube-video-channel` (find videos), `youtube-transcript` (transcripts of given videos), `threads-posts` (Threads search and replies). The server also exposes `get-actor-run`, `get-dataset-items` and `get-key-value-store-record`.

The input and output of each Actor are in `../social-content-research/references/content-contracts.md`; read it before your first call in a conversation.

## Steps

1. **Pin down the subject**: a product, category, brand, competitor or problem, and what the user wants out of it (pain points for ads, objections for a sales page, feature requests, a summary of one post or video). If the user gives links to Reddit posts, YouTube videos or a Threads post, use those directly and skip discovery.

2. **Discover sources** (skip when links were given):
   - Reddit: search Actor with two to four phrasings of the subject (the problem, "best X for Y", "X vs Y", the brand name in `brandNames`), `sort: "relevance"`, `timeframe: "year"`, `maxResults` 25. Keep posts that are on topic by title and text, then pick the 5 with the most comments.
   - YouTube: video Actor search for reviews or comparisons, `maxResultsPerQuery` 10; pick the 3 to 5 most viewed that are on topic.
   - Threads (optional): `mode: "search"` with the subject, `maxResults` 20.

3. **Estimate the cost** of the whole plan (discovery plus collection) from the price table and show it with the plan in two lines. Default plan: Reddit search 25 posts + comments on 5 posts at up to 60 each + a 10-video YouTube search + 3 transcripts ≈ USD 0.79. If the estimate is USD 0.50 or less, run now; above it, run only after the user agrees.

4. **Collect**: comment Actor with the chosen `postUrls`, `maxComments` = 60 × posts, `sort: "top"`, `maxDepth: 2`; transcript Actor with the chosen `videoUrls` and `includeTimestamps: false`. Start them together.

5. **Wait for every run to finish** with `get-actor-run` (`waitSecs: 45`, up to 8 times), then fetch rows with `get-dataset-items` and the `OUTPUT` record with `get-key-value-store-record`. Never invent a comment, quote or transcript line.

6. **Analyze.** Read every comment and transcript. Group statements into themes and count how many separate comments or videos raise each theme. Keep only themes raised at least twice, unless the user asked for everything. For each theme keep one to three short verbatim quotes (at most 25 words each) with the link they came from (`permalink` for comments, `videoUrl` for transcripts). Weigh comments by `score` when ranking. Separate what people want, what frustrates them, what stops them buying, which alternatives they name, and the words and phrases they repeat.

## Answer format

1. Sources line: "N Reddit comments from P posts in r/…, T YouTube transcripts, ..." with the date range.
2. A table: Theme, Type (pain, desire, objection, praise, comparison), Mentions, Example quote (verbatim, in quotes), Source link. Sort by mentions.
3. "Their words": 8 to 15 phrases people use, verbatim.
4. "Alternatives named": brands or products mentioned, with counts.
5. "Angles": three to five ad, landing-page or content angles, each tied to a theme and its evidence.
6. One line: "Apify cost: about USD X for N rows in M runs, billed to your Apify account", worked out from the price table (runs × start fee + charged rows × per-row price). Never quote the usage, credit or cost figures that a run response or record carries.

For a single post or video, give a summary (five bullets), the main points of agreement and disagreement, and the quotes, in place of the table.

## Responsible use

Quote content, not people: do not show Reddit or Threads usernames unless the user asks who said something, and never compile what one person has said across posts. Quotes are for research; remind the user once that publishing someone's words in an ad needs their permission. YouTube transcripts belong to the video's creator; summarize them and quote briefly.

## When things go wrong

- The social-research server is not connected or a tool call returns an authorization error: tell the user to sign in to Apify (a free account works) for the social-research server in their app's connector or MCP settings, then retry.
- A video has no transcript: the transcript record lists it as `noCaptions` or `languageNotAvailable`, and it is not charged. Name the videos skipped; offer another language with `language` or `captionSource: "autoOnly"`.
- Reddit search returns off-topic posts: drop them, say how many, and refine the phrases or add `subreddits`.
- Few comments: the posts were small. Say so and offer more posts or a wider `timeframe`.
