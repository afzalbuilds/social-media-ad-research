# Social Media & Ad Library Research

This extension researches competitors' ads in the Meta, TikTok, LinkedIn and Google ad libraries and public content on YouTube, Instagram Reels, Threads and Reddit, with Imzlab Technologies' Actors on Apify, through the `social-research` MCP server. The full instructions live in the extension's skills. Activate the matching skill before you call any of the server's tools:

- `competitor-ad-research`: a brand's or niche's ads in the Meta, TikTok, LinkedIn and Google ad libraries.
- `social-content-research`: YouTube, Instagram Reels, Threads and Reddit content and benchmarks.
- `audience-insights`: pain points, objections and customer language from comments and transcripts.
- `social-research-cost-estimate`: the Apify cost of a job before running it.

Every tool call on `social-research` starts a paid Actor run on the user's own Apify account. Follow the skill's cost rules: estimate from the price table in its references, ask before large batches, and never quote a cost from a run's usage figures. Wait for each run to finish, account for every requested item, and never invent a result.

If the research tools are missing, the server is not signed in yet: ask the user to run `/mcp auth social-research` and approve Apify's sign-in page in the browser.

Commands: `/audience-insights`, `/competitor-ads`, `/social-trends`. Subagent for unattended runs that leave a report on disk: `competitor-intel-reporter`.
