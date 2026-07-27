---
name: Check user eligibility for a Galxe credential
description: Verify whether a wallet address satisfies a Galxe credential's requirement, for gating access or rewards.
api: https://graphigo-business.prd.galaxy.eco/query
protocol: graphql
operations: [credential, credential.eligible]
auth: access-token header
---

# Check user eligibility for a Galxe credential

Use the Galxe Integration GraphQL API to confirm a user (by wallet address) meets a credential's condition.

## Prerequisites
- A Galxe **Server API** access token (dashboard → Account → Settings → Server API → Generate New API Tokens).
- The target **credential ID** (`credId`).

## Steps
1. POST to `https://graphigo-business.prd.galaxy.eco/query` with headers
   `content-type: application/json` and `access-token: <TOKEN>`.
2. Read the credential and check eligibility in one query:
   ```graphql
   query CheckEligibility($credId: ID!, $address: String!, $campaignId: ID) {
     credential(id: $credId) {
       name
       credType
       eligible(address: $address, campaignId: $campaignId)
     }
   }
   ```
3. Interpret `eligible`: `1` = eligible, `0` = not eligible.

## Rules & conventions
- Errors arrive in the GraphQL `errors[]` array; inspect `extensions.code`
  (`INVALID_TOKEN`, `PERMISSION_DENIED`, `NOT_FOUND`, `INVALID_ARGUMENT`) — see `errors/galxe-error-codes.yml`.
- On HTTP `429` (`QUOTA_EXCEEDED`) back off exponentially — limits in `rate-limits/galxe-rate-limits.yml`.
- The token's wallet must have admin/owner access to the space for credentials it owns.
