# Ad library Actor contracts

Prices are Apify Store pay-per-event prices on the FREE tier as of 2026-11-25; paid Apify plans are 2.5 to 20 percent lower. A row is charged when it is written to the dataset. Every run also charges one start event. Schemas were read from the Actors' live builds on 2026-11-25 and every output shape below was checked against a live run the same day.

All Actors belong to Apify user `muhammadafzal`. On the **social-research** MCP server the tool name is the Actor ID with `/` replaced by `--`, for example `muhammadafzal--facebook-ads-library-scraper`. Some apps add a server prefix, turn hyphens into underscores, or shorten a long name and add a suffix; match on the start of the Actor slug. Each tool returns the run (`runId`, `status`, `storages.datasets.default.id`, `storages.keyValueStores.default.id`), not the rows. If a tool response already includes dataset items, use them; otherwise fetch them with `get-dataset-items`.

Run options (`memory`, `timeout`, `maxTotalChargeUsd`) go in the tool's `callOptions`, never inside `input`. Send only the input fields listed below. Every Actor handles its own network routing internally, so nothing else is needed.

## Shared rules

- `maxResults` is the spending control. Always set it to the number the user asked for (default 20 ads per platform), never leave the Actor's default.
- Always compute a cost from the prices in this file. A run response or summary record can include usage, credit or cost fields (`usageTotalUsd`, `estimatedCostUsd`, `providerCredits`); those are Apify's or a data provider's internal figures, not what the user is billed, so never report them as the cost.
- The summary record named per Actor explains the run: rows delivered, why it stopped, and warnings. Read it whenever fewer rows than requested came back.
- These are public transparency libraries. Spend and reach figures exist only where the platform publishes them (political and issue ads, ads shown in the EU); `null` means the platform did not publish a figure, not zero.

---

## Meta (Facebook and Instagram): `facebook-ads-library-scraper`

Price: USD 0.003 per ad, start USD 0.015 (512 MB, one event). `maxResults` 1 to 1,000 across all sources (0 means 1,000). About 8 seconds for a small search.

Input:
```json
{
  "searchTerms": ["running shoes"],
  "pageUrls": ["https://www.facebook.com/nike"],
  "country": "US",
  "adActiveStatus": "ACTIVE",
  "adType": "ALL",
  "mediaType": "ALL",
  "maxResults": 20
}
```
- Use `searchTerms` for a keyword, topic or brand name; use `pageUrls` (Facebook Page links or numeric Page IDs) for one advertiser's full ad set. Each entry is its own search.
- `adLibraryUrls`: a full facebook.com/ads/library link the user already filtered; it must contain `q=`, `view_all_page_id=` or `id=`.
- `country`: `ALL` or a two-letter code (US, CA, MX, BR, GB, IE, FR, DE, ES, IT, NL, AU, IN, AE, SA, ZA, JP, SG and about 35 more). `adActiveStatus`: `ALL` | `ACTIVE` | `INACTIVE`. `adType`: `ALL` | `POLITICAL_AND_ISSUE_ADS`. `mediaType`: `ALL` | `IMAGE` | `VIDEO` | `MEME` | `NONE`.

Dataset row (camelCase): `adId`, `pageName`, `pageId`, `pageUrl`, `pageLikeCount`, `pageVerified`, `pageCategory`, `pageInstagramUser`, `pageInstagramFollowers`, `adText`, `headline`, `ctaText`, `ctaType`, `ctaUrl`, `linkDescription`, `displayFormat` (`IMAGE`, `VIDEO`, `CAROUSEL`, `DCO` and others), `mediaType`, `imageUrls[]`, `videoUrls[]`, `carouselCards[]` (each with its own body, title, link and media), `platforms[]` (`FACEBOOK`, `INSTAGRAM`, `AUDIENCE_NETWORK`, `MESSENGER`), `isActive`, `startDate`, `endDate`, `daysRunning`, `categories[]`, `adSnapshotUrl`, `adLibraryUrl`, `spend`, `impressions`, `euTotalReach`, `demographicDistribution`, `winnerScore` (0 to 100), `winnerTier` (`high` | `medium` | `low`), `winnerSignals[]` (for example `long_running`, `still_active`, `high_variation`), `detectedCtas[]`, `hashtags[]`, `emojiCount`, `wordCount`, `adCategory`, `containsAiMedia`, `containsSensitiveContent`, `scrapedAt`, `sourceUrl`.

Data notes: when `displayFormat` is `DCO` the `adText` and `headline` can be catalog placeholders such as `{{product.name}}`; the real copy is in `carouselCards`, so quote from there and call it a dynamic catalog ad. `winnerScore` is the Actor's own heuristic (long-running, still active, many variations), not a Meta metric; describe it that way.

Key-value record `OUTPUT`: `status`, `totalAds`, `requestedMaxResults`, `effectiveMaxResults`, `sourcesProcessed`, `stopReason` (`max-results` and others), `warnings[]`, `scrapedAt`.

---

## TikTok: `tiktok-ads-library-scraper`

Price: USD 0.005 per ad, start USD 0.01 (256 MB). `maxResults` 1 to 100 per run. About 20 to 70 seconds with details on.

Input:
```json
{
  "query": "coffee",
  "searchMode": "keyword",
  "maxResults": 20,
  "includeDetails": true
}
```
- One `query` per run: a keyword, or an advertiser name with `searchMode: "advertiser"` (optionally with the numeric `advertiserBusinessId`). For several brands, start one run per brand.
- `includeDetails: true` adds objective, landing page, media links and target countries; the price per ad is the same.
- TikTok's public ad library covers ads shown in Europe (EU, EEA, Switzerland and the UK). Ads that ran only in the US or elsewhere are not in it; say so when a US-only brand returns nothing.

Dataset row (camelCase): `adId`, `advertiser`, `advertiserBusinessId`, `firstShown`, `lastShown`, `uniqueUsersSeen` (a range such as `100K-200K`), `caption`, `objective`, `destinationUrl`, `adCategory`, `targetCountries[]`, `videoUrls[]`, `imageUrls[]`, `adUrl`, `searchUrl`, `scrapedAt`.

Key-value record `OUTPUT`: `status` (`data` | `empty` | other), `delivered`, `stopReason` (`result_limit` and others), `warnings[]` ("Detail unavailable for ad …" is normal: that ad keeps its search-level fields).

---

## LinkedIn: `linkedin-ads-library-scraper`

Price: USD 0.005 per ad, start USD 0.01 (256 MB). `maxResults` 1 to 100. About 15 seconds.

Input:
```json
{
  "advertiser": "HubSpot",
  "keyword": "marketing automation",
  "maxResults": 20
}
```
- `advertiser` is a company-name search and can match similarly named companies; filter the rows on `advertiserName`. `keyword` searches ad text. Either or both may be set.
- `adUrls`: linkedin.com/ad-library/detail/… links; search fields are then ignored.

Dataset row (camelCase): `adId`, `adUrl`, `advertiserName`, `advertiserUrl`, `adType` (`Single Image Ad`, `Video Ad`, `Carousel Ad`, `Document Ad`, `Text Ad` and others), `text`, `headline`, `imageUrl`, `videoUrl`, `paidForBy`, `searchUrl`. LinkedIn publishes no run dates or reach in this library.

Key-value record `SUMMARY` (ignore `STATE`, which is internal): `outcome` (`data` | `empty` | `failed`), `delivered`, `stopReason` (`result-limit` and others), `warnings[]`, `chargedResultEvents`.

---

## Google (Search, YouTube, Shopping, Display): `google-ads-library-scraper`

Price: USD 0.0015 per ad or advertiser row, plus USD 0.003 per ad when `includeDetails` is on; start USD 0.005 (256 MB). `maxResults` 1 to 50,000. A few seconds for 20 ads; add about one second per ad with details.

Input:
```json
{
  "searchTerms": ["allbirds.com"],
  "resultType": "ads",
  "region": "US",
  "platforms": ["YOUTUBE"],
  "adFormat": "ALL",
  "startDate": "2026-10-01",
  "endDate": "2026-10-31",
  "maxAdsPerTarget": 20,
  "maxAdvertisersPerTerm": 3,
  "maxResults": 20,
  "includeDetails": false
}
```
- `searchTerms`: a website (`allbirds.com`, best: every ad pointing at that site) or an advertiser name (`Allbirds`: matching advertiser accounts, then their ads). `advertiserIds`: Transparency Center IDs starting with `AR`. `startUrls`: adstransparency.google.com links as `{"url": "…"}` objects.
- `resultType: "advertisers"` returns the accounts behind a website or name (legal name, country, identity verification, ads seen) and no ad rows.
- `region`: `anywhere` or a two-letter country code. `platforms`: any of `SEARCH`, `YOUTUBE`, `SHOPPING`, `MAPS`, `PLAY`; empty means all (Google filters by platform only for ads shown since 2023-09-04). `adFormat`: `ALL` | `TEXT` | `IMAGE` | `VIDEO`. Leave the dates out for any time.
- `maxAdsPerTarget` caps ads per website or account, newest first; keep it equal to `maxResults` for one target.
- `includeDetails: true` adds regions with first and last shown dates, times-shown ranges (published mainly for the EU), platforms, topic, funder, audience criteria and variations. Use it only when the user asks for reach or targeting.

Dataset row (camelCase), `recordType` `ad`: `creativeId`, `advertiserId`, `advertiserName`, `advertiserLegalName`, `advertiserCountry`, `advertiserIdentityVerified`, `domain`, `format`, `firstShownAt`, `lastShownAt`, `shownDays`, `imageUrl`, `previewUrl`, `adUrl`, `advertiserUrl`, `region`, `searchTarget`, `detailsIncluded`, then with details: `topic`, `fundedBy`, `variationCount`, `variations`, `regionCount`, `regionsShown`, `platforms`, `euTimesShownMin`, `euTimesShownMax`, `euTimesShownByPlatform`, `audienceSelection`. Google publishes most text ads as an image, so text ads usually have a `previewUrl` and no words; link to `adUrl` rather than inventing the copy.

`recordType` `advertiser`: `advertiserId`, `advertiserName`, `advertiserLegalName`, `advertiserCountry`, `advertiserIdentityVerified`, `adsSeen`, `domainsSeen[]`, `advertiserUrl`.

Key-value record `OUTPUT`: `outcome` (`SUCCESS` and others), `stopReason`, `message`, `delivered`, `resultEventsCharged`, `detailsEventsCharged`, `warnings[]`, `targets[]` (per target: `target`, `type`, `status`, `adsFound`, `delivered`, `estimatedTotalMin`, `estimatedTotalMax`). `estimatedTotalMin`/`Max` is Google's own estimate of how many ads the advertiser has; quote it as the size of the full ad set.

---

## Cost formula

cost = runs × start fee + charged rows × per-row price.

Examples on the FREE tier: 20 Meta ads ≈ USD 0.075; 20 TikTok ads ≈ USD 0.11; 20 LinkedIn ads ≈ USD 0.11; 20 Google ads ≈ USD 0.035 (USD 0.095 with details); one brand on all four libraries at 20 ads each ≈ USD 0.33.

Quote costs as "about": libraries often hold fewer ads than requested, and the user's Apify plan tier may be discounted.
