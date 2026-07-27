---
name: Push members into a Galxe API credential
description: Add, remove, or replace the eligible-address list of a Galxe API-type credential idempotently.
api: https://graphigo-business.prd.galaxy.eco/query
protocol: graphql
operations: [credentialItems]
auth: access-token header
---

# Push members into a Galxe API credential

Manage who can pass a credential by writing its address list with the `credentialItems` mutation.

## Prerequisites
- A Server API access token whose wallet has **admin/owner** access to the space.
- The `credId` of an **API**-source credential.

## Steps
1. POST to `https://graphigo-business.prd.galaxy.eco/query` with `access-token: <TOKEN>`.
2. Choose the operation:
   ```graphql
   mutation UpdateItems($input: CredentialItemsInput!) {
     credentialItems(input: $input) { name }
   }
   ```
   - `APPEND` — add addresses to the list (idempotent, safe to retry).
   - `REMOVE` — remove addresses (idempotent, safe to retry).
   - `REPLACE` — overwrite the entire list (NOT idempotent; only for lists < 500).
3. For large updates prefer **REMOVE then APPEND** over `REPLACE`.

## Rules & conventions
- Idempotency: APPEND/REMOVE may be safely retried; REPLACE must not — see `conventions/galxe-conventions.yml`.
- Rate limits: 1500 req/5min and 10000 req/60min on the credential push API; `429` means back off (`rate-limits/galxe-rate-limits.yml`).
- Errors: GraphQL `errors[].extensions.code` (`PERMISSION_DENIED`, `INVALID_ARGUMENT`) — `errors/galxe-error-codes.yml`.
