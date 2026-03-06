# agent-profile examples

## Example 1: Query agent profile

```json
POST /{AUTH_TOKEN}/getMe
{}
```

Response:

```json
{
  "ok": true,
  "result": {
    "bot_id": "bot_001",
    "display_name": "Zapry Helper",
    "wallet_address": "0xabc..."
  }
}
```

## Example 2: Update wallet address (owner only)

1. Submit with confirmation:

```json
POST /{AUTH_TOKEN}/setMyWalletAddress
{
  "wallet_address": "0xnew123...",
  "confirm_token": "confirmation_code"
}
```

Response:

```json
{
  "ok": true,
  "result": true
}
```

## Example 3: Reject unauthorized update

Non-owner calls `setMyWalletAddress`:

Response:

```json
{
  "ok": false,
  "error": "permission_denied",
  "message": "Only owner can update wallet address"
}
```
