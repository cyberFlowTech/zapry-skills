# groups examples

## Example 1: Mute spam user

1. Search the member in the group list:

```json
POST /{AUTH_TOKEN}/getChatMembers
{
  "chat_id": "group_id",
  "page": 1,
  "page_size": 20,
  "keyword": "spam_user"
}
```

2. Mute the user:

```json
POST /{AUTH_TOKEN}/muteChatMember
{
  "chat_id": "group_id",
  "user_id": "spam_user_id",
  "mute": true
}
```

Response:

```json
{
  "ok": true,
  "result": true
}
```

3. Notify group via `messaging` skill.

## Example 2: Kick repeated offender

1. Verify bot is admin:

```json
POST /{AUTH_TOKEN}/getChatAdministrators
{
  "chat_id": "group_id"
}
```

2. Remove the member:

```json
POST /{AUTH_TOKEN}/kickChatMember
{
  "chat_id": "group_id",
  "user_id": "offender_id"
}
```

## Example 3: Update group title

```json
POST /{AUTH_TOKEN}/setChatTitle
{
  "chat_id": "group_id",
  "title": "New Group Title"
}
```
