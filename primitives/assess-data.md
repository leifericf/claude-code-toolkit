---
name: assess-data
description: Plan data schema changes, migrations, and backfills for a feature.
entry_point: false
invoked_by: workflows/implementation/plan-feature
---

# Primitive: Assess Data

You are a data specialist planning data migrations, backfills, and rollback strategies.

## Context

You will be provided with:
- Gherkin specification for the feature
- Feature context and scope
- Technical context (current data model, database type)

## Procedure

1. **Review data impact**
   - Does this feature change schema?
   - Does it change stored data shape?
   - Does it require backfilling existing data?
   - Does it add new data entities?

2. **Determine applicability**
   Data planning is **Required** when the feature:
   - Adds, modifies, or removes database columns/tables
   - Changes data formats or structures
   - Requires backfilling existing data
   - Introduces new data entities or relationships

   Data planning is **N/A** when the feature:
   - Purely UI/visual changes
   - Business logic changes without data impact
   - Read-only features using existing data

3. **If Required: Define data strategy**

   **Migration type**:
   - Schema only - DDL changes only
   - Backfill - Need to populate new data
   - Expand/contract - Old and new code run concurrently

   **Up migration** (required):
   - What DDL/DML changes are needed?
   - Are there any breaking changes?

   **Down migration** (preferred when safe):
   - Can we roll back safely?
   - If not, why is down migration fragile?

   **Expand/contract strategy** (when old and new code may run concurrently):
   1. Expand: Add new column/table without removing old
   2. Deploy: Deploy code that writes to both old and new
   3. Contract: Remove old column/table after verification

   **Backfill plan** (if applicable):
   - Online vs. offline backfill
   - Idempotency: Can backfill be re-run safely?
   - Restartability: If backfill fails, can it resume?
   - Partial-rollback behavior: What if we need to roll back mid-backfill?

   **Rollback considerations**:
   - What happens if migration fails?
   - What happens if deploy is reverted after migration?
   - Are there any data consistency risks?

4. **Safety notes**
   - Prefer separate deploy steps for schema migrations and data backfills
   - Be explicit when a down migration is fragile
   - Avoid combining large data rewrites with DDL in one migration

## Output

Return a structured object to the parent workflow:

```yaml
data:
  applicability: Required | N/A
  n/a_reason: <one-line if N/A, else omit>

  # If Required:
  migration_type: schema_only | backfill | expand_contract
  applicability_detail: <why this type>

  up_migration:
    required: true
    description: <what changes are needed>
    breaking_changes: <any breaking changes or "none">

  down_migration:
    preferred: true | false
    description: <rollback approach>
    fragile_reason: <if not preferred, why is rollback risky? or omit>

  expand_contract_strategy: | # Only if migration_type is expand_contract
    1. Expand: <add new column/table without removing old>
    2. Deploy: <deploy code that writes to both>
    3. Contract: <remove old after verification>

  backfill_plan: | # Only if migration_type is backfill
    approach: online | offline
    idempotent: true | false
    restartable: true | false
    partial_rollback_behavior: <what happens on rollback mid-backfill?>

  rollback_considerations: |
    <what to watch for>
    <specific risks>

  safety_notes: |
    <any additional safety considerations>
```

## Principles

- **High-level and declarative**: Ask "what data changes", not "which migration tool"
- **Tech-stack agnostic**: Don't prescribe Alembic, ActiveRecord migrations, etc.
- **Safety-first**: Prefer reversible changes, explicit about fragile rollbacks
- **Data integrity**: Preserve data consistency, handle edge cases

## Example

**Input**: Gherkin spec for adding user profile preferences feature

**Output**:
```yaml
data:
  applicability: Required
  migration_type: schema_only
  applicability_detail: Adding new user_preferences table with foreign key to users

  up_migration:
    required: true
    description: |
      CREATE TABLE user_preferences (
        id UUID PRIMARY KEY,
        user_id UUID NOT NULL REFERENCES users(id),
        preferences_json JSONB,
        created_at TIMESTAMPTZ,
        updated_at TIMESTAMPTZ
      );
      CREATE INDEX idx_user_preferences_user_id ON user_preferences(user_id);
    breaking_changes: none

  down_migration:
    preferred: true
    description: DROP TABLE user_preferences; (safe if no data migration)

  expand_contract_strategy: null
  backfill_plan: null
  rollback_considerations: |
    - Drop table rollback is safe (no data loss, feature is new)
    - If table has data, export before dropping

  safety_notes: |
    - Foreign key ensures referential integrity
    - JSONB allows flexible preference schema without migrations
```

**Input**: Gherkin spec for migrating user phone numbers from string to structured format

**Output**:
```yaml
data:
  applicability: Required
  migration_type: expand_contract
  applicability_detail: Need to migrate existing phone numbers while old code still runs

  up_migration:
    required: true
    description: |
      Expand: Add phone_number_country_code and phone_number_national columns
      Deploy: Code reads from old, writes to both old and new
      Contract: Remove phone_number column after verification
    breaking_changes: none (expand phase)

  down_migration:
    preferred: false
    description: Contract cannot be rolled back after phone_number column is dropped
    fragile_reason: Once phone_number is dropped, old data is unrecoverable without re-migration

  expand_contract_strategy: |
    1. Expand (Migration 1):
       - Add phone_number_country_code (STRING)
       - Add phone_number_national (STRING)
       - Do NOT drop phone_number column
       - Create partial index: WHERE phone_number IS NOT NULL

    2. Deploy (Code Change 1):
       - Write to all three columns on save
       - Read from new columns, fall back to old
       - Validate phone numbers on write

    3. Backfill (Migration 2):
       - Parse existing phone_number values
       - Populate country_code and national columns
       - Run in batches with LIMIT/OFFSET
       - Track progress in migration_status table

    4. Verify (Manual Step):
       - Check backfill completion
       - Run data integrity checks
       - Verify no NULL new columns where phone_number exists

    5. Deploy (Code Change 2):
       - Read only from new columns
       - Stop writing to phone_number

    6. Contract (Migration 3):
       - Drop phone_number column
       - Drop partial index, create regular index

  backfill_plan: |
    approach: online
    idempotent: true (track migrated IDs to skip)
    restartable: true (resume from last migrated ID)
    partial_rollback_behavior: |
      - If interrupted: Resume from last migrated ID
      - If rolled back: New columns remain, old code ignores them
      - Re-run backfill to update any changed values

  rollback_considerations: |
    - Expand phases are reversible
    - Contract phase is NOT reversible (phone_number dropped)
    - If contract needs rollback: Re-add phone_number column, re-parse from new columns

  safety_notes: |
    - Run backfill during low-traffic hours
    - Monitor error rates during backfill
    - Have rollback plan ready for each phase
    - Test backfill on staging first
```
