---
name: convex-developer
description: Use when working with Convex - guides implementation of Convex functions, schema design, and best practices for database interactions to ensure efficient, maintainable, and scalable data management.
---

## Convex Fundamentals 

### Each function type

| Type | Use When | Cannot |
|---|---|---|
| `query()` | Reading data, fetching context | Write to DB, call external APIs |
| `mutation()` | Writing data, any DB insert/patch/delete | Call external APIs (Twilio, LLM, etc.) |
| `action()` | Calling external APIs, complex workflows | Use `ctx.db` directly — must use `ctx.runQuery`/`ctx.runMutation` |
| `internalAction()` / `internalMutation()` | Functions only called by other Convex functions (not from client) 

### Index Usage — Always Use Indexes for Lookups

Every table has an index. Always use it — never do a full table scan for data.

```typescript
// WRONG — full table scan
const state = await ctx.db.query('worldStates').filter(q => q.eq(q.field('playerId'), playerId)).unique();

// RIGHT — uses index
const state = await ctx.db.query('worldStates').withIndex('by_player', q => q.eq('playerId', playerId)).unique();
```
### Upsert Pattern

Several functions need upsert behavior (insert if not exists, patch if exists). Convex has no native upsert — implement it explicitly:

```typescript
const existing = await ctx.db.query('tableName').withIndex('by_player', q => q.eq('playerId', playerId)).unique();
if (existing) {
  await ctx.db.patch(existing._id, { ...updates });
} else {
  await ctx.db.insert('tableName', { playerId, ...defaults });
}
```

### `ctx.db.replace()` vs `ctx.db.patch()`

- `patch()` — merges fields. Existing fields not in the update are preserved.
- `replace()` — replaces the entire document. Use only for full resets (e.g. `worldStates.reset`).

### External API Calls Belong in Actions, Not Mutations
```typescript
// WRONG
export const badMutation = mutation({
  handler: async (ctx, args) => {
    await fetch('https://api.twilio.com/...'); // ❌ network calls in mutations break
  }
});
```
## Schema Change Protocol


When adding a field to an existing table:
1. Add it to `convex/schema.ts` with `v.optional()` if existing records won't have it
2. Update the relevant TypeScript interface in `convex/types.ts`
3. Update the Inter-Agent Contract skill document
4. Notify other agents if any function signatures change

When adding a new table:
1. Add to `convex/schema.ts`
2. Create the corresponding `convex/tableName.ts` with all CRUD functions
3. Add appropriate indexes — every table that is looked up should have an index
---

Defined in `convex/crons.ts`.  Example of how to construct cron jobs when needed:

```typescript
import { cronJobs } from 'convex/server';
import { internal } from './_generated/api';

const crons = cronJobs();

crons.hourly('npc-nudge', { minuteUTC: 0 }, internal.inactivity.sendNudges);

export default crons;
```

Rules for crons:
- Always use `internalAction` for cron handlers — they should not be callable from the client
- Crons must handle empty result sets gracefully (no data to run against = no error)
- Verify crons are visible in the Convex dashboard after deploying

---


## Acceptance Criteria for Every Function You Write

- [ ] Uses the correct function type (query/mutation/action) for its purpose
- [ ] Uses indexes for all specific lookups — no full table scans
- [ ] Is exported with a clear, consistent name matching what it does, append get to getting data functions, append delete to deleting functions, append updating to updating functions
- [ ] Handles the "record not found" case explicitly — no silent nulls
- [ ] Validators match the schema exactly — no loose `v.any()` unless schema uses `v.object({})`
- [ ] After writing: update Project Overview or Contract if function signature is new to make other agents aware of availability

