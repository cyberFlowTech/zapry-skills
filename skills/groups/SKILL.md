---
name: groups
description: Zapry chat and group management capability.
version: 1.0.0
owner: zapry-platform
tags: ["zapry", "groups", "moderation", "admin"]
triggers_intent: ["group management", "group moderation", "mute member", "kick member", "list group members", "query group member", "query admin list", "set group title", "set group description"]
triggers_api: ["getChatMember", "getChatMembers", "getChatMemberCount", "getChatAdministrators", "muteChatMember", "kickChatMember", "setChatTitle", "setChatDescription"]
risk: high
scope: ["group_query", "group_moderation", "group_profile_update"]
out_of_scope: ["message_send", "feed_ops", "club_ops", "wallet_transfer"]
depends_on: ["messaging"]
---

# Skill: groups

## When to Use

- Need to query group members, member count, or admin list
- Need to mute or kick members
- Need to update group title or description

## When NOT to Use

- For sending messages only, use `messaging`
- For publishing feed content or club operations, use `feed` or `clubs`

## Workflow

1. **Identify action type**: Query (read-only), moderation (high-risk write), or group profile update.
2. **Permission preflight**: Call `getChatAdministrators` to confirm Bot is admin; if a specific member is targeted, use `getChatMember` for operability checks.
3. **Execute action**:
   - Query: `getChatMembers` / `getChatMemberCount`
   - Moderation: `muteChatMember` / `kickChatMember`
   - Profile update: `setChatTitle` / `setChatDescription`
4. **Verify result**: Confirm state change matches expectation (e.g., member list, mute state, member removal).
5. **Notify and audit**: Send result via `messaging` and write audit log.
6. **Rollback strategy**: For reversible actions, prefer explicit recovery steps (e.g., `mute:false` after accidental mute).

## API Matrix

| API | Purpose | Required Fields | Notes |
|-----|---------|-----------------|-------|
| `getChatMember` | Query a specific member | `chat_id`, `user_id` | Targeted preflight before moderation |
| `getChatMembers` | List group members | `chat_id` | Supports paging and keyword filter |
| `getChatMemberCount` | Get group member count | `chat_id` | Can be used for threshold control |
| `getChatAdministrators` | Get admin list | `chat_id` | Verify Bot admin permission |
| `muteChatMember` | Mute/unmute a member | `chat_id`, `user_id`, `mute` | `true` = mute, `false` = unmute |
| `kickChatMember` | Remove a member | `chat_id`, `user_id` | High-risk action |
| `setChatTitle` | Set group title | `chat_id`, `title` | Audit before and after |
| `setChatDescription` | Set group description | `chat_id`, `description` | Audit before and after |

### Minimal Request/Response Pattern

Request example (`muteChatMember`):

```json
{
  "chat_id": "group_id",
  "user_id": "target_user_id",
  "mute": true
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
- `Target state error`: E.g., already muted or already removed. Return idempotent success or explainable failure.
- `Parameter error`: Invalid `chat_id`/`user_id`, reject immediately.

## Security Notes

- All moderation actions must be audit-logged (target, action, time, reason, operator).
- High-risk actions (`kickChatMember`, bulk mute) should require a business-side reason.
- Enable rate limits and circuit breakers for bulk operations to prevent collateral damage.
- Forbid moderation actions on protected roles (e.g., group owner, peer admins).
- Moderation results should be made visible via `messaging` to reduce "silent governance" risk.

## Examples

See `examples.md` in this directory.
