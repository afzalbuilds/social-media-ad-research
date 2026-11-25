---
name: competitor-ad-research
description: Research the ads a competitor, brand or niche is running in the public ad libraries of Meta (Facebook and Instagram), TikTok, LinkedIn and Google (Search, YouTube, Shopping, Display), and summarize creatives, hooks, offers, formats, landing pages and how long each ad has run. Use when the user says "what ads is Nike running", "competitor ads", "Facebook ad library", "Meta ads for", "Instagram ads", "TikTok ads", "LinkedIn ads", "Google ads transparency", "YouTube ads of", "ad creative research", "winning ads", "longest-running ads", "ad inspiration for", "who advertises on", or asks how a brand advertises.
---

# Competitor ad research

You research ads by running Imzlab Technologies' ad library Actors on Apify through the **social-research** MCP server (some apps call it a connector). Each Actor is a tool on that server whose name contains the Actor slug, for example `muhammadafzal--facebook-ads-library-scraper`; depending on the app, the tool name may carry a server prefix, use underscores in place of hyphens, or be shortened with a suffix, so match on the start of the slug (`facebook-ads`, `tiktok-ads`, `linkedin-ads`, `google-ads`). The server also exposes `get-actor-run`, `get-dataset-items` and `get-key-value-store-record`, which you need to collect results.

Read `references/ad-library-contracts.md` for the exact input and output of every ad library Actor before your first call in a conversation.

## Steps

1. **Pin down the request.** You need an advertiser (brand name, website or Facebook Page link) or a keyword, the platforms, the country, and how many ads per platform. Infer what you can: a brand alone means all four libraries; "Facebook" or "Instagram" means Meta only; "B2B" or "LinkedIn" means LinkedIn first. Default count is 20 ads per platform, active ads first, country from the user's context or `ALL`. Ask only when neither an advertiser nor a keyword is given. When the user names a brand, look up or ask for its website: Google's library is searched best by domain.

2. **Route.**
   - Meta: `searchTerms` with the brand or keyword, or `pageUrls` when the user gives the Facebook Page. Set `adActiveStatus: "ACTIVE"` unless the user wants history.
   - TikTok: one run per brand or keyword; `searchMode: "advertiser"` for a brand, `keyword` for a topic. TikTok's library covers ads shown in Europe and the UK only; for a US-only brand, say so instead of reporting "no ads".
   - LinkedIn: `advertiser` for a brand (filter rows on `advertiserName`), `keyword` for a topic.
   - Google: `searchTerms` with the website domain; add `platforms: ["YOUTUBE"]` for video ads. Google is searched by advertiser or site, not by topic, so skip it for a pure keyword.

3. **Estimate the cost** with the price table in the contracts file. If the estimate is USD 0.50 or less, run now and report the cost afterwards. Above USD 0.50, show the estimate as one line ("about USD 1.49 for 100 ads on each of the four libraries, billed to your Apify account") and run only after the user agrees.

4. **Run the Actors.** Start the platform runs together (up to 4 at a time). Call each tool with the input shape from the contract file and never add fields it does not list. Set `maxResults` (and `maxAdsPerTarget` for Google) to the count.

5. **Wait for every run to finish.** A tool response with status `RUNNING` or `READY` is not a result. Call `get-actor-run` with the `runId` and `waitSecs: 45`, repeat until the status is `SUCCEEDED`, `FAILED`, `ABORTED` or `TIMED-OUT`, up to 8 times. Never report a run as done while it is still running, and never invent ads.

6. **Fetch the results**, whatever the run status was: `get-dataset-items` with the run's default `datasetId` and `limit` equal to `maxResults`, and `get-key-value-store-record` with the run's default `keyValueStoreId` and the record named in the contract (`OUTPUT` for Meta, TikTok and Google, `SUMMARY` for LinkedIn). The record explains a shortfall: the library holds fewer ads, the country filter, or the user's maximum cost per run.

7. **Analyze** only what the rows show:
   - per platform: ads found, active versus stopped, formats (image, video, carousel, catalog), placements;
   - the longest-running ads (Meta `daysRunning` or `startDate`, TikTok `firstShown` to `lastShown`, Google `shownDays`): an ad that keeps running is usually one that pays for itself, so lead with these;
   - recurring hooks (first line of the copy), offers and prices, claims, calls to action, landing pages and UTM campaign names;
   - reach where published (TikTok `uniqueUsersSeen`, Meta `euTotalReach`, Google times-shown ranges) and the size of the full ad set (Google `estimatedTotalMin` to `estimatedTotalMax`).

## Answer format

1. One line per platform: ads found, active, main formats.
2. A table of the top ads (up to 15 in chat): Platform, Advertiser, Format, Running (start date and days, or first and last shown), Hook (first 15 words of the copy, quoted), Call to action, Landing page, Link (`adLibraryUrl`, `adUrl` or `adSnapshotUrl`). Sort by days running.
3. "What stands out": three to six observations, each tied to the ads that show it (angles, offers, formats, landing pages, creative refresh rate). Label them as observations from the sample, not facts about the brand's whole strategy.
4. One line: "Apify cost: about USD X for N ads in M runs, billed to your Apify account." Work the cost out from the price table (runs × start fee + charged rows × per-row price). Never quote the usage, credit or cost figures that a run response or record carries.

Do not paste raw JSON. Link media rather than embedding it. For a Meta ad whose copy is a catalog placeholder such as `{{product.name}}`, quote a carousel card instead and call it a dynamic catalog ad. Offer the next step: export all rows as CSV, compare two competitors side by side, mine the audience's own words (`audience-insights` skill), or re-run in a week to see which ads were stopped.

## CSV export

When asked for a file, write `<brand-or-keyword>-ads-<YYYY-MM-DD>.csv` in the working folder (or as a downloadable file in chat) with columns: platform, advertiser, ad_id, format, status, first_shown, last_shown, days_running, copy, headline, cta, landing_url, platforms_or_placements, reach, ad_link, media_links. One row per ad; join lists with ` | `.

## Responsible use

The ad libraries are public transparency tools. Use the results for competitive research and creative inspiration. Remind the user once, in one sentence, not to copy another advertiser's creatives, trademarks or claims verbatim. Present political and issue ads neutrally, with their published funder, and do not help target or harass the people or groups an ad names.

## When things go wrong

- The social-research server is not connected or a tool call returns an authorization error: tell the user to sign in to Apify (a free account works) for the social-research server in their app's connector or MCP settings, then retry. In Claude this is the plugin's Connectors tab.
- A platform returns no ads: report it as "no ads in that library for this search", name the filters used (country, active only), and suggest the fix: a different spelling or the Facebook Page link for Meta, the website domain for Google, `ALL` countries, or inactive ads too. For TikTok, remember the library covers Europe and the UK only.
- LinkedIn rows from other companies with similar names: drop them and say how many were dropped.
- The run reports a charge limit or "maximum cost per run": the user's Apify spending limit per run stopped it; report how many ads were delivered and suggest raising the limit in Apify Console or lowering the count.
