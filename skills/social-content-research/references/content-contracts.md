# Social content Actor contracts

Prices are Apify Store pay-per-event prices on the FREE tier as of 2026-11-25; paid Apify plans are 2.5 to 20 percent lower. A row is charged when it is written to the dataset. Every run also charges one start event. Schemas were read from the Actors' live builds on 2026-11-25 and every output shape below was checked against a live run the same day.

All Actors belong to Apify user `muhammadafzal`. On the **social-research** MCP server the tool name is the Actor ID with `/` replaced by `--`, for example `muhammadafzal--youtube-video-channel-scraper`. Some apps add a server prefix, turn hyphens into underscores, or shorten a long name and add a suffix; match on the start of the Actor slug (the two YouTube tools differ in `video-channel` and `transcript`, the two Reddit tools in `buyer-intent` and `comment`). Each tool returns the run (`runId`, `status`, `storages.datasets.default.id`, `storages.keyValueStores.default.id`), not the rows. If a tool response already includes dataset items, use them; otherwise fetch them with `get-dataset-items`.

Run options (`memory`, `timeout`, `maxTotalChargeUsd`) go in the tool's `callOptions`, never inside `input`. Send only the input fields listed below. Every Actor handles its own network routing internally, so nothing else is needed.

## Shared rules

- Always set the Actor's result cap (`maxResults`, `maxResultsPerQuery`, `maxComments`) to what the user asked for, never leave the default.
- Always compute a cost from the prices in this file. A run response or summary record can include usage, credit or cost fields (`usageTotalUsd`, `estimatedResultCostUsd`, `providerCredits`); those are internal figures, not what the user is billed, so never report them as the cost.
- Inputs that return nothing (a video without captions, a missing channel) are not charged; the summary record lists them.
- Counts such as likes, views and followers can arrive as numbers or numeric strings; convert before sorting or adding.

---

## YouTube videos, search and channels: `youtube-video-channel-scraper`

Price: USD 0.003 per video or channel row, start USD 0.005 (256 MB). Optional AI-training permission check USD 0.0025 per video. About 5 seconds for a 20-result search; deep search 30 to 60 seconds per query.

Input:
```json
{
  "searchQueries": ["espresso machine review"],
  "maxResultsPerQuery": 20,
  "searchType": "video",
  "sortBy": "relevance",
  "uploadDate": "any",
  "duration": "any",
  "searchCountry": "US",
  "channelHandles": ["@jameshoffmann"],
  "latestVideos": 10,
  "videoUrls": ["https://www.youtube.com/watch?v=jNQXAC9IVRw"]
}
```
- Use any combination of the three lists. `searchQueries`: up to 20, each like the YouTube search box. `maxResultsPerQuery` up to 20 without deep search; set `"deepSearch": true` for more (up to about 700 per query).
- `searchType`: `video` | `channel` | `movie`. `sortBy`: `relevance` | `upload_date` | `view_count` | `rating`. `uploadDate`: `any` | `last_hour` | `today` | `this_week` | `this_month` | `this_year`. `duration`: `any` | `under4` | `4to20` | `over20`.
- `channelHandles`: `@handle` or channel links. `latestVideos` 0 to 100 newest uploads listed inside the channel row (included in the channel row's price).
- `videoUrls`: watch, youtu.be, Shorts or bare 11-character IDs, for full metadata of known videos.
- Leave `includeTrainingPermission` off unless the user asks whether a video may be used for AI training.

Dataset row (camelCase), `recordType` `video`: `source` (`search` | `videoUrl` | other), `input`, `searchQuery`, `searchRank`, `videoId`, `videoUrl`, `title`, `description`, `durationSeconds`, `durationText`, `viewCount`, `likeCount`, `commentCount`, `uploadDate`, `publishedTimeText` (for example `3w ago`), `tags`, `categories`, `thumbnailUrl`, `isLive`, `isShort`, `badges[]`, `hasCaptions`, `captionLanguages`, `channelId`, `channelHandle`, `channelName`, `channelUrl`, `channelVerified`, `scrapedAt`. Search rows carry views and the relative date but often `null` likes, comments and exact upload date; ask for the video by URL when those matter.

`recordType` `channel`: `channelId`, `channelHandle`, `channelName`, `channelUrl`, `subscriberCount`, `subscriberCountText`, `channelVideoCount`, `channelViewCount`, `channelDescription`, `joinedDate`, `country`, `links[]`, `latestVideos[]` (position, videoId, title, link, views, age).

Key-value record `OUTPUT`: `status`, `delivered`, `videosDelivered`, `channelsDelivered`, `chargedResults`, `settings`, `counts` (per kind: `delivered`, `unavailable`, `notFound`, `empty`, `invalidInput`, `duplicate`, `failed`), per-input outcomes.

---

## YouTube transcripts: `youtube-transcript-extractor`

Price: USD 0.003 per transcript, start USD 0.005 (256 MB). Videos without captions in the language are not charged. About 5 seconds per video.

Input:
```json
{
  "videoUrls": ["https://www.youtube.com/watch?v=jNQXAC9IVRw"],
  "language": "en",
  "captionSource": "manualFirst",
  "includeTimestamps": false,
  "includeSrt": false
}
```
- `videoUrls` is required: watch, youtu.be, Shorts, embed or live links, or bare video IDs. Playlist, channel and search links are not accepted; find videos first with the video Actor.
- `language`: a code such as `en`, `es`, `de`, `pt-BR`; a plain code matches regional tracks. `captionSource`: `manualFirst` | `autoFirst` | `manualOnly` | `autoOnly`.
- Keep `includeTimestamps: false` unless the user wants quotes with times; it makes rows much larger.

Dataset row (camelCase): `videoId`, `videoUrl`, `input`, `language`, `languageName`, `requestedLanguage`, `captionSource` (`manual` | `auto`), `manualLanguages[]`, `autoGeneratedLanguages[]`, `segmentCount`, `wordCount`, `characterCount`, `text` (full plain transcript), `segments` (with timestamps on), `srt`, `extractedAt`.

Key-value record `OUTPUT`: `status`, `delivered`, `chargedResults`, `counts` (`delivered`, `noCaptions`, `languageNotAvailable`, `emptyTrack`, `unavailable`, `invalidInput`, `duplicate`, `failed`), `stopReason`, `warnings[]`, `videos[]` (per video: `status`, `reason`, available languages).

---

## Instagram trending Reels: `instagram-trending-reels-research-scraper`

Price: USD 0.005 per Reel, start USD 0.016 (256 MB). `maxResults` 1 to 100. About 15 to 20 seconds.

Input:
```json
{
  "maxResults": 20,
  "batches": 1,
  "minLikes": 10000,
  "maxAgeDays": 7,
  "captionContains": "recipe"
}
```
- This Actor samples Instagram's current trending Reels feed. It does not search Instagram, read a given account or fetch a given Reel. `captionContains` only filters the sample, so a niche word can return few or no rows; raise `batches` (1 to 3) for a larger sample.
- `minLikes` and `maxAgeDays` (1 to 365) filter the sample.

Dataset row (camelCase): `id`, `permalink`, `caption`, `hashtags[]`, `creatorUsername`, `publishedAt`, `likeCount`, `commentCount`, `playCount`, `engagementsPerHour`, `sourceRank`, `researchRank`, `collectedAt`.

Key-value record `SUMMARY`: `status` (`data` | `empty` | other), `delivered`, `chargedResultEvents`, `warnings[]`.

---

## Threads: `threads-posts-profiles-search-scraper`

Price: USD 0.01 per row, start USD 0.02 (128 MB). `maxResults` 1 to 500 per run, `maxResultsPerTarget` 1 to 100 per handle, link or query. About 10 seconds.

Input:
```json
{
  "mode": "search",
  "queries": ["espresso"],
  "handles": ["zuck"],
  "postUrls": ["https://www.threads.com/@zuck/post/DgHQuRJm8lC"],
  "maxResults": 20,
  "maxResultsPerTarget": 20,
  "maxRequests": 10,
  "includeReplies": false
}
```
- `mode`: `search` (keyword search, `queries`, optional `startDate`/`endDate` as YYYY-MM-DD), `posts` (recent posts of `handles`), `profiles` (profile details of `handles`), `post` (one or more `postUrls`; `includeReplies: true` adds the replies Threads returns with the post), `users` (account search by `queries`). Send only the list the mode uses. At most 20 handles or queries.
- Keep `maxRequests` at 10 (1 to 20); it bounds retries.

Dataset row (camelCase), `type` `post` or `reply`: `id`, `url`, `username`, `fullName`, `text`, `publishedAt`, `likes`, `replies`, `reposts`, `quotes`, `shares`, `verified`, `media[]`, `hashtags[]`, `mentions[]`, `parentUrl` (set on replies), `source` (`{mode, target}`), `scrapedAt`. `type` `profile`: `id`, `url`, `username`, `fullName`, `biography`, `followers`, `verified`, `isPrivate`, `profilePictureUrl`, `bioLinks[]`.

Key-value record `OUTPUT`: `status` (`DATA` | `EMPTY` | other), `delivered`, `warnings[]`, `stopReason` (`RESULT_LIMIT` and others), `chargedEvents`.

---

## Reddit search: `reddit-buyer-intent-brand-monitor`

Price: USD 0.005 per post, start USD 0.006 (1 GB, one event). `maxResults` 1 to 500 unique posts across all searches. If you set `callOptions.maxTotalChargeUsd`, it must be at least USD 0.25 or the run is rejected. 30 to 90 seconds for 50 posts.

Input:
```json
{
  "queries": ["best espresso machine under 500"],
  "brandNames": ["Breville"],
  "subreddits": ["espresso", "Coffee"],
  "sort": "relevance",
  "timeframe": "year",
  "maxResults": 25
}
```
- `queries`: phrases to search on Reddit (product, problem, competitor or question wording). Each `brandNames` entry is also run as its own search and is flagged in the rows that mention it.
- `subreddits`: names without `r/`; empty searches all of Reddit.
- `sort`: always set `relevance` (the Actor's default `new` returns the newest posts on Reddit whether or not they fit). `timeframe`: `hour` | `day` | `week` | `month` | `year` | `all`.

Dataset row (camelCase): `id`, `title`, `text` (title plus body, truncated), `author`, `subreddit`, `url`, `createdAt`, `score` (upvotes), `numComments`, `matchedQueries[]`, `matchedBrands[]`, `buyerIntentScore` (0 to 100), `intentSignals[]` (for example `active research`, `comparison`, `recommendation request`), `source`, `scrapedAt`.

Data notes: judge relevance from the title and text. `matchedQueries` and `buyerIntentScore` are a strict exact-phrase heuristic and are often empty or 0 on clearly relevant posts (in the 2026-11-25 test, "Good espresso machine for less than $500?" scored 0), so never drop a row on those fields alone. Use `url` as the input for the comment Actor.

Key-value record `OUTPUT`: `delivered`, `routes` (candidate counts per data route), `warnings[]` ("Reddit direct search returned HTTP 403; using the fallback data route" is normal), `queries[]`, `brandNames[]`, `maxResults`.

---

## Reddit comments: `reddit-comment-scraper`

Price: USD 0.002 per comment, start USD 0.006 (256 MB). `maxComments` 1 to 5,000 across all posts. About 5 seconds for 100 comments.

Input:
```json
{
  "postUrls": ["https://www.reddit.com/r/espresso/comments/1uzgm6n/good_espresso_machine_for_less_than_500/"],
  "maxComments": 100,
  "sort": "top",
  "includeReplies": true,
  "maxDepth": 2
}
```
- `postUrls` is required: full Reddit post links. It is not a subreddit feed, user history or comment search; find posts first with the Reddit search Actor.
- `sort`: `confidence` | `top` | `new` | `controversial` | `old` | `qa`. `maxDepth` 0 to 10; 0 or `includeReplies: false` gives top-level comments only.

Dataset row (camelCase): `commentId`, `postId`, `postTitle`, `postUrl`, `sourceUrl`, `subreddit`, `author`, `body`, `score`, `createdAt`, `editedAt`, `parentId`, `depth` (0 is top level), `permalink`, `isStickied`, `isDistinguished`, `isRemoved`.

Key-value record `OUTPUT`: `status`, `recordsReturned`, `resultEvents`, `stopReason` (`completed` and others), `postsNotStarted`, `warnings[]`.

---

## Cost formula

cost = runs × start fee + charged rows × per-row price.

Examples on the FREE tier: 20 YouTube search results ≈ USD 0.065; one channel with its 10 latest uploads ≈ USD 0.008; 10 transcripts ≈ USD 0.035; 20 trending Reels ≈ USD 0.116; 20 Threads posts ≈ USD 0.22; 25 Reddit posts ≈ USD 0.131; 200 Reddit comments ≈ USD 0.406.
