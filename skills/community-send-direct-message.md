---
name: Send a direct message to a Community member
description: Send an SMS/WhatsApp Direct Message to a live member of a Community account, respecting quiet hours and deduplication.
api: openapi/community-async-openapi-original.yml
operations:
- send-message
---

# Send a direct message

Use Community's Async REST API to send a Direct Message (DM) to a member who is
in a `live` state.

## Prerequisites
- API access provisioned by Community (technical onboarding packet; contact
  saleseng@community.com). Access is not self-serve.
- A bearer token (JWT) or OAuth2 access token — see
  `authentication/community-authentication.yml`.
- Your account `client_id` for the server path.

## Base
`POST https://api.community.com/webhooks/v1/community/{client_id}/message_send`
Header: `Authorization: Bearer <token>`, `Content-Type: application/json`

## Steps
1. Format the recipient `phone_number` as a 10-digit US/CA number (or 11 digits
   with a leading `1`). Strip special characters for reliability.
2. Call **send-message** (`POST /message_send`) with `phone_number` and `text`.
3. Optional: set `override_quiet_hours: true` to bypass the default 11am-8pm ET
   Quiet Hours window (otherwise the message is queued until it ends).
4. Optional: set `communication_channel` to route via WhatsApp (best-effort;
   only within a 24h member-initiated window).
5. Expect **202 Accepted** — the send is asynchronous; the response body does
   not contain the delivered message.

## Rules and gotchas
- Identical `text` to the same `phone_number` is deduplicated (30 min for SMS,
  10 min for WhatsApp). Vary text while testing.
- Any link in `text` is rewritten by Community's link wrapper.
- The member must be `live`; new members must opt in first (see the member
  states guide).
- Errors are HTTP-status only (see `errors/community-problem-types.yml`):
  400 validation, 401 auth, 403 forbidden, 415 non-JSON body.
