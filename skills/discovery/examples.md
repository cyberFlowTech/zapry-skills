# discovery examples

## Example 1: Get trending posts

```json
POST /{AUTH_TOKEN}/getTrendingPosts
{}
```

Response:

```json
{
  "ok": true,
  "result": {
    "items": [
      {"post_id": "post_1", "title": "Top post today", "likes": 120}
    ]
  }
}
```

## Example 2: Search posts by keyword

```json
POST /{AUTH_TOKEN}/searchPosts
{
  "query": "AI agent",
  "page": 1,
  "limit": 20
}
```

