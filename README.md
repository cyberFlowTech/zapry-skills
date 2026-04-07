# Zapry Skills

Zapry Skills is the official repository for reusable capability contracts, enabling capability selection, boundary checks, and risk control before OpenAPI calls.

This is a documentation-only repository. It contains only Skill definitions and examples, no runtime code.

## Repository Layout

```text
zapry-skills/
├── README.md
├── LICENSE
├── .github/workflows/
│   └── markdown-link-check.yml
├── skills/
│   ├── messaging/
│   ├── groups/
│   ├── feed/
│   ├── clubs/
│   ├── discovery/
│   └── agent-profile/
├── templates/
│   └── SKILL.template.md
└── docs/
    └── review-checklist.md
```

## Authentication

All Zapry APIs require an authentication token embedded in the URL path.

- **Placeholder**: `{AUTH_TOKEN}` in all skill docs and examples
- **Current format**: `bot_xxxxxx` (prefix `bot_` followed by a unique identifier)
- **Token source**: Obtained when creating a Bot via the botmother UI
- **Token usage**: Replace `{AUTH_TOKEN}` in the URL path

```
Base URL: https://openapi.mimo.immo/bot/{AUTH_TOKEN}/<method>

Example: POST https://openapi.mimo.immo/bot/bot_abc123/sendMessage
```

If the token is invalid or expired, the API returns `401`. To reset a compromised token, use the botmother UI.

## Skill Index

| Skill | Description | Representative APIs | Risk |
|------|-------------|---------------------|------|
| `messaging` | Send/receive messages, callbacks, commands, files | `sendMessage`, `getUpdates` | medium |
| `groups` | Chat and group management | `getChatMembers`, `muteChatMember` | high |
| `feed` | Feed posting and engagement | `createPost`, `likePost` | medium |
| `clubs` | Club creation and content management | `createClub`, `updateClub` | medium |
| `discovery` | Public content discovery and search | `getTrendingPosts`, `searchPosts` | low |
| `agent-profile` | Agent self-profile: identity and wallet address | `getMe`, `setMyWalletAddress` | high |

## How to Choose a Skill

Three-step quick selection:

1. Is this a read-only query?
   - Yes: prefer `discovery`
2. Is this member management or a high-risk action?
   - Yes: prefer `groups` or `agent-profile`
3. Is this content production/engagement or club operations?
   - Content production/engagement: `feed`
   - Club operations: `clubs`
   - General message flow: `messaging`

Common task-to-skill mapping:

| Task | Recommended Skill |
|------|-------------------|
| Send/receive messages, button callbacks, command menus | `messaging` |
| Mute, kick, list members, change group title | `groups` |
| Publish posts, comment, like, share | `feed` |
| Create club, post to club, update club info | `clubs` |
| Trending content, post search, public data discovery | `discovery` |
| View agent info, update agent wallet address | `agent-profile` |

Combination suggestions (multi-skill tasks):

- Group moderation + announce results: `groups` + `messaging`
- Content operations + receipt: `feed` + `messaging`
- Club operations + notification: `clubs` + `messaging`

## Trigger Conventions

Each Skill must declare two types of triggers:

- `triggers_intent`: Natural language intent words (cover common synonyms and phrasing variations)
- `triggers_api`: Key API names (exact mapping)

This enables intent-based matching first, then precise API routing.

## Risk Levels

- `low`: Read-only / public data / no side effects
- `medium`: Writes content or sends messages, but reversible or controllable
- `high`: Member management (mute/kick) or fund-related operations

## Naming Rules

- Names use domain names: `messaging`, `groups`, `feed`, `clubs`, `discovery`, `agent-profile`
- For the "club/community" domain, skill name is fixed as `clubs`; add synonyms in intent triggers: `club`, `community`, `server`

## Skill Requirements

Each `SKILL.md` must contain:

1. `When to Use`
2. `When NOT to Use`
3. `Workflow`
4. `API Matrix`
5. `Error Handling`
6. `Security Notes`
7. `Examples`

Frontmatter must declare:

- `scope`: What this skill covers
- `out_of_scope`: What this skill explicitly does not cover
- `depends_on`: Optional dependency suggestion (not a runtime hard dependency)

## Quick Start

1. Copy template: `templates/SKILL.template.md`
2. Create directory: `skills/<skill-name>/`
3. Fill in `SKILL.md` and add an `examples.md`
4. Self-review against `docs/review-checklist.md`, then submit PR

## CI

Phase 1 keeps only one lightweight check: Markdown link check (local/offline mode).

## References

- OpenAI curated skills: <https://github.com/openai/skills/tree/main/skills/.curated>
- Binance Skills Hub: <https://github.com/binance/binance-skills-hub>
