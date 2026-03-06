# feed examples

## Example 1: Publish a post

```json
POST /{AUTH_TOKEN}/createPost
{
  "content": "Today we shipped a new feature."
}
```

Response:

```json
{
  "ok": true,
  "result": {
    "post_id": "post_123"
  }
}
```

## Example 2: Engage with a post

1. Comment:

```json
POST /{AUTH_TOKEN}/commentPost
{
  "post_id": "post_123",
  "content": "Great update!"
}
```

2. Like:

```json
POST /{AUTH_TOKEN}/likePost
{
  "post_id": "post_123"
}
```

3. Share:

```json
POST /{AUTH_TOKEN}/sharePost
{
  "post_id": "post_123"
}
```
