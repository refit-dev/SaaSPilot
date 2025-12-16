# Prompt: Add New Feature

Use this template when asking AI to add a completely new feature to SaaSPilot.

## Template

```
I need to add a new feature to SaaSPilot: [FEATURE NAME]

**Feature Description**:
[Describe what the feature does]

**User Story**:
As a [user type], I want to [action] so that [benefit].

**Requirements**:
1. [Requirement 1]
2. [Requirement 2]
3. [Requirement 3]

**Database Changes Needed**:
- [ ] New model(s): [Model names]
- [ ] Update existing model: [Which one]
- [ ] New fields: [List fields]

**UI Components Needed**:
- [ ] New page(s): [Page names and routes]
- [ ] New components: [Component names]
- [ ] Forms: [What forms]
- [ ] Dashboards/views: [What views]

**API/Actions Needed**:
- [ ] Server actions: [List actions]
- [ ] API routes: [List endpoints]
- [ ] External integrations: [Any third-party services]

**Authentication**:
- [ ] Public feature (no auth required)
- [ ] Requires authentication
- [ ] Requires specific role: [role name]

**Before you start**:
1. Read /docs/ai-instructions.md
2. Review /docs/coding-patterns.md
3. Check /examples for similar features
4. Follow the established patterns

**Acceptance Criteria**:
- [ ] Feature works as described
- [ ] Follows coding patterns
- [ ] Has proper error handling
- [ ] Is type-safe (TypeScript)
- [ ] Has appropriate authentication
- [ ] Database schema is updated
- [ ] UI is responsive and accessible

Please implement this feature step by step, explaining each part.
```

## Example Usage

```
I need to add a new feature to SaaSPilot: Task Management

**Feature Description**:
Users can create, update, delete, and view their tasks. Tasks have titles, descriptions, due dates, and status (todo, in-progress, done).

**User Story**:
As a logged-in user, I want to manage my tasks so that I can track my work.

**Requirements**:
1. Create new tasks with title, description, due date
2. View all my tasks in a list
3. Update task status and details
4. Delete tasks
5. Filter tasks by status

**Database Changes Needed**:
- [x] New model: Task
  - id (string, primary key)
  - title (string)
  - description (string, optional)
  - status (enum: TODO, IN_PROGRESS, DONE)
  - dueDate (datetime, optional)
  - userId (string, foreign key)
  - createdAt (datetime)
  - updatedAt (datetime)

**UI Components Needed**:
- [x] New page: /dashboard/tasks
- [x] Components:
  - TaskList (displays all tasks)
  - TaskItem (individual task card)
  - TaskForm (create/edit form)
  - TaskFilters (filter by status)

**API/Actions Needed**:
- [x] Server actions:
  - createTask
  - updateTask
  - deleteTask
  - getTasks (with filters)

**Authentication**:
- [x] Requires authentication
- [x] Users can only see/manage their own tasks

Please implement this feature step by step, explaining each part.
```
