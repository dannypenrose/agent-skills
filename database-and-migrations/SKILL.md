---
name: database-and-migrations
description: >
  Enforce database design and migration standards when working with database schemas, queries,
  migrations, or ORM configurations. Use this skill whenever the user creates or modifies Prisma
  schemas, Entity Framework models, SQLAlchemy models, Django models, raw SQL, database indexes,
  migration files, seed data, or data backfill scripts. Also applies when discussing query
  optimization, N+1 problems, connection pooling, naming conventions, or zero-downtime migrations
  -- even if the user doesn't explicitly mention 'database standards'.
---

# Database & Migration Standards

When this skill triggers, follow these steps:

## 1. Read the Standards

**Always read the database standards document first:**

```
https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/governance/database-standards.md
```

**If the task involves a data migration** (backfill, transform, platform migration, cleanup, merge/split), also read:

```
https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/data-migration.md
```

Fetch these documents before writing or reviewing any database code. Do not rely on the quick-reference below as a substitute for the full standards on complex tasks.

## 2. Quick-Reference (Critical Rules)

For simple schema changes, apply these rules directly. For anything complex, read the full doc.

### Naming Conventions

| Element            | Convention                    | Example                       |
| ------------------ | ----------------------------- | ----------------------------- |
| Tables             | snake_case, **plural**        | `users`, `order_items`        |
| Columns            | snake_case                    | `created_at`, `user_id`      |
| Primary keys       | `id`                          | `id`                          |
| Foreign keys       | `{referenced_table_singular}_id` | `user_id`, `order_id`     |
| Indexes            | `idx_{table}_{columns}`       | `idx_users_email`             |
| Unique constraints | `uq_{table}_{columns}`        | `uq_users_email`             |
| Check constraints  | `chk_{table}_{description}`   | `chk_orders_positive_total`  |

### Required Columns (Every Table)

Every table must have these columns:

- `id` -- primary key (UUID v4/v7 or BIGSERIAL depending on context)
- `created_at` -- `TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT NOW()`
- `updated_at` -- `TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT NOW()` (with trigger or ORM auto-update)
- `deleted_at` -- nullable, only when soft deletes are needed

In Prisma, use `@map()` and `@@map()` to ensure database-level snake_case:

```prisma
model User {
  id        String   @id @default(uuid()) @db.Uuid
  email     String   @unique
  createdAt DateTime @default(now()) @map("created_at")
  updatedAt DateTime @updatedAt @map("updated_at")

  @@map("users")
}
```

### Index Patterns

- Always index foreign key columns
- Composite indexes: column order must match query patterns (leftmost prefix rule)
- Use partial indexes for filtered queries (`WHERE status = 'pending'`)
- Use `CREATE INDEX CONCURRENTLY` in production (avoids table lock)
- No redundant indexes -- a composite index on `(a, b)` already covers queries on `(a)`

### Migration Safety Rules

| Rule | Details |
| ---- | ------- |
| New columns must be **nullable or have a DEFAULT** | Never add `NOT NULL` without a default to an existing table |
| Never drop columns in one step | Step 1: stop writing. Step 2: deploy code that doesn't read. Step 3: drop in separate migration |
| Create indexes **concurrently** | `CREATE INDEX CONCURRENTLY` to avoid locking |
| No in-place type changes on large tables | Add new column, backfill, switch reads, drop old column |
| Rename columns via add/backfill/switch/drop | Not `ALTER COLUMN RENAME` (breaks running code) |
| Test on production-size data | Small test DBs hide performance problems |

### N+1 Prevention

When reviewing or writing queries:

```typescript
// BAD: N+1
const users = await prisma.user.findMany();
for (const user of users) {
  const posts = await prisma.post.findMany({ where: { userId: user.id } });
}

// GOOD: Eager loading
const users = await prisma.user.findMany({ include: { posts: true } });

// GOOD: Batch with IN clause
const userIds = users.map(u => u.id);
const posts = await prisma.post.findMany({ where: { userId: { in: userIds } } });
```

### Data Migration Rules (Backfills/Transforms)

When writing data migrations, enforce:

- **Idempotent**: Safe to run multiple times (use guard conditions like `WHERE full_name IS NULL`)
- **Batched**: Process in chunks (1000 rows), never one giant transaction
- **Cursor-based**: Use cursor pagination, not OFFSET
- **Dry-run mode**: Support `--dry-run` flag that logs what would change without writing
- **Pause/resume**: Support resuming from a cursor after interruption
- **Reversible**: Document rollback procedure before execution
- **Validated**: Pre-migration checks (row counts, edge cases) and post-migration verification (spot checks, integrity)

## 3. Enforcement Checklist

Before approving any database change, verify:

- [ ] Naming conventions followed (snake_case tables plural, snake_case columns)
- [ ] `id`, `created_at`, `updated_at` present on every new table
- [ ] Foreign keys have explicit `ON DELETE` actions
- [ ] Foreign key columns are indexed
- [ ] New columns on existing tables are nullable or have defaults
- [ ] Indexes created concurrently (production contexts)
- [ ] No N+1 query patterns in associated application code
- [ ] Migrations are reversible (Down/rollback defined)
- [ ] If data migration: idempotent, batched, dry-run supported, rollback documented
