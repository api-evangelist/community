---
name: Onboard and segment a Community member
description: Invite/update a member, attach custom data, and place them into a subcommunity (tag) for targeted messaging.
api: openapi/community-async-openapi-original.yml
operations:
- upsert-member
- upsert-member-custom-data
- tag-create
- change-community-membership
- opt-out-member
---

# Onboard and segment a member

Create or update a member, enrich them with custom data, and organize them into
a subcommunity so campaigns can target the right segment.

## Prerequisites
- Provisioned API access and a bearer/OAuth2 token (see
  `authentication/community-authentication.yml`).
- Base: `https://api.community.com/webhooks/v1/community/{client_id}`, JSON body,
  `Authorization: Bearer <token>`.

## Steps
1. **upsert-member** (`POST /subscription_create`) — invite or update a member
   by `phone_number` with standard fields (first_name, last_name, email, etc.).
   Returns 202; the member becomes `live` only after they opt in.
2. **upsert-member-custom-data** (`POST /subscription_field_value_modify`) —
   set custom field values on the member for segmentation/personalization.
3. **tag-create** (`POST /tag_create`) — create a subcommunity (tag) if the
   target segment does not exist yet (title, description, color).
4. **change-community-membership** (`POST /subscription_tag_modify`) — add or
   remove the member from the subcommunity. Pass `tag_id` (preferred) or `tag`
   name; `tag_id` wins if both are provided.
5. To honor an opt-out, call **opt-out-member** (`POST /subscription_opt_out`).

## Rules and gotchas
- All calls are asynchronous and return **202 Accepted**.
- Opt-out is sticky: a deleted/opted-out number is prevented from API/import
  resubscription for a cooldown period (protects member consent).
- An account allows up to 200 subcommunities by default.
- Errors are HTTP-status only (see `errors/community-problem-types.yml`).
