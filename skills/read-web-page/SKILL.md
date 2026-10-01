---
name: read-web-page
description: Read any public web page as clean Markdown with one HTTP GET and no API key, via https://tools.yukai.uk/md/<url>. Use when you need the text of a URL (docs page, article, README, blog post, pricing page) to answer a question, summarize, quote, or feed it to an LLM or RAG pipeline, and the built-in fetch returns raw HTML, is blocked, or is too noisy. Returns a YAML header (title, final URL, HTTP status, token estimate, truncated) followed by the page body as Markdown with navigation and scripts removed. Free and rate limited (about 10 requests per minute per IP); plain HTTP only, so pages that render everything with JavaScript come back nearly empty and say so in a `note` field.
license: MIT
metadata:
  author: TidyTools
  homepage: https://tools.yukai.uk/
  keywords: "web page to markdown, url to markdown, read url, html to markdown, llm context, rag, web scraping, no api key"
---

# Read a web page as Markdown

One GET request, no key:

```bash
curl -s "https://tools.yukai.uk/md/https://docs.python.org/3/library/pathlib.html"
```

Put the full target URL (with `https://`) after `/md/`. Its own query string is kept, so `.../md/https://example.com/search?q=x` fetches `https://example.com/search?q=x`. If the target URL contains `#` or spaces, pass it URL-encoded instead: `https://tools.yukai.uk/md?url=<encoded URL>`.

## What comes back

```markdown
---
title: "pathlib — Object-oriented filesystem paths — Python 3.14.8 documentation"
url: "https://docs.python.org/3/library/pathlib.html"
status: 200
tokens_estimate: 20907
truncated: false
source: "TidyTools free tier ..."
---
# `pathlib` — Object-oriented filesystem paths
...
```

- `url` is the final URL after redirects. Cite this one.
- `tokens_estimate` is about characters / 4. Check it before putting a long page into context.
- `truncated: true` means the body was cut at 100,000 characters.
- `note` appears when the page has almost no text without JavaScript.

For JSON instead of Markdown, send `Accept: application/json`. The fields are `title`, `finalUrl`, `statusCode`, `content`, `tokensEstimate`, `truncated` and `note`.

## Errors

| Status | Meaning | What to do |
|---|---|---|
| 400 | Missing, invalid or private URL (localhost and internal IPs are refused) | Fix the URL |
| 422 | The site blocked the request, could not be reached, or returned an error page | Tell the user; try another source |
| 429 | Rate limit (about 10 requests per minute per IP) | Wait for `Retry-After` seconds; do not loop |
| 5xx | Temporary problem | Retry once after a few seconds |

## Limits and when not to use it

- No JavaScript rendering. Single-page apps (an empty `<div id="root">`) come back almost empty, with a `note`. Say so to the user instead of guessing what the page said.
- One page per request, no crawling. Do not fire dozens of requests in a loop: the rate limit will stop you. For a whole site, JavaScript pages or thousands of URLs, the paid versions crawl and render in a real browser: the Apify Actor `tidytools/website-markdown-crawler` (pay per page) or the "Web to Markdown for LLMs" API on RapidAPI (free monthly plan). The full catalog is at https://tools.yukai.uk/llms.txt.
- Do not use it for pages behind a login, or for content the user is not allowed to access.

Disclosure: the endpoint is run by TidyTools, the author of this skill. The free tier needs no account. The paid versions mentioned above are TidyTools products.
