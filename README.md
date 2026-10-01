# TidyTools agent skills

Agent Skills (`SKILL.md`) for Claude Code, Codex, Cursor and other agents that load skills. Each skill calls a free web endpoint with one HTTP GET. They need **no API key and no account**.

| Skill | What the agent can do | Endpoint |
|---|---|---|
| [`read-web-page`](skills/read-web-page/SKILL.md) | Read any public web page as clean Markdown, with title, final URL and a token estimate | `https://tools.yukai.uk/md/<url>` |
| [`check-ai-crawler-access`](skills/check-ai-crawler-access/SKILL.md) | See which AI crawlers (GPTBot, ClaudeBot, PerplexityBot and 26 more) a site allows, and get a robots.txt fix | `https://tools.yukai.uk/ai-crawlers/<domain>` |

Try them without an agent:

```bash
curl -s "https://tools.yukai.uk/md/https://example.com/"
curl -s "https://tools.yukai.uk/ai-crawlers/wikipedia.org"
```

## Install

With the [skills CLI](https://github.com/vercel-labs/skills):

```bash
npx skills add tidytools/agent-skills
```

Or copy a folder from `skills/` into your agent's skills directory, for example `~/.claude/skills/` for Claude Code.

## Limits

- The endpoints are free and rate limited, at about 10 requests per minute per IP.
- They use a plain HTTP fetch, so they run no JavaScript.
- Each request handles one page.

For JavaScript pages, whole-site crawls, PDFs, RAG chunks or volume, there are paid versions on Apify and RapidAPI. The machine-readable catalog of all TidyTools tools is at [tools.yukai.uk/llms.txt](https://tools.yukai.uk/llms.txt).

Disclosure: TidyTools runs the endpoints and wrote these skills. Contact and privacy: [tools.yukai.uk/about](https://tools.yukai.uk/about).

## License

MIT
