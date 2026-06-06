---
name: clubs
description: Zapry club creation and management capability.
version: 1.0.0
owner: zapry-platform
tags: ["zapry", "clubs", "community", "server"]
triggers_intent: ["club", "community", "server", "create club", "post to club", "update club info", "leave club", "quit club", "club announcement", "community management"]
triggers_api: ["createClub", "postToClub", "updateClub", "leaveClub", "quitClub"]
risk: medium
scope: ["club_create", "club_content", "club_profile_update", "club_membership"]
out_of_scope: ["group_moderation", "feed_ops", "wallet_transfer"]
depends_on: ["messaging"]
---

# Skill: clubs

## When to Use

- Need to create a club
- Need to post content within a club
- Need to update club profile information
- Need the current bot to leave a club

## When NOT to Use

- For group member mute/kick/ban actions, use `groups`
- For global feed operations, use `feed`

## Workflow

1. **Identify club action**: Create, post, update profile, or leave.
2. **Parameter preflight**: Validate club name, description, content length and format.
3. **Execute call**:
   - Create: `createClub`
   - Publish content: `postToClub`
   - Update profile: `updateClub`
   - Leave as current bot: `leaveClub` or `quitClub`
4. **Confirm result**: Record club ID or post ID.
5. **Message linkage**: Broadcast changes or announcements via `messaging` when needed.
6. **Failure handling**: Backoff on rate limit; return explainable info on parameter errors.

## API Matrix

| API | Purpose | Required Fields | Notes |
|-----|---------|-----------------|-------|
| `createClub` | Create a club | `name` | Validate naming rules and uniqueness |
| `postToClub` | Post content in club | `club_id`, `content` | Recommend attaching category and tags |
| `updateClub` | Update club info | `club_id`, `patch` | Audit before and after changes |
| `leaveClub` / `quitClub` | Leave a club as the current bot | `club_id` | Does not remove other members |

### Minimal Request/Response Pattern

Request example (`createClub`):

```json
{
  "name": "Zapry Builders",
  "description": "Official builders club"
}
```

Response example:

```json
{
  "ok": true,
  "result": {
    "club_id": "club_123"
  }
}
```

## Error Handling

- `401`: Auth failure. Terminate flow.
- `429`: Rate limit hit. Delay retry.
- `Business error`: Invalid parameters or insufficient permissions. Return clear reason.
- `Target not found`: Invalid club ID. Return explainable failure.
- `Not a member`: The bot is not currently in the club. Tell the caller no leave action was applied.

## Security Notes

- Club profile updates must log operator and change diff.
- Club posts must comply with content safety requirements.
- `leaveClub` only exits the current bot from the target club. Do not use member kick/moderation APIs as a substitute.
- High-frequency bulk updates require throttling and human review support.
- Do not use club management APIs for group chat management (separation of concerns).

## Examples

See `examples.md` in this directory.
