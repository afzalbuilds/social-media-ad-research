---
name: social-research-cost-estimate
description: Estimate what a social media or ad library research job will cost on Apify before running anything. Use when the user asks "how much will it cost", "price per ad", "is it free", "how is this billed", "cheapest way to", "cost of 500 ads", "what does a weekly competitor report cost", or wants to compare the prices of the Meta, TikTok, LinkedIn or Google ad libraries, YouTube, Instagram Reels, Threads or Reddit sources, or asks about Apify credits, plans or charges for this plugin.
---

# Social research cost estimate

Prices are in `../competitor-ad-research/references/ad-library-contracts.md` and `../social-content-research/references/content-contracts.md` (FREE-tier Apify Store prices, verified 2026-11-25). Use those files as the only source; do not quote other numbers.

## How billing works (two or three sentences when asked)

The plugin is free. Each source is a pay-per-event Actor on Apify Store, billed to the user's own Apify account: one event per row written to the dataset, plus a small start event per run. Inputs that return nothing (no ads, no captions) charge no rows. Free Apify accounts include monthly usage credit; paid plans get tiered discounts of up to 20 percent per row.

## Price summary (FREE tier)

| Source | Per row | Per run start |
|---|---|---|
| Meta ad library | USD 0.003 per ad | USD 0.015 |
| TikTok ad library | USD 0.005 per ad | USD 0.01 |
| LinkedIn ad library | USD 0.005 per ad | USD 0.01 |
| Google ads transparency | USD 0.0015 per ad (+ USD 0.003 with details) | USD 0.005 |
| YouTube videos and channels | USD 0.003 per video or channel | USD 0.005 |
| YouTube transcripts | USD 0.003 per transcript | USD 0.005 |
| Instagram trending Reels | USD 0.005 per Reel | USD 0.016 |
| Threads | USD 0.01 per post, reply or profile | USD 0.02 |
| Reddit search | USD 0.005 per post | USD 0.006 |
| Reddit comments | USD 0.002 per comment | USD 0.006 |

## Estimate

1. Turn the request into runs with the routing in `competitor-ad-research`, `social-content-research` or `audience-insights`: one run per platform for ads (TikTok: one per brand or keyword), one run per Actor for content (several queries, handles or links fit in one run).
2. Cost per Actor = runs × start fee + rows × per-row price.
3. Present a small table (Source, rows, runs, per row, subtotal) and the total as "about USD X". Say that the real charge is lower when a library or search holds fewer items than requested.
4. Reference figures: one brand on all four ad libraries at 20 ads each about USD 0.33, at 100 each about USD 1.49; 20 YouTube results about USD 0.065; a default audience-insights plan (25 Reddit posts, 300 comments, a 10-video YouTube search, 3 transcripts) about USD 0.79; a weekly report on three competitors across four ad libraries at 20 ads each about USD 1.00 per week.
5. Add the rough time: seconds for Meta, LinkedIn, Google, YouTube and Reddit comments; up to a minute for TikTok with details and for Reddit search.

Do not run any Actor from this skill. If the user then says to go ahead, hand over to `competitor-ad-research`, `social-content-research` or `audience-insights`.
