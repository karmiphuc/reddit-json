---
name: reddit-json
description: Access Reddit content via Reddit's internal JSON API. Use when scraping Reddit content (posts, comments, search results) and encountering Cloudflare verification walls. Triggers on phrases like "scrape Reddit", "fetch Reddit", "Reddit is blocked", "Reddit content", "Cloudflare Reddit", "Reddit not accessible", or any research task targeting Reddit content.
---

# Reddit JSON API

Reddit blocks scraping attempts with a Cloudflare verification wall. The JSON API provides a direct path — it's public API surface that doesn't route through the same anti-bot layer.

## Core Pattern

Append `.json` to any Reddit URL:

```
reddit.com/r/<sub>/comments/<id>  →  reddit.com/r/<sub>/comments/<id>.json
```

## Endpoints

### Single Post + Comment Tree
```
https://www.reddit.com/r/<subreddit>/comments/<post_id>.json
```
Returns post metadata + full comment tree.

### Search (with selftext excerpts)
```
https://www.reddit.com/r/<subreddit>/search.json?q=<query>&sort=relevance&limit=5
```
Returns post metadata + truncated selftext. Good enough for relevance filtering.

### Pagination
Use `after` from previous response:
```
https://www.reddit.com/r/<sub>/search.json?q=<query>&after=t3_<id>
```

### Context depth (comments)
```
https://www.reddit.com/r/<sub>/comments/<id>.json?context=0
```
`context=N` controls nesting depth.

## What You Get

- **Post metadata:** title, author, score, timestamp, flair, upvote ratio, num comments, permalink
- **Selftext:** Full body for single-post JSON; truncated (~500 chars) for search results
- **Comments:** Full nested tree with `body`, `author`, `score`, `created_utc`

## Finding Post IDs

Raindrop / bookmarks store Reddit links as `https://www.reddit.com/r/sub/comments/POST_ID/`. Extract the slug — that's your `<post_id>`.

For search-derived links, use the `permalink` field from the JSON response.

## Limitations

- **Private subreddits:** Blocked
- **Gilded content:** Restricted
- **Search selftext:** Truncated (≈500 chars), not full — sufficient for relevance scoring
- **Rate limits:** Be reasonable; no explicit throttling but don't hammer

## Tools

- **`pullmd__read_url`** with `url=https://www.reddit.com/r/.../.json` — best for markdown conversion
- **`web_fetch`** with `.json` URL — raw JSON, works for programmatic extraction
- **`exec` + curl** — for piping to jq/python for analysis

## Examples

```bash
# Search for NVIDIA earnings discussion
curl -s "https://www.reddit.com/r/technology/search.json?q=NVIDIA+earnings&sort=relevance&limit=5"

# Full post + comments
curl -s "https://www.reddit.com/r/ClaudeAI/comments/1j9abcw.json?context=3"
```

For detailed field schemas and more examples, see [references/reddit-json-schema.md](references/reddit-json-schema.md).
