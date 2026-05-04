# reddit-json

Access Reddit content via Reddit's internal JSON API — without Cloudflare getting in the way.

## The Problem

Reddit serves a Cloudflare verification wall to most automated requests. Browser automation works, but for data extraction at scale, it's overkill.

## The Solution

Reddit exposes a public JSON API that doesn't route through Cloudflare anti-bot. Same data, no verification, no drama.

## Quick Start

Append `.json` to any Reddit URL:

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
