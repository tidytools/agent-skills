---
name: check-ai-crawler-access
description: Check which AI crawlers a website allows or blocks (GPTBot, OAI-SearchBot, ChatGPT-User, ClaudeBot, Claude-SearchBot, Claude-User, PerplexityBot, Google-Extended and 21 more) with one HTTP GET and no API key, via https://tools.yukai.uk/ai-crawlers/<domain>. Use when the user asks "does this site block ChatGPT / Claude / AI crawlers?", "can AI search engines see my site?", "why doesn't my site show up in ChatGPT or Perplexity answers?", "is it OK to scrape this site for AI?", or wants a robots.txt for AI bots. Returns a plain-language verdict, a policy label, AI-search and AI-access scores, allowed / blocked / partial bots, Cloudflare Content Signals, llms.txt presence, noai meta tags and a recommended robots.txt snippet. Free and rate limited (about 10 requests per minute per IP).
license: MIT
metadata:
  author: TidyTools
  homepage: https://tools.yukai.uk/
  keywords: "robots.txt, ai crawlers, gptbot, claudebot, perplexitybot, geo, generative engine optimization, ai seo, llms.txt, no api key"
---

# Check AI crawler access for a site

```bash
curl -s "https://tools.yukai.uk/ai-crawlers/nytimes.com"
```

A domain or full URL works. A URL with a path also tests that path, for example `.../ai-crawlers/https://example.com/blog/`. Query form: `https://tools.yukai.uk/ai-crawlers?url=example.com`.

## Read these fields first

- `verdict`: one sentence you can relay to the user.
- `policy`: one of these values:
  - `open`: everything allowed;
  - `search-only`: training crawlers blocked, AI search allowed;
  - `blocks-search`: some AI search or assistant crawlers blocked;
  - `restrictive`: all AI search and assistant crawlers blocked;
  - `no-robots`: the site has no robots.txt;
  - `unknown`: robots.txt could not be read.
- `aiSearchScore` and `aiAccessScore`: 0 to 100. Search means AI answer engines; access covers all AI crawlers.
- `blockedBots`, `partialBots`, `allowedBots`: crawler names.
- `robotsTxt.state`: `found`, `missing`, `unavailable` or `server-error`. With `unavailable` (for example robots.txt answered 403), access cannot be verified. Say that rather than claiming the site is open.
- `llmsTxt` and `llmsFullTxt`: whether `/llms.txt` and `/llms-full.txt` exist.
- `noAiDirective`: whether a `noai` / `noimageai` meta tag or X-Robots-Tag is set.
- `recommendedRobotsSnippet`: a robots.txt block that keeps AI search and assistant crawlers allowed. Offer it when the user wants to be visible in AI answers.

## Explain the two kinds of crawler

- **Training crawlers**, such as GPTBot, ClaudeBot and Google-Extended, collect content for model training.
- **Search and assistant crawlers**, such as OAI-SearchBot, ChatGPT-User, Claude-SearchBot, Claude-User and PerplexityBot, fetch pages to answer a user's question and cite them.

Blocking the first keeps content out of training. Blocking the second keeps the site out of AI answers, which owners often do not intend. Real example from 2026-10-01: nytimes.com blocks both kinds (AI search score 41); wikipedia.org allows all (100).

## Errors

- 400: invalid or private address.
- 422: the site could not be reached.
- 429: rate limited. Wait for `Retry-After` seconds; do not loop.

## Limits

This checks what the site declares (robots.txt, meta tags). It does not test firewalls that block bots by IP or behaviour. For many domains at once, change tracking over time, or a firewall test, use the paid Apify Actor `tidytools/ai-crawler-access-checker` (pay per site) or its RapidAPI API. Catalog: https://tools.yukai.uk/llms.txt.

Disclosure: the endpoint is run by TidyTools, the author of this skill. The free tier needs no account.
