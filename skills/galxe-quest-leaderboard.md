---
name: Read Galxe quest status and space leaderboard
description: Fetch a quest's live status and page through a space's loyalty-points leaderboard.
api: https://graphigo-business.prd.galaxy.eco/query
protocol: graphql
operations: [quest, space, space.loyaltyPointsRanks]
auth: access-token header
---

# Read Galxe quest status and space leaderboard

## Prerequisites
- A Server API access token.
- A quest ID and/or a numeric space ID.

## Steps
1. POST to `https://graphigo-business.prd.galaxy.eco/query` with `access-token: <TOKEN>`.
2. Quest status:
   ```graphql
   query GetQuest($id: ID!) { quest(id: $id) { name status participantsCount } }
   ```
   `status` ∈ Draft, Active, NotStarted, Expired, CapReached, Deleted.
3. Space leaderboard (cursor-paginated):
   ```graphql
   query GetLeaderboard($spaceId: Int!, $cursorAfter: String) {
     space(id: $spaceId) {
       name
       loyaltyPointsRanks(cursorAfter: $cursorAfter) {
         totalCount
         list { rank points address { username } }
       }
     }
   }
   ```
4. Page forward by passing the last `cursorAfter`; there is no direct user-position lookup — paginate to find a user.

## Rules & conventions
- Use `cursorAfter`/`cursorBefore`; the `first`/`after` params are deprecated — see `conventions/galxe-conventions.yml`.
- Handle `429` with exponential backoff (`rate-limits/galxe-rate-limits.yml`).
- Errors follow the GraphQL `errors[].extensions` envelope (`errors/galxe-error-codes.yml`).
