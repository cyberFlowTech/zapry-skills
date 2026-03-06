---
name: groups
description: Zapry group querying and moderation capability.
version: 1.0.0
owner: zapry-platform
tags: ["zapry", "groups", "moderation", "admin"]
triggers_intent: ["group moderation", "mute user", "kick member", "ban user", "unban", "query group member", "query admin list", "set group title", "set group description"]
triggers_api: ["getChatMember", "getChatMemberCount", "getChatAdministrators", "banChatMember", "unbanChatMember", "restrictChatMember", "kickChatMember", "setChatTitle", "setChatDescription"]
risk: high
scope: ["group_query", "group_moderation", "group_profile_update"]
out_of_scope: ["message_send", "feed_ops", "club_ops", "wallet_transfer"]
depends_on: ["messaging"]
---

# Skill: groups

## When to Use

- Need to query group members, member count, or admin list
- Need to ban, unban, mute, or kick members
- Need to update group title or description

## When NOT to Use

- For sending messages only, use `messaging`
- For publishing feed content or club operations, use `feed` or `clubs`

## Workflow

1. **Identify action type**: Query (read-only) or moderation (high-risk write).
2. **Permission preflight**: Call `getChatAdministrators` and `getChatMember` to confirm Bot is admin and target is operable.
3. **Execute action**:
   - Query: `getChatMember` / `getChatMemberCount`
   - Moderation: `banChatMember` / `unbanChatMember` / `restrictChatMember` / `kickChatMember`
   - Profile update: `setChatTitle` / `setChatDescription`
4. **Verify result**: Confirm state change matches expectation (e.g., mute duration, member status).
5. **Notify and audit**: Send result via `messaging` and write audit log.
6. **Rollback strategy**: For reversible actions, prefer rollback on error (e.g., `unbanChatMember` after accidental ban).

## API Matrix

| API | Purpose | Required Fields | Notes |
|-----|---------|-----------------|-------|
| `getChatMember` | Query member identity and permissions | `chat_id`, `user_id` | Preflight check before moderation |
| `getChatMemberCount` | Get group member count | `chat_id` | Can be used for threshold control |
| `getChatAdministrators` | Get admin list | `chat_id` | Verify Bot admin permission |
| `banChatMember` | Ban a member | `chat_id`, `user_id` | High-risk action, reason required |
| `unbanChatMember` | Unban a member | `chat_id`, `user_id` | Used for accidental ban recovery |
| `restrictChatMember` | Mute/unmute | `chat_id`, `user_id`, `permissions` | Specify duration and scope |
| `kickChatMember` | Remove a member | `chat_id`, `user_id` | High-risk action |
| `setChatTitle` | Set group title | `chat_id`, `title` | Audit before and after |
| `setChatDescription` | Set group description | `chat_id`, `description` | Audit before and after |

### Minimal Request/Response Pattern

Request example (`restrictChatMember`):

```json
{
  "chat_id": "group_id",
  "user_id": "target_user_id",
  "permissions": {
    "can_send_messages": false
  }
}
```

Response example:

```json
{
  "ok": true,
  "result": true
}
```

## Error Handling

- `401`: Token invalid. Terminate operation.
- `429`: Rate limit hit. Exponential backoff retry.
- `Permission error`: Return "Bot is not admin" or "Target cannot be operated on". Do not write.
- `Target state error`: E.g., already banned or already left. Return idempotent success or explainable failure.
- `Parameter error`: Invalid `chat_id`/`user_id`, reject immediately.

## Security Notes

- All moderation actions must be audit-logged (target, action, time, reason, operator).
- High-risk actions (ban, kick) should require a business-side reason.
- Enable rate limits and circuit breakers for bulk operations to prevent collateral damage.
- Forbid moderation actions on protected roles (e.g., group owner, peer admins).
- Moderation results should be made visible via `messaging` to reduce "silent governance" risk.

## Examples

See `examples.md` in this directory.
