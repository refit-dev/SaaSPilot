# AI Coding Agent Instructions for SaaSPilot

## 🤖 For AI Assistants

This document provides context and instructions for AI coding agents working with the SaaSPilot codebase.

### Quick Context
- **Framework**: Next.js 14+ (App Router)
- **Language**: TypeScript
- **Database**: MongoDB with Prisma ORM
- **Authentication**: NextAuth.js
- **Payments**: Stripe (Credit-based system)
- **UI**: Tailwind CSS + Shadcn/ui
- **Email**: Resend / SendGrid

### Project Structure Philosophy
- Feature-based organization in `/app` directory
- Shared components in `/components`
- Business logic in `/actions` (server actions)
- Database queries through Prisma in `/db`
- Type definitions in `/types`

### Key Conventions

#### 1. Naming Conventions
- Components: PascalCase (`UserDashboard.tsx`)
- Files: kebab-case for non-components (`user-service.ts`)
- API routes: kebab-case (`/api/user-profile/route.ts`)
- Database models: PascalCase (`User`, `Credit`)

#### 2. File Structure Pattern
Every feature should follow this structure:
```
/app/feature-name/
├── page.tsx (main page)
├── layout.tsx (if needed)
├── loading.tsx (loading state)
├── error.tsx (error boundary)
└── components/ (feature-specific components)
```

#### 3. Server Actions Pattern
Location: `/actions/feature-name/`
```typescript
'use server'

import { auth } from '@/auth'
import { db } from '@/db'

export async function actionName(data: ActionInput): Promise<ActionResult> {
  // 1. Authentication check
  const session = await auth()
  if (!session?.user) {
    return { error: 'Unauthorized' }
  }

  // 2. Validation
  const validated = schema.parse(data)

  // 3. Database operation
  const result = await db.model.operation()

  // 4. Return result
  return { success: true, data: result }
}
```

#### 4. API Route Pattern
Location: `/app/api/resource-name/route.ts`
```typescript
import { NextRequest, NextResponse } from 'next/server'
import { auth } from '@/auth'

export async function GET(req: NextRequest) {
  const session = await auth()
  if (!session) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 })
  }

  // Logic here
  return NextResponse.json({ data })
}
```

#### 5. Component Pattern
```typescript
'use client' // Only if client-side features needed

import { ComponentProps } from '@/types'

interface Props {
  // Props with JSDoc
}

/**
 * @ai-context Brief description of component purpose
 * @ai-dependencies List any external dependencies
 * @ai-modify-safe Yes/No - whether AI can safely modify
 */
export function ComponentName({ prop1, prop2 }: Props) {
  // Component logic
  return (
    // JSX
  )
}
```

### Database Operations

#### Always use Prisma client
```typescript
import { db } from '@/db'

// ✅ Good
const user = await db.user.findUnique({ where: { id } })

// ❌ Avoid raw queries unless absolutely necessary
```

#### Transaction pattern for related operations
```typescript
const result = await db.$transaction(async (tx) => {
  const user = await tx.user.create({ data: userData })
  const credit = await tx.credit.create({ data: { userId: user.id } })
  return { user, credit }
})
```

### Authentication Checks

Always verify authentication before protected operations:
```typescript
import { auth } from '@/auth'

const session = await auth()
if (!session?.user?.id) {
  // Handle unauthorized
}
```

### Environment Variables

Access through `process.env` with fallbacks:
```typescript
const apiKey = process.env.API_KEY
if (!apiKey) {
  throw new Error('API_KEY is required')
}
```

### Error Handling

Use consistent error patterns:
```typescript
try {
  // Operation
} catch (error) {
  console.error('Operation failed:', error)
  return { error: 'User-friendly error message' }
}
```

### Styling

- Use Tailwind utility classes
- Follow Shadcn/ui component patterns
- Responsive design: mobile-first (sm, md, lg, xl breakpoints)
- Dark mode support: use `dark:` prefix

### Testing Approach (when adding tests)

```typescript
import { describe, it, expect } from '@jest/globals'

describe('Feature Name', () => {
  it('should perform expected behavior', async () => {
    // Arrange
    // Act
    // Assert
  })
})
```

### Common Modification Scenarios

#### Adding a New Database Model
1. Update `prisma/schema.prisma`
2. Run `npx prisma generate`
3. Create migration: `npx prisma migrate dev`
4. Update TypeScript types if needed

#### Adding a New API Endpoint
1. Create `/app/api/endpoint-name/route.ts`
2. Implement HTTP methods (GET, POST, etc.)
3. Add authentication checks
4. Document in `/docs/api-documentation.md`

#### Adding a New Feature Page
1. Create `/app/feature-name/page.tsx`
2. Add server actions in `/actions/feature-name/`
3. Create components in feature directory
4. Update navigation if needed

#### Integrating Third-Party Service
1. Add credentials to `.env.example`
2. Create service file in `/lib/service-name.ts`
3. Add initialization in appropriate location
4. Document usage in `/docs/`

### Files You Can Safely Modify
- Any file in `/app` (pages, layouts, components)
- Any file in `/components`
- Server actions in `/actions`
- Libraries in `/lib`
- Schemas in `/schemas`
- Styles in `/app/globals.css`

### Files to Modify with Caution
- `/auth.ts` and `/auth.config.ts` (authentication core)
- `/middleware.ts` (routing and protection)
- `/prisma/schema.prisma` (requires migration)
- Environment variable handling

### Files NOT to Modify
- `/package-lock.json` (use package.json instead)
- Generated files in `/node_modules`
- `.next/` build directory

### When You're Unsure
1. Ask for clarification
2. Suggest creating a new file rather than modifying core files
3. Propose the change before implementing
4. Add TODO comments for human review

### Useful Commands
```bash
# Development
npm run dev

# Database
npx prisma studio          # Visual database editor
npx prisma generate        # Generate Prisma client
npx prisma migrate dev     # Create and apply migration
npx prisma db push         # Push schema without migration

# Build
npm run build
npm run start

# Linting
npm run lint
```

### Getting Help
- Check `/docs` directory for specific documentation
- Review `/examples` for implementation patterns
- See `/prompts` for common task templates
