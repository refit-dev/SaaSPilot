# Prompt: Create API Endpoint

Use this template when you need to add a new API route.

## Template

```
I need to create a new API endpoint in SaaSPilot.

**Endpoint Details**:
- Path: /api/[path]
- Methods: [GET/POST/PUT/DELETE]
- Purpose: [What this endpoint does]

**Authentication**:
- [ ] Public endpoint
- [ ] Requires authentication
- [ ] Requires specific role: [role]

**Request Format**:

[For GET]
Query Parameters:
- param1 (type): description
- param2 (type, optional): description

[For POST/PUT]
Body (JSON):
```json
{
  "field1": "value",
  "field2": 123
}
```

**Response Format**:

Success (200/201):
```json
{
  "data": {
    // response data
  }
}
```

Error (400/401/500):
```json
{
  "error": "Error message",
  "code": "ERROR_CODE"
}
```

**Database Operations**:
- [ ] Read from: [model name]
- [ ] Write to: [model name]
- [ ] Update: [model name]
- [ ] Delete from: [model name]

**Validation Requirements**:
- Field1: [validation rules]
- Field2: [validation rules]

**Additional Notes**:
[Any special requirements, rate limiting, caching, etc.]

**Before implementing**:
1. Check /docs/api-documentation.md for existing endpoints
2. Follow pattern in /docs/coding-patterns.md (API Routes section)
3. Use consistent error handling

Please create this endpoint following SaaSPilot patterns.
```

## Example Usage

```
I need to create a new API endpoint in SaaSPilot.

**Endpoint Details**:
- Path: /api/tasks
- Methods: GET, POST
- Purpose: Fetch user tasks and create new tasks

**Authentication**:
- [x] Requires authentication

**Request Format**:

[GET]
Query Parameters:
- status (string, optional): filter by status (todo/in-progress/done)
- limit (number, optional): max results (default 50)

[POST]
Body (JSON):
```json
{
  "title": "Task title",
  "description": "Task description",
  "dueDate": "2024-12-31T00:00:00.000Z",
  "status": "todo"
}
```

**Response Format**:

Success GET (200):
```json
{
  "data": [
    {
      "id": "task_id",
      "title": "Task 1",
      "status": "todo",
      "dueDate": "2024-12-31T00:00:00.000Z",
      "createdAt": "2024-01-01T00:00:00.000Z"
    }
  ],
  "total": 10
}
```

Success POST (201):
```json
{
  "data": {
    "id": "task_id",
    "title": "New Task",
    "status": "todo"
  }
}
```

**Database Operations**:
- [x] Read from: Task
- [x] Write to: Task

**Validation Requirements**:
- title: required, min 1 char, max 200 chars
- description: optional, max 1000 chars
- status: required, enum (todo/in-progress/done)
- dueDate: optional, valid date in future

Please create this endpoint following SaaSPilot patterns.
```
