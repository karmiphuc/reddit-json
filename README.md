# reddit-json

Access Reddit content via Reddit's public JSON API. A practical skill for AI agents that need posts, comments, or search results.

## When to use this

Reddit's HTML routes through Cloudflare and other anti-bot layers, which makes browser automation heavy for simple lookups. Appending `.json` to Reddit URLs returns structured data directly — useful for search, single posts, and comment trees.

## Limits

- Private subreddits and gilded content are restricted.
- Search selftext is truncated to ~500 characters; the full body requires the single-post endpoint.
- Be a reasonable citizen on rate limits, and respect Reddit's terms of service and robots guidance.

## Quick Start

Append `.json` to a Reddit URL:

```bash
# Search posts (includes truncated selftext excerpts)
curl "https://www.reddit.com/r/technology/search.json?q=AI&sort=relevance&limit=5"

# Single post + full comment tree
curl "https://www.reddit.com/r/technology/comments/abc123.json?context=3"
```

## For AI Agents

Load `SKILL.md` into your agent. It includes:
- Endpoint patterns (search, single post, pagination)
- What fields are available in each response
- How to find post IDs from saved links
- Limitations and known edge cases

## Endpoints

| Pattern | URL | Returns |
|---------|-----|---------|
| Search | `/r/<sub>/search.json?q=<query>` | Post metadata + truncated selftext |
| Single post | `/r/<sub>/comments/<id>.json` | Full post body + comment tree |
| Paginated | Add `&after=t3_<id>` | Next page of results |

## Limitations

- Private subreddits: blocked
- Gilded content: restricted
- Search selftext: truncated (~500 chars), not full body
- Rate limits: be reasonable

## Install

Download `reddit-json.skill` and place it in your agent's skills directory.

Or copy `SKILL.md` and `references/reddit-json-schema.md` directly.
