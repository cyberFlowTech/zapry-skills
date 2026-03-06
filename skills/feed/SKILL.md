---
name: feed
description: Zapry feed posting and engagement capability.
version: 1.0.0
owner: zapry-platform
tags: ["zapry", "feed", "post", "engagement"]
triggers_intent: ["publish post", "comment post", "like post", "share post", "content engagement", "content operations", "engage feed"]
triggers_api: ["createPost", "commentPost", "likePost", "sharePost"]
risk: medium
scope: ["feed_create", "feed_comment", "feed_like", "feed_share"]
out_of_scope: ["group_moderation", "club_ops", "wallet_transfer"]
depends_on: ["messaging"]
---

# Skill: feed

## When to Use

- Need to publish a post or engage with content
- Need to comment, like, or share a post
- Need to automate content operations

## When NOT to Use

- For group management, use `groups`
- For club operations, use `clubs`

## Workflow

1. **Choose action**: Publish, comment, like, or share.
2. **Content preflight**: Validate text length, media legality, and compliance requirements.
3. **Execute call**: Select `createPost` / `commentPost` / `likePost` / `sharePost` based on action.
4. **Confirm result**: Record `post_id` or engagement result to ensure action landed.
5. **Linked notification**: Use `messaging` to relay result or alert when needed.
6. **Failure degradation**: Retry on rate limit / transient failure; return clear reason on business rejection.

## API Matrix

| API | Purpose | Required Fields | Notes |
|-----|---------|-----------------|-------|
| `createPost` | Publish a post | `content` | Recommend content review and deduplication first |
| `commentPost` | Comment on a post | `post_id`, `content` | Watch comment frequency and sensitive words |
| `likePost` | Like a post | `post_id` | Recommend idempotent deduplication |
| `sharePost` | Share a post | `post_id` | Recommend recording source chain |

### Minimal Request/Response Pattern

Request example (`createPost`):

```json
{
  "content": "Today we shipped a new feature.",
  "visibility": "public"
}
```

Response example:

```json
{
  "ok": true,
  "result": {
    "post_id": "post_123"
  }
}
```

## Error Handling

- `401`: Auth failure. Stop retrying.
- `429`: Rate limit hit. Exponential backoff retry.
- `Content unavailable`: Target post does not exist or is not visible. Return business error.
- `Duplicate action`: For idempotent actions like like, return idempotent success.

## Security Notes

- Avoid high-frequency interactions in short windows to prevent triggering risk controls.
- Posts and comments must comply with content safety rules.
- Bulk operations must have rate caps and stop conditions.
- Automated operations should avoid "farming" patterns (dense likes, repetitive comments).

## Examples

See `examples.md` in this directory.
