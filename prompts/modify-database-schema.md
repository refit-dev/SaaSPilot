# Prompt: Modify Database Schema

Use this template when you need to change the Prisma schema.

## Template

```
I need to modify the database schema in SaaSPilot.

**Change Type**:
- [ ] Add new model
- [ ] Add fields to existing model
- [ ] Modify existing fields
- [ ] Add relations
- [ ] Delete model/fields
- [ ] Add indexes

**Model Name**: [Model name]

**Changes Needed**:

[For new model]
```prisma
model ModelName {
  id          String   @id @default(cuid())
  field1      String
  field2      Int?
  userId      String
  user        User     @relation(fields: [userId], references: [id])
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
}
```

[For adding fields]
Add to existing [ModelName]:
- field1: Type (description)
- field2: Type (description)

[For modifications]
Change:
- field1: from [current type] to [new type]
- Reason: [why this change]

**Migration Strategy**:
- [ ] Safe migration (no data loss)
- [ ] Requires data migration
- [ ] Default values needed

**Impact Analysis**:
Affects:
- [ ] Existing data (how?)
- [ ] API endpoints (which ones?)
- [ ] UI components (which ones?)
- [ ] Server actions (which ones?)

**Related Code Updates**:
After schema change, update:
1. [File/component 1]
2. [File/component 2]
3. TypeScript types
4. Validation schemas

**Steps to Implement**:
1. Update /prisma/schema.prisma
2. Run: npx prisma format
3. Run: npx prisma generate
4. Run: npx prisma db push (or migrate dev)
5. Update affected code
6. Test thoroughly

Please help me implement these database changes safely.
```

## Example Usage

```
I need to modify the database schema in SaaSPilot.

**Change Type**:
- [x] Add new model

**Model Name**: Task

**Changes Needed**:

```prisma
model Task {
  id          String   @id @default(cuid())
  title       String
  description String?
  status      TaskStatus @default(TODO)
  dueDate     DateTime?
  userId      String
  user        User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt

  @@index([userId])
  @@index([status])
}

enum TaskStatus {
  TODO
  IN_PROGRESS
  DONE
}
```

Also need to add relation to User model:
```prisma
model User {
  // ... existing fields
  tasks       Task[]
}
```

**Migration Strategy**:
- [x] Safe migration (no data loss)
- New model, no existing data affected

**Impact Analysis**:
Affects:
- [x] Existing data: None (new model)
- [x] API endpoints: Need to create new endpoints
- [x] UI components: Need to create task management UI
- [x] Server actions: Need to create task CRUD actions

**Related Code Updates**:
After schema change, update:
1. Create /actions/tasks/ directory with CRUD actions
2. Create /app/dashboard/tasks/ page
3. Create task-related components
4. Add TaskStatus type to /types/index.ts
5. Create validation schemas in /schemas/task-schema.ts

**Steps to Implement**:
1. Update /prisma/schema.prisma
2. Run: npx prisma format
3. Run: npx prisma generate
4. Run: npx prisma db push
5. Create related files (actions, pages, components)
6. Test task creation, reading, updating, deletion

Please help me implement these database changes safely.
```
