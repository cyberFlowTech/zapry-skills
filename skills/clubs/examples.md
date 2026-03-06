# clubs examples

## Example 1: Create and initialize a club

1. Create:

```json
POST /{AUTH_TOKEN}/createClub
{
  "name": "Zapry Builders",
  "description": "Official builders club"
}
```

Response:

```json
{
  "ok": true,
  "result": {
    "club_id": "club_123"
  }
}
```

2. Update profile:

```json
POST /{AUTH_TOKEN}/updateClub
{
  "club_id": "club_123",
  "description": "Updated description for Zapry Builders"
}
```

## Example 2: Post announcement to club

```json
POST /{AUTH_TOKEN}/postToClub
{
  "club_id": "club_123",
  "content": "New feature released today!"
}
```
