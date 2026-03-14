# feed examples

## Example 1: Publish a post

```json
POST /{AUTH_TOKEN}/createPost
{
  "content": "Today we shipped a new feature.",
  "images": ["/tmp/release-cover.png"]
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

### If user asks for image post

- Do **not** send text-only `createPost`.
- `images` must be present and non-empty.
- Each image source must be local path, `data:` URI, or `/_temp/media` URL.

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
