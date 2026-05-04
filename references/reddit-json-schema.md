# Reddit JSON API Schema & Examples

## Response Structure

### Post Listing (search.json)

```json
{
  "kind": "Listing",
  "data": {
    "children": [
      {
        "kind": "t3",
        "data": {
          "subreddit": "technology",
          "author": "username",
          "title": "Post Title",
          "selftext": "Body text (truncated in search results)",
          "score": 1234,
          "upvote_ratio": 0.95,
          "num_comments": 56,
          "created_utc": 1746000000,
          "permalink": "/r/technology/comments/abc123/...",
          "url": "https://reddit.com/...",
          "id": "abc123",
          "stickied": false,
          "over_18": false,
          "link_flair_text": "Discussion"
        }
      }
    ],
    "after": "t3_xyz789"
  }
}
```

### Single Post + Comments (comments.json)

```json
{
  "kind": "Listing",
  "data": {
    "children": [
      {
        "kind": "t3",
        "data": {
          "title": "...",
          "selftext": "Full body text",
          "author": "...",
          "score": ...,
          "num_comments": ...,
          "created_utc": ...,
          "id": "..."
        }
      }
    ]
  }
}
```

Then comment tree as second element in the array:
```json
[
  { /* post listing */ },
  {
    "kind": "Listing",
    "data": {
      "children": [
        {
          "kind": "t1",
          "data": {
            "body": "Comment text",
            "author": "commenter",
            "score": 42,
            "created_utc": 1746001000,
            "parent_id": "t3_abc123",
            "id": "def456",
            "children": [ /* nested replies */ ]
          }
        }
      ]
    }
  }
]
```

## Quick Extraction Commands

```bash
# Extract titles from search
curl -s "https://www.reddit.com/r/tech/search.json?q=AI" | \
  python3 -c "import json,sys; [print(c['data']['title']) for c in json.load(sys.stdin)['data']['children']]"

# Extract selftext + score from single post
curl -s "https://www.reddit.com/r/tech/comments/abc123.json" | \
  python3 -c "import json,sys; d=json.load(sys.stdin)[0]['data']['children'][0]['data']; print(d['title'], d['score'], d['selftext'][:200])"

# Paginate through results
curl -s "https://www.reddit.com/r/tech/search.json?q=AI&limit=10" > page1.json
AFTER=$(python3 -c "import json; print(json.load(open('page1.json'))['data']['after'])")
curl -s "https://www.reddit.com/r/tech/search.json?q=AI&after=$AFTER&limit=10" > page2.json
```

## Common Subreddits for Tech Research

| Subreddit | Topic |
|-----------|-------|
| `r/technology` | General tech |
| `r/ClaudeAI` | Claude Code |
| `r/LocalLLaMA` | Local models |
| `r/MachineLearning` | ML research |
| `r/programming` | Software dev |
| `r/artificial` | AI discussion |

## Notes

- Timestamps are `created_utc` — Unix epoch. Convert with `date -r <timestamp>`.
- `score` is already upvotes-minus-downvotes (net score).
- `permalink` always starts with `/r/` — prepend `https://reddit.com` for full URL.
- `selftext` in search results is typically truncated to ~500 chars. Full content requires the single-post endpoint.
