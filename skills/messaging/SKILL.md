---
name: messaging
description: Zapry messaging capability for send, receive, callback, command, and file operations.
version: 1.0.0
owner: zapry-platform
tags: ["zapry", "messaging", "callback", "webhook", "commands"]
triggers_intent: ["send message", "reply to user", "callback button", "command menu", "receive message", "webhook", "long polling", "send file"]
triggers_api: ["sendMessage", "sendPhoto", "sendVideo", "sendDocument", "sendAudio", "sendVoice", "sendAnimation", "deleteMessage", "answerCallbackQuery", "getUpdates", "setWebhook", "getWebhookInfo", "deleteWebhook", "setMyCommands", "getMyCommands", "deleteMyCommands", "getFile"]
risk: medium
scope: ["message_send", "message_receive", "callback", "commands", "files", "message_delete"]
out_of_scope: ["group_moderation", "feed_ops", "club_ops", "wallet_transfer"]
depends_on: []
---

# Skill: messaging

## When to Use

- Need to send text, photo, video, file, audio, voice, or animation
- Need to handle button callbacks (callback query)
- Need to receive message updates (polling or webhook)
- Need to manage Bot command lists
- Need to retrieve file information sent by users

## When NOT to Use

- For group member management (ban/mute/kick), use `groups`
- For publishing feed content or club operations, use `feed` or `clubs`
- For agent self-profile high-risk actions, use `agent-profile`

## Workflow

1. Determine the message flow based on user intent: `polling` (`getUpdates`) or `webhook` (`setWebhook`)
2. Choose the send method (text/media/file) and construct the request body
3. If interactive buttons are included, listen for and handle `answerCallbackQuery` after sending
4. If command entry points are needed, call `setMyCommands` and verify with `getMyCommands`
5. On failure, execute retry or degradation strategy based on error code

## API Matrix

| API | Purpose | Required Fields | Notes |
|-----|---------|-----------------|-------|
| `sendMessage` | Send text message | `chat_id`, `text` | Supports inline/reply keyboard |
| `sendPhoto` | Send photo | `chat_id`, `photo` | URL or file ID |
| `sendVideo` | Send video | `chat_id`, `video` | URL or file ID |
| `sendDocument` | Send file | `chat_id`, `document` | URL or file ID |
| `sendAudio` | Send audio | `chat_id`, `audio` | Check format limits |
| `sendVoice` | Send voice | `chat_id`, `voice` | Check format limits |
| `sendAnimation` | Send GIF/animation | `chat_id`, `animation` | Check size limits |
| `deleteMessage` | Delete message | `chat_id`, `message_id` | Only within permission scope |
| `answerCallbackQuery` | Respond to button callback | `callback_query_id` | Prevents interaction from hanging |
| `getUpdates` | Long-poll for updates | - | For polling mode |
| `setWebhook` | Set webhook URL | `url` | For server-push mode |
| `getWebhookInfo` | Get webhook status | - | Read-only |
| `deleteWebhook` | Remove webhook | - | Reverts to polling |
| `setMyCommands` | Set command menu | `commands` | Array of command objects |
| `getMyCommands` | Get command menu | - | Read-only |
| `deleteMyCommands` | Delete command menu | - | Clears all commands |
| `getFile` | Get file info | `file_id` | Returns download URL |

### Minimal Request/Response Pattern

Request example (`sendMessage`):

```json
{
  "chat_id": "group_or_user_id",
  "text": "Hello from Zapry Bot"
}
```

Response example:

```json
{
  "ok": true,
  "result": {
    "message_id": "12345"
  }
}
```

## Error Handling

- `401`: Bot token invalid. Stop retrying and return auth failure.
- `429`: Rate limit hit. Exponential backoff retry.
- `5xx`: Transient failure. Retry with limited attempts and degrade gracefully.
- `Business error`: Return explainable error directly. Never swallow errors.

## Security Notes

- Minimize sensitive information in message content.
- Delete message operations must respect permission boundaries; cross-permission deletion is forbidden.
- Webhook URL changes must be audit-logged (change time, operator).
- File handling should only occur when necessary; avoid excessive persistence.

## Examples

See `examples.md` in this directory.
