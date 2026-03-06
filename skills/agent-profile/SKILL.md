---
name: agent-profile
description: Agent self-profile capability for identity inspection and wallet address update.
version: 1.0.0
owner: zapry-platform
tags: ["zapry", "agent", "profile", "wallet"]
triggers_intent: ["view agent info", "agent profile", "update wallet address", "set wallet address", "bot info", "bot profile", "my profile"]
triggers_api: ["getMe", "setMyWalletAddress"]
risk: high
scope: ["agent_profile_read", "agent_wallet_address_update"]
out_of_scope: ["wallet_transfer", "group_moderation", "feed_ops", "club_ops"]
depends_on: []
---

# Skill: agent-profile

## When to Use

- Need to query Agent identity information
- Need to update Agent's own wallet receiving address

## When NOT to Use

- For transfers, deductions, or fund operations, do not use this skill
- For group management, feed, or club operations, do not use this skill

## Workflow

1. **Identify action type**: Read-only (`getMe`) or high-risk write (`setMyWalletAddress`).
2. **Permission check**: Only owner can execute write actions.
3. **Secondary confirmation**: Write actions require confirmation (confirmation code / button / passphrase, choose one).
4. **Execute call**: Proceed after passing verification.
5. **Audit landing**: Record old value, new value, operator, time, and request source.
6. **Result receipt**: Return clear and traceable result for both success and failure.

## API Matrix

| API | Purpose | Required Fields | Notes |
|-----|---------|-----------------|-------|
| `getMe` | Get Agent info | - | Low-risk read-only |
| `setMyWalletAddress` | Update Agent wallet address | `wallet_address` | High-risk write. Requires owner + secondary confirmation |

### Minimal Request/Response Pattern

Request example (`setMyWalletAddress`):

```json
{
  "wallet_address": "0xabc123...",
  "confirm_token": "confirmation_code"
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
- `Permission denied`: Non-owner rejected immediately with reason.
- `Confirmation failed`: Timeout or verification failure. Do not execute write.
- `Parameter error`: Invalid wallet address format. Reject write.
- `429`: Rate limit. Retry after delay.

## Security Notes

- `setMyWalletAddress` requires secondary confirmation.
- Profile write actions are restricted to owner only.
- Any failure must return a clear error. Silent error swallowing is forbidden.
- Address changes must log old value, new value, operator, time, and request source.
- Do not output full sensitive credentials in logs. Keep only necessary audit information.

## Examples

See `examples.md` in this directory.
