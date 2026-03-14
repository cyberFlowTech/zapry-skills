---
name: discovery
description: Zapry public content discovery capability with read-only endpoints.
version: 1.0.0
owner: zapry-platform
tags: ["zapry", "discovery", "search", "trending", "public-data"]
triggers_intent: ["trending posts", "search content", "discover content", "trends", "public wallet address", "search posts"]
triggers_api: ["getTrendingPosts", "searchPosts", "getWalletAddress"]
risk: low
scope: ["public_trending", "public_search", "public_wallet_lookup"]
out_of_scope: ["message_send", "group_moderation", "feed_ops", "wallet_transfer"]
depends_on: []
---

# Skill: discovery

## When to Use

- Need to query publicly trending content
- Need to search posts
- Need to look up public wallet address information

## When NOT to Use

- For write actions (posting, commenting, managing), do not use this skill
- For accessing private data, do not use this skill

## Workflow

1. **Identify query type**: Trending, keyword search, or address lookup.
2. **Build query parameters**: Keywords, pagination, sort order, or target user identifier.
3. **Execute read-only call**: `getTrendingPosts` / `searchPosts` / `getWalletAddress`.
4. **Normalize results**: Unify field structure (title, link, source, timestamp).
5. **Quality control**: Separate empty results from weak matches to avoid misleading output.

## API Matrix

| API | Purpose | Required Fields | Notes |
|-----|---------|-----------------|-------|
| `getTrendingPosts` | Get trending posts | - | Good for trending dashboards |
| `searchPosts` | Search posts | `query` | Supports keyword search |
| `getWalletAddress` | Look up public wallet address | `user_id` | Read public info only |

### Minimal Request/Response Pattern

Request example (`searchPosts`):

```json
{
  "query": "AI agent",
  "page": 1,
  "limit": 20
}
```

Response example:

```json
{
  "ok": true,
  "result": {
    "items": []
  }
}
```

## Error Handling

- `401`: Token invalid. Terminate request.
- `429`: Rate limit. Retry after delay.
- `Empty result`: Return empty array with "no matching data" note.
- `Parameter error`: Invalid query or user identifier. Return explainable error.

## Security Notes

- This skill only reads public data. It does not perform any write actions.
- Do not infer or extend non-existent private information.
- For wallet addresses, only display publicly visible fields. No privacy correlation inference.
- Output content should cite sources. Avoid presenting speculation as fact.

## Examples

See `examples.md` in this directory.
