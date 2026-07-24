---
name: Log a conversation to Botanalytics
description: >-
  Send chatbot / voice-assistant conversation messages to Botanalytics for
  conversational analytics, using the v2 message ingestion API.
api: openapi/botanalytics-messages-openapi.yml
operations:
  - logMessages
---

# Log a conversation to Botanalytics

Use this skill to submit conversation message events from a chatbot or voice
assistant to Botanalytics for analytics processing.

## Prerequisites

- A Botanalytics API key. It is a **JWT** whose `channel` claim identifies the
  source channel (e.g. `universal`, `facebook-messenger`, `amazon-alexa`,
  `microsoft-bot-framework`, `samsung-bixby`). Get one by creating an account
  at https://botanalytics.co.

## Steps

1. **Authenticate.** Send the API key as `Authorization: Bearer <token>`. The
   `channel` claim in the JWT must match the channel your messages come from.

2. **Call `logMessages`** — `POST https://api.beta.botanalytics.co/v2/messages`
   with `Content-Type: application/json` and a body of the shape:

   ```json
   { "messages": [ { /* channel message event */ } ] }
   ```

   Batch multiple events in one call.

3. **Handle the response.**
   - `200` / `201` — accepted.
   - `400` — bad request (missing/invalid parameter); fix the body.
   - `401` — missing or wrong token; check the Bearer JWT.
   - `404` — resource not found.
   - `500`–`504` — server error; retry with backoff.

   Errors are returned as a JSON object in the response body
   (see `errors/botanalytics-problem-types.yml`).

## Conventions

- **No idempotency key** is documented. The official SDKs retry failed POSTs
  (default limit 10, 30s timeout), so delivery is at-least-once — dedupe
  downstream if exact-once matters.
- Prefer the first-party SDKs (`@botanalytics/core`, `botanalytics` on PyPI,
  `co.botanalytics:java-sdk-core`) which wrap this operation per channel — see
  `packages/botanalytics-packages.yml`.
