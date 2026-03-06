---
name: your-skill-name
description: Short description about what this skill does and when to use it.
version: 1.0.0
owner: zapry-platform
tags: ["zapry", "domain"]
triggers_intent: ["intent word A", "intent word B"]
triggers_api: ["apiMethodA", "apiMethodB"]
risk: low
scope: ["capability_a", "capability_b"]
out_of_scope: ["capability_x"]
depends_on: []
---

# Skill: your-skill-name

## When to Use

- Applicable scenario 1
- Applicable scenario 2

## When NOT to Use

- Not applicable scenario 1
- Not applicable scenario 2

## Workflow

1. Step one
2. Step two
3. Step three

## API Matrix

| API | Purpose | Required Fields | Notes |
|-----|---------|-----------------|-------|
| `methodA` | Purpose description | `field_a`, `field_b` | Remarks |

### Minimal Request/Response Pattern

Request example:

```json
{
  "field": "value"
}
```

Response example:

```json
{
  "ok": true,
  "result": {}
}
```

## Error Handling

- `401`: Handling strategy
- `429`: Handling strategy
- `Business error`: Handling strategy

## Security Notes

- Permission boundaries
- Risk controls

## Examples

See `examples.md` in this directory.
