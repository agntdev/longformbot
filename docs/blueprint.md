# Longform Article Generator — Bot specification

**Archetype:** content

**Voice:** professional and concise — write every user-facing message, button label, error, and empty state in this voice.

A Telegram bot that generates 1,500+ word articles on user-requested topics, delivering results to both the requester and a designated owner account with full text and metadata retention.

> This is the complete contract for the bot. Implement EVERY entry point, flow, feature, integration, and edge case below. The completeness review checks the bot against this document after each build pass.

## Primary audience

- content creators
- bloggers
- educators
- researchers

## Success criteria

- Generate 1,500+ word articles on demand
- Deliver articles to user and owner with requester metadata
- Maintain request history with 90-day retention

## Entry points

Every feature must be reachable from the bot's command/button surface (button-first; only /start and /help are slash commands).

- **/start** (command, actor: user, command: /start) — Display main menu with article request instructions
- **/write** (command, actor: user, command: /write) — Initiate article request with topic parameter
- **/status** (command, actor: user, command: /status) — Check request status by ID
- **/cancel** (command, actor: user, command: /cancel) — Cancel queued request
- **Request Article** (button, actor: user, callback: request:start) — Open guided article request form

## Flows

### Article Request
_Trigger:_ /write

1. Receive topic input
2. Collect optional parameters (tone, keywords)
3. Confirm request with ETA
4. Generate article
5. Deliver to user and owner

_Data touched:_ Request, Article

### Status Check
_Trigger:_ /status

1. Validate request ID
2. Return current status and progress

_Data touched:_ Request

### Cancellation
_Trigger:_ /cancel

1. Verify request is queued
2. Mark as cancelled
3. Notify user

_Data touched:_ Request

## Data entities

Durable data (must survive a restart) uses the toolkit's persistent store, never in-memory maps.

- **Request** _(retention: persistent)_ — User article request metadata
  - fields: requester_id, topic, tone, keywords, word_count, status, timestamp
- **Article** _(retention: persistent)_ — Generated article content
  - fields: content, title, meta_description, word_count
- **User** _(retention: session)_ — Telegram user account
  - fields: telegram_id, username
- **Owner** _(retention: persistent)_ — Designated owner account
  - fields: telegram_id, notification_preferences

## Integrations

- **Telegram** (required) — Bot API messaging and notifications
Call external APIs against their real contract (correct endpoints, ids, params); credentials from env. Do not fake responses.

## Owner controls

- Set owner Telegram ID
- Configure retention period (default 90 days)
- Adjust rate limits (free/paid tiers)
- View audit trail of all requests

## Notifications

- Send full article to requester
- Send article + requester metadata to owner
- Status updates for queued requests

## Permissions & privacy

- Anonymize user data after retention period
- Require explicit consent for extended data retention
- No third-party data sharing

## Edge cases

- Handling >10,000 word articles as downloadable files
- Rate limit enforcement for free tier
- Invalid request IDs in /status
- Missing parameters in /write command

## Required tests

- End-to-end article generation flow
- Notification delivery to owner
- Rate limit enforcement
- Status tracking accuracy

## Assumptions

- Owner will manually handle payment integration later
- Single owner account for all notifications
- Default 1,500 word minimum enforced
