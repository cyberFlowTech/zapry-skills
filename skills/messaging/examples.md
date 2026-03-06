# messaging examples

## Example 1: Welcome message with commands

1. Register commands:

```json
POST /{AUTH_TOKEN}/setMyCommands
{
  "commands": [
    {"command": "start", "description": "Start the bot"},
    {"command": "help", "description": "Show help"}
  ]
}
```

2. When user triggers `/start`, send welcome:

```json
POST /{AUTH_TOKEN}/sendMessage
{
  "chat_id": "user_id",
  "text": "Welcome! Use /help to see available commands.",
  "reply_markup": {
    "inline_keyboard": [[
      {"text": "Get Started", "callback_data": "onboard"}
    ]]
  }
}
```

3. Handle callback:

```json
POST /{AUTH_TOKEN}/answerCallbackQuery
{
  "callback_query_id": "query_id_from_update"
}
```

## Example 2: Receive and reply in polling mode

1. Poll for updates:

```json
POST /{AUTH_TOKEN}/getUpdates
{
  "offset": 0,
  "timeout": 30
}
```

Response:

```json
{
  "ok": true,
  "result": [
    {
      "update_id": 100,
      "message": {
        "message_id": "msg_1",
        "chat": {"id": "chat_123"},
        "text": "Hello"
      }
    }
  ]
}
```

2. Reply to user:

```json
POST /{AUTH_TOKEN}/sendMessage
{
  "chat_id": "chat_123",
  "text": "Hi there! How can I help?"
}
```
