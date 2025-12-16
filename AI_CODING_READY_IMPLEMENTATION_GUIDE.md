# AI Coding Agent Ready - SaaSPilot Implementation Guide

## 🎯 Objective
Transform SaaSPilot into the most AI-coding-agent-friendly SaaS boilerplate by adding comprehensive documentation, standardized patterns, and intelligent code organization that enables AI assistants (Claude, Cursor, GitHub Copilot, etc.) to quickly understand and modify the codebase.

---

## 📋 Implementation Checklist

### Phase 1: Documentation Infrastructure (Priority: HIGH)
- [ ] Create `/docs` directory structure
- [ ] Add AI instruction files
- [ ] Document architecture and patterns
- [ ] Create API documentation
- [ ] Add component relationship maps

### Phase 2: Code Organization (Priority: HIGH)
- [ ] Add comprehensive JSDoc comments
- [ ] Implement standardized file headers
- [ ] Create pattern documentation
- [ ] Organize modular feature system

### Phase 3: AI Integration Tools (Priority: MEDIUM)
- [ ] Add prompt templates
- [ ] Create setup automation scripts
- [ ] Add example projects
- [ ] Create troubleshooting guides

### Phase 4: Developer Experience (Priority: MEDIUM)
- [ ] Add testing scaffolding
- [ ] Create migration guides
- [ ] Add dependency visualizations
- [ ] Implement code generation templates

---

## 📁 Required Directory Structure

Create the following new directories and files:

```
/saas-pilot-root/
├── /docs/
│   ├── ai-instructions.md
│   ├── architecture.md
│   ├── api-documentation.md
│   ├── coding-patterns.md
│   ├── component-map.md
│   ├── database-schema.md
│   ├── deployment-guide.md
│   └── troubleshooting.md
│
├── /prompts/
│   ├── add-new-feature.md
│   ├── create-api-endpoint.md
│   ├── add-payment-plan.md
│   ├── customize-ui-component.md
│   ├── modify-database-schema.md
│   └── integrate-third-party-service.md
│
├── /examples/
│   ├── /ai-newsletter-platform/
│   ├── /form-builder-saas/
│   ├── /waitlist-management/
│   ├── /course-platform/
│   └── /crm-lite/
│
├── /scripts/
│   ├── setup-ai-dev.sh
│   ├── generate-docs.js
│   └── validate-structure.js
│
└── CLAUDE.md (root level)
```

---

## 📄 File 1: `/docs/ai-instructions.md`

Create this file with the following content:

```markdown
# AI Coding Agent Instructions for SaaSPilot

## 🤖 For AI Assistants

This document provides context and instructions for AI coding agents working with the SaaSPilot codebase.

### Quick Context
- **Framework**: Next.js 14+ (App Router)
- **Language**: TypeScript
- **Database**: MongoDB with Prisma ORM
- **Authentication**: NextAuth.js
- **Payments**: Stripe
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
- Database models: PascalCase (`User`, `Subscription`)

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
  const profile = await tx.profile.create({ data: { userId: user.id } })
  return { user, profile }
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
2. Create service file in `/services/service-name.ts`
3. Add initialization in appropriate location
4. Document usage in `/docs/`

### Files You Can Safely Modify
- Any file in `/app` (pages, layouts, components)
- Any file in `/components`
- Server actions in `/actions`
- Services in `/services`
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
```

---

## 📄 File 2: `/docs/architecture.md`

```markdown
# SaaSPilot Architecture

## System Overview

```
┌─────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                         │
│  ┌─────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │   Landing   │  │ Auth Pages   │  │  Dashboard   │       │
│  │   Pages     │  │ (Login/Reg)  │  │    Pages     │       │
│  └─────────────┘  └──────────────┘  └──────────────┘       │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                     NEXT.JS APP ROUTER                       │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  Middleware (Auth, i18n, Rate Limiting)             │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                      API/ACTIONS LAYER                       │
│  ┌───────────┐  ┌───────────┐  ┌────────────┐             │
│  │  Server   │  │  API      │  │  Webhooks  │             │
│  │  Actions  │  │  Routes   │  │  (Stripe)  │             │
│  └───────────┘  └───────────┘  └────────────┘             │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                      SERVICE LAYER                           │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐   │
│  │   Auth   │  │ Payments │  │   Email  │  │  Other   │   │
│  │ (NextAuth)│  │ (Stripe) │  │ (Resend) │  │ Services │   │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘   │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                       DATA LAYER                             │
│  ┌────────────────────────────────────────────────────┐    │
│  │              Prisma ORM → MongoDB                  │    │
│  └────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

## Directory Structure Explained

### `/app` - Next.js App Router
- **Purpose**: All routes and pages
- **Pattern**: File-system based routing
- **Key files**:
  - `page.tsx` - Route page component
  - `layout.tsx` - Shared layout wrapper
  - `loading.tsx` - Loading UI
  - `error.tsx` - Error boundary

### `/actions` - Server Actions
- **Purpose**: Server-side business logic
- **Pattern**: "use server" directive
- **Security**: Runs on server only, secure by default

### `/components` - React Components
- **Purpose**: Reusable UI components
- **Structure**:
  - `/ui` - Base Shadcn components
  - `/shared` - Shared app components
  - Feature-specific components in feature directories

### `/db` - Database
- **Purpose**: Database client and utilities
- **Pattern**: Centralized Prisma instance

### `/services` - External Integrations
- **Purpose**: Third-party service wrappers
- **Examples**: Stripe, SendGrid, Analytics

### `/lib` - Utilities
- **Purpose**: Helper functions and utilities
- **Examples**: Validation, formatting, constants

### `/hooks` - React Hooks
- **Purpose**: Reusable React hooks
- **Pattern**: `use` prefix (e.g., `useUser`)

### `/types` - TypeScript Types
- **Purpose**: Shared type definitions
- **Pattern**: Interface and type declarations

### `/schemas` - Validation Schemas
- **Purpose**: Zod schemas for validation
- **Usage**: Forms, API inputs, database validation

## Data Flow Examples

### User Authentication Flow
```
User → Login Form → Server Action (auth)
  → NextAuth → Prisma → MongoDB
  → Session Created → Redirect to Dashboard
```

### Payment Flow
```
User → Pricing Page → Checkout
  → Stripe Checkout → Payment Success
  → Webhook → Update User Subscription
  → Database Updated → User Notified
```

### Content Creation Flow
```
User → Dashboard Form → Server Action
  → Validate Input → Prisma Create
  → MongoDB → Success Response
  → UI Updated → Confirmation
```

## Key Technologies Integration

### Authentication (NextAuth.js)
- **Config**: `/auth.config.ts` and `/auth.ts`
- **Providers**: Credentials, Google, GitHub
- **Session**: JWT-based
- **Database**: Prisma adapter for MongoDB

### Payments (Stripe)
- **Setup**: `/services/stripe.ts`
- **Webhooks**: `/app/api/webhooks/stripe/route.ts`
- **Events**: subscription.created, subscription.updated, payment.succeeded

### Database (MongoDB + Prisma)
- **Schema**: `/prisma/schema.prisma`
- **Client**: `/db/index.ts`
- **Migrations**: Prisma migrate

### Email (Resend/SendGrid)
- **Templates**: `/email-templates/`
- **Service**: `/services/email.ts`
- **Usage**: Transactional emails, newsletters

## Security Architecture

### Middleware Protection
```typescript
// /middleware.ts
export default auth((req) => {
  // Auth checks
  // Route protection
  // Rate limiting
})
```

### API Security
- JWT verification
- CSRF protection
- Rate limiting
- Input validation (Zod schemas)

### Environment Variables
- Secrets in `.env.local`
- Never commit `.env` files
- Use `.env.example` as template

## Performance Considerations

### Server vs Client Components
- Default to Server Components
- Use `'use client'` only when needed:
  - Event handlers
  - Browser APIs
  - State management (useState, useEffect)

### Database Optimization
- Connection pooling (handled by Prisma)
- Indexes on frequently queried fields
- Select only needed fields

### Caching Strategy
- Static pages where possible
- Dynamic routes with revalidation
- API routes with cache headers

## Deployment Architecture

```
GitHub → Vercel → Production
         ↓
    Preview Deployments
         ↓
    MongoDB Atlas
         ↓
    External Services (Stripe, Resend)
```

## Scaling Considerations

### Horizontal Scaling
- Stateless server actions
- JWT-based sessions (no session store)
- Database connection pooling

### Vertical Scaling
- MongoDB indexes for performance
- CDN for static assets
- Image optimization (Next.js Image)

## Monitoring & Logging

### Error Tracking
- Console.error for development
- Production: Add Sentry or similar

### Analytics
- User events tracking
- Performance monitoring
- Business metrics

## Extension Points

### Adding New Features
1. Create feature directory in `/app`
2. Add server actions in `/actions`
3. Create database models if needed
4. Add UI components
5. Update navigation

### Adding Third-Party Services
1. Add SDK to dependencies
2. Create service wrapper in `/services`
3. Add environment variables
4. Initialize in appropriate location
5. Document usage

### Custom API Routes
1. Create in `/app/api/[route]/route.ts`
2. Implement HTTP methods
3. Add authentication
4. Handle errors
5. Document in API docs
```

---

## 📄 File 3: `/docs/coding-patterns.md`

```markdown
# Coding Patterns & Best Practices

This document outlines the standard patterns used throughout SaaSPilot. Follow these patterns to maintain consistency and enable AI coding agents to understand and extend the codebase effectively.

## Table of Contents
1. [Server Actions](#server-actions)
2. [API Routes](#api-routes)
3. [Database Operations](#database-operations)
4. [Component Patterns](#component-patterns)
5. [Form Handling](#form-handling)
6. [Error Handling](#error-handling)
7. [Type Definitions](#type-definitions)

---

## Server Actions

### Basic Pattern
```typescript
'use server'

import { auth } from '@/auth'
import { db } from '@/db'
import { actionSchema } from '@/schemas/action-schema'
import { revalidatePath } from 'next/cache'

/**
 * @ai-context Performs [specific action description]
 * @ai-safe-to-modify Yes
 * @returns Promise with success/error result
 */
export async function performAction(
  formData: FormData
): Promise<{ success?: boolean; error?: string; data?: any }> {
  // 1. Authenticate
  const session = await auth()
  if (!session?.user?.id) {
    return { error: 'Unauthorized' }
  }

  // 2. Validate input
  const validatedFields = actionSchema.safeParse({
    field1: formData.get('field1'),
    field2: formData.get('field2'),
  })

  if (!validatedFields.success) {
    return { error: 'Invalid fields' }
  }

  const { field1, field2 } = validatedFields.data

  try {
    // 3. Perform database operation
    const result = await db.model.create({
      data: {
        userId: session.user.id,
        field1,
        field2,
      },
    })

    // 4. Revalidate if needed
    revalidatePath('/dashboard')

    // 5. Return success
    return { success: true, data: result }
  } catch (error) {
    console.error('Action failed:', error)
    return { error: 'Something went wrong' }
  }
}
```

### With TypeScript Input (Alternative)
```typescript
'use server'

import { z } from 'zod'

const inputSchema = z.object({
  name: z.string().min(1),
  email: z.string().email(),
})

type ActionInput = z.infer<typeof inputSchema>
type ActionResult = { success?: boolean; error?: string; data?: any }

export async function typedAction(input: ActionInput): Promise<ActionResult> {
  // Same pattern as above
}
```

---

## API Routes

### GET Endpoint Pattern
```typescript
// /app/api/resource/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { auth } from '@/auth'
import { db } from '@/db'

/**
 * @ai-context Fetches [resource description]
 * @ai-auth-required Yes
 */
export async function GET(req: NextRequest) {
  try {
    // 1. Authenticate
    const session = await auth()
    if (!session?.user?.id) {
      return NextResponse.json(
        { error: 'Unauthorized' },
        { status: 401 }
      )
    }

    // 2. Get query parameters
    const searchParams = req.nextUrl.searchParams
    const param = searchParams.get('param')

    // 3. Fetch data
    const data = await db.model.findMany({
      where: { userId: session.user.id },
    })

    // 4. Return response
    return NextResponse.json({ data })
  } catch (error) {
    console.error('API Error:', error)
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    )
  }
}
```

### POST Endpoint Pattern
```typescript
export async function POST(req: NextRequest) {
  try {
    // 1. Authenticate
    const session = await auth()
    if (!session?.user?.id) {
      return NextResponse.json(
        { error: 'Unauthorized' },
        { status: 401 }
      )
    }

    // 2. Parse body
    const body = await req.json()

    // 3. Validate
    const validated = schema.parse(body)

    // 4. Process
    const result = await db.model.create({
      data: {
        ...validated,
        userId: session.user.id,
      },
    })

    // 5. Return
    return NextResponse.json({ data: result }, { status: 201 })
  } catch (error) {
    if (error instanceof z.ZodError) {
      return NextResponse.json(
        { error: 'Validation failed', details: error.errors },
        { status: 400 }
      )
    }
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    )
  }
}
```

---

## Database Operations

### Create Pattern
```typescript
const newRecord = await db.model.create({
  data: {
    field1: value1,
    field2: value2,
    // Relations
    user: {
      connect: { id: userId }
    }
  },
  // Include relations if needed
  include: {
    relatedModel: true
  }
})
```

### Read Patterns
```typescript
// Find one
const record = await db.model.findUnique({
  where: { id: recordId },
  include: { relations: true }
})

// Find many with filtering
const records = await db.model.findMany({
  where: {
    userId,
    status: 'active',
  },
  orderBy: { createdAt: 'desc' },
  take: 10,
  skip: 0,
})

// Find first
const first = await db.model.findFirst({
  where: { email },
})
```

### Update Pattern
```typescript
const updated = await db.model.update({
  where: { id: recordId },
  data: {
    field1: newValue,
    updatedAt: new Date(),
  },
})
```

### Delete Pattern
```typescript
const deleted = await db.model.delete({
  where: { id: recordId },
})
```

### Transaction Pattern
```typescript
const result = await db.$transaction(async (tx) => {
  // Multiple operations
  const user = await tx.user.update({ ... })
  const log = await tx.auditLog.create({ ... })
  
  return { user, log }
})
```

---

## Component Patterns

### Server Component Pattern
```typescript
// Default: Server Component (no 'use client')
import { auth } from '@/auth'
import { db } from '@/db'

/**
 * @ai-context Displays [component purpose]
 * @ai-type Server Component
 */
export default async function ComponentName() {
  // Can directly fetch data
  const session = await auth()
  const data = await db.model.findMany()

  return (
    <div>
      {/* JSX */}
    </div>
  )
}
```

### Client Component Pattern
```typescript
'use client'

import { useState, useEffect } from 'react'

interface Props {
  initialData?: SomeType
}

/**
 * @ai-context Interactive component for [purpose]
 * @ai-type Client Component
 * @ai-state-management useState, custom hooks
 */
export function ComponentName({ initialData }: Props) {
  const [state, setState] = useState(initialData)

  // Effects, handlers, etc.

  return (
    <div>
      {/* Interactive JSX */}
    </div>
  )
}
```

### Form Component Pattern
```typescript
'use client'

import { useFormState, useFormStatus } from 'react-dom'
import { performAction } from '@/actions/action-name'

/**
 * @ai-context Form for [purpose]
 * @ai-action-binding performAction server action
 */
export function FormComponent() {
  const [state, formAction] = useFormState(performAction, null)

  return (
    <form action={formAction}>
      <input name="field1" required />
      <input name="field2" required />
      <SubmitButton />
      {state?.error && <p className="text-red-500">{state.error}</p>}
    </form>
  )
}

function SubmitButton() {
  const { pending } = useFormStatus()
  return (
    <button type="submit" disabled={pending}>
      {pending ? 'Submitting...' : 'Submit'}
    </button>
  )
}
```

---

## Form Handling

### With React Hook Form + Zod
```typescript
'use client'

import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'

const formSchema = z.object({
  name: z.string().min(2, 'Name too short'),
  email: z.string().email('Invalid email'),
})

type FormData = z.infer<typeof formSchema>

export function ZodForm() {
  const form = useForm<FormData>({
    resolver: zodResolver(formSchema),
    defaultValues: {
      name: '',
      email: '',
    },
  })

  async function onSubmit(data: FormData) {
    const result = await performAction(data)
    if (result.error) {
      // Handle error
    }
  }

  return (
    <form onSubmit={form.handleSubmit(onSubmit)}>
      <input {...form.register('name')} />
      {form.formState.errors.name && (
        <p>{form.formState.errors.name.message}</p>
      )}
      {/* More fields */}
    </form>
  )
}
```

---

## Error Handling

### Try-Catch Pattern
```typescript
try {
  const result = await riskyOperation()
  return { success: true, data: result }
} catch (error) {
  console.error('Operation failed:', error)
  
  // Specific error types
  if (error instanceof PrismaClientKnownRequestError) {
    if (error.code === 'P2002') {
      return { error: 'Record already exists' }
    }
  }
  
  // Generic error
  return { error: 'Something went wrong' }
}
```

### Error Boundary (React)
```typescript
'use client'

import { useEffect } from 'react'

/**
 * @ai-context Error boundary for [feature]
 */
export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string }
  reset: () => void
}) {
  useEffect(() => {
    console.error('Error:', error)
  }, [error])

  return (
    <div>
      <h2>Something went wrong!</h2>
      <button onClick={reset}>Try again</button>
    </div>
  )
}
```

---

## Type Definitions

### Model Types (from Prisma)
```typescript
// Generated automatically from Prisma schema
import { User, Post, Subscription } from '@prisma/client'

// With relations
type UserWithProfile = User & {
  profile: Profile | null
}
```

### Custom Types
```typescript
// /types/index.ts

export interface ApiResponse<T> {
  success: boolean
  data?: T
  error?: string
}

export type UserRole = 'user' | 'admin' | 'moderator'

export interface PaginatedResult<T> {
  items: T[]
  total: number
  page: number
  pageSize: number
}
```

### Component Props
```typescript
interface ComponentProps {
  // Required
  id: string
  title: string
  
  // Optional
  subtitle?: string
  className?: string
  
  // Callbacks
  onSubmit?: (data: FormData) => void
  
  // Children
  children?: React.ReactNode
}
```

---

## Naming Conventions

### Files
- Components: `PascalCase.tsx` (e.g., `UserDashboard.tsx`)
- Utilities: `kebab-case.ts` (e.g., `format-date.ts`)
- Actions: `kebab-case.ts` (e.g., `create-user.ts`)
- Types: `PascalCase.ts` or `index.ts` (e.g., `User.ts`)

### Variables
- Constants: `UPPER_SNAKE_CASE` (e.g., `MAX_FILE_SIZE`)
- Functions: `camelCase` (e.g., `getUserById`)
- Components: `PascalCase` (e.g., `UserProfile`)
- Boolean variables: `isX`, `hasX`, `shouldX` (e.g., `isLoading`)

### Database
- Models: `PascalCase` (e.g., `User`, `BlogPost`)
- Fields: `camelCase` (e.g., `createdAt`, `userId`)
- Enum values: `UPPER_SNAKE_CASE` or `camelCase`

---

## Comments & Documentation

### JSDoc for Functions
```typescript
/**
 * Retrieves user data by ID
 * 
 * @param userId - The unique user identifier
 * @param includeProfile - Whether to include profile data
 * @returns Promise resolving to user data or null
 * @throws {PrismaClientKnownRequestError} If database query fails
 * 
 * @ai-context Core user data retrieval function
 * @ai-dependencies Prisma, Auth session
 * @ai-modify-safe Yes - can extend with additional includes
 */
export async function getUserById(
  userId: string,
  includeProfile = false
): Promise<UserWithProfile | null> {
  // Implementation
}
```

### Inline Comments for Complex Logic
```typescript
// Calculate subscription end date based on plan duration
// Monthly plans: +30 days, Annual plans: +365 days
const endDate = plan === 'monthly' 
  ? addDays(new Date(), 30)
  : addYears(new Date(), 1)
```

### AI-Specific Annotations
```typescript
/**
 * @ai-context This handles Stripe webhook events
 * @ai-modify-careful This is security-sensitive code
 * @ai-test-required Yes - changes should be thoroughly tested
 * @ai-dependencies Stripe SDK, User model, Email service
 */
```
```

---

## 📄 File 4: `/docs/api-documentation.md`

```markdown
# API Documentation

## Overview
All API routes are located in `/app/api/` and follow RESTful conventions.

## Authentication
Most endpoints require authentication via NextAuth session.

```typescript
// Headers required
{
  "Cookie": "next-auth.session-token=..."
}
```

## Base URL
- Development: `http://localhost:3000/api`
- Production: `https://your-domain.com/api`

---

## User Endpoints

### GET /api/user/profile
Get current user profile

**Auth Required**: Yes

**Response**:
```json
{
  "data": {
    "id": "user_id",
    "name": "John Doe",
    "email": "john@example.com",
    "image": "https://...",
    "role": "user"
  }
}
```

### PUT /api/user/profile
Update user profile

**Auth Required**: Yes

**Request Body**:
```json
{
  "name": "John Doe",
  "bio": "Developer"
}
```

**Response**:
```json
{
  "success": true,
  "data": { /* updated user */ }
}
```

---

## Subscription Endpoints

### GET /api/subscriptions/current
Get user's current subscription

**Auth Required**: Yes

**Response**:
```json
{
  "data": {
    "id": "sub_id",
    "status": "active",
    "planId": "pro",
    "currentPeriodEnd": "2024-12-31T00:00:00.000Z"
  }
}
```

### POST /api/subscriptions/checkout
Create Stripe checkout session

**Auth Required**: Yes

**Request Body**:
```json
{
  "priceId": "price_xxx",
  "successUrl": "/dashboard",
  "cancelUrl": "/pricing"
}
```

**Response**:
```json
{
  "sessionId": "cs_xxx",
  "url": "https://checkout.stripe.com/..."
}
```

---

## Webhook Endpoints

### POST /api/webhooks/stripe
Handle Stripe webhook events

**Auth Required**: No (Stripe signature verification)

**Events Handled**:
- `checkout.session.completed`
- `customer.subscription.created`
- `customer.subscription.updated`
- `customer.subscription.deleted`
- `invoice.payment_succeeded`
- `invoice.payment_failed`

---

## Error Responses

All errors follow this structure:

```json
{
  "error": "Error message",
  "code": "ERROR_CODE",
  "details": { /* optional */ }
}
```

**Status Codes**:
- `200` - Success
- `201` - Created
- `400` - Bad Request
- `401` - Unauthorized
- `403` - Forbidden
- `404` - Not Found
- `500` - Server Error

---

## Rate Limiting

- Standard: 100 requests per 15 minutes per IP
- Authentication: 5 failed attempts per 15 minutes
- Webhooks: No limit (but signature verified)

---

## Adding New Endpoints

Template for new API routes:

```typescript
// /app/api/new-endpoint/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { auth } from '@/auth'

export async function GET(req: NextRequest) {
  const session = await auth()
  if (!session) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 })
  }
  
  // Implementation
  
  return NextResponse.json({ data })
}
```

Add documentation here after creating new endpoints.
```

---

## 📄 File 5: `/CLAUDE.md` (Root Level)

```markdown
# Building with Claude - Quick Start Guide

Welcome! This guide helps AI coding agents (Claude, Cursor, etc.) quickly understand and work with SaaSPilot.

## 🚀 Quick Setup

1. **Environment Setup**:
```bash
cp .env.example .env.local
# Fill in required variables
```

2. **Install Dependencies**:
```bash
npm install
```

3. **Database Setup**:
```bash
npx prisma generate
npx prisma db push
```

4. **Run Development Server**:
```bash
npm run dev
```

Visit: `http://localhost:3000`

---

## 📚 Essential Reading for AI Agents

Before making changes, read these files IN ORDER:

1. **`/docs/ai-instructions.md`** - Core instructions and conventions
2. **`/docs/architecture.md`** - System architecture overview
3. **`/docs/coding-patterns.md`** - Standard code patterns
4. **`/docs/api-documentation.md`** - API reference

---

## 🎯 Common Tasks

### Adding a New Feature
```bash
# 1. Read the prompt template
# 2. Follow the pattern in /docs/coding-patterns.md
# 3. Create feature structure:
/app/feature-name/
  ├── page.tsx
  ├── components/
  └── layout.tsx

/actions/feature-name/
  └── action.ts
```

See: `/prompts/add-new-feature.md`

### Creating an API Endpoint
See: `/prompts/create-api-endpoint.md`

### Modifying Database Schema
See: `/prompts/modify-database-schema.md`

### Adding a Payment Plan
See: `/prompts/add-payment-plan.md`

### Customizing UI Components
See: `/prompts/customize-ui-component.md`

---

## 🛠️ AI-Friendly Features

✅ **Comprehensive JSDoc comments** on all major functions
✅ **Consistent naming conventions** throughout codebase
✅ **Type-safe** with TypeScript
✅ **Clear folder structure** with purpose-specific directories
✅ **Modular architecture** - easy to extend
✅ **Detailed error handling** patterns
✅ **Ready-to-use examples** in `/examples` directory

---

## 📁 Key Directories

```
/app              → Pages and routes (Next.js App Router)
/actions          → Server actions (business logic)
/components       → React components
  /ui             → Base components (Shadcn)
  /shared         → Shared app components
/db               → Database client
/services         → Third-party integrations
/lib              → Utility functions
/hooks            → React hooks
/types            → TypeScript types
/schemas          → Zod validation schemas
/prisma           → Database schema
/docs             → Documentation (AI instructions)
/prompts          → Task templates for AI agents
/examples         → Complete example projects
```

---

## 🔐 Authentication Flow

```typescript
// Check if user is authenticated
import { auth } from '@/auth'

const session = await auth()
if (!session?.user) {
  // Handle unauthorized
}
```

---

## 💾 Database Queries

```typescript
// Always use Prisma
import { db } from '@/db'

const user = await db.user.findUnique({
  where: { id: userId }
})
```

---

## ⚡ Server Actions

```typescript
'use server'

export async function myAction(formData: FormData) {
  // 1. Auth check
  // 2. Validation
  // 3. Database operation
  // 4. Return result
}
```

---

## 🎨 UI Components

```typescript
// Use Shadcn/ui components
import { Button } from '@/components/ui/button'
import { Card } from '@/components/ui/card'

// Tailwind for styling
<div className="flex items-center gap-4">
  <Button variant="default">Click me</Button>
</div>
```

---

## 🚨 Important Files (Modify with Caution)

- `/auth.ts`, `/auth.config.ts` - Authentication core
- `/middleware.ts` - Request middleware
- `/prisma/schema.prisma` - Database schema (requires migration)
- `/app/api/webhooks/` - Webhook handlers (security-sensitive)

---

## 🧪 Testing Changes

```bash
# Type checking
npm run type-check

# Linting
npm run lint

# Build test
npm run build
```

---

## 📦 Adding Dependencies

```bash
npm install package-name

# Update after adding packages
npx prisma generate  # If DB-related
```

---

## 🐛 Debugging Tips

1. **Check logs**: Console in development
2. **Database**: Use `npx prisma studio` to view data
3. **API testing**: Use Thunder Client or Postman
4. **Type errors**: Run `npm run type-check`

---

## 💡 When Unsure

1. Check `/docs` for specific guidance
2. Look at `/examples` for similar implementations
3. Review `/prompts` for task templates
4. Ask for clarification before making major changes

---

## 🤖 AI Agent Best Practices

**DO**:
✅ Read relevant docs before coding
✅ Follow established patterns
✅ Add comments for complex logic
✅ Use TypeScript types
✅ Test changes locally
✅ Update documentation when adding features

**DON'T**:
❌ Modify core auth/security files without understanding
❌ Skip validation in server actions
❌ Ignore TypeScript errors
❌ Add dependencies without necessity
❌ Commit `.env` files
❌ Break existing patterns without reason

---

## 🔗 Useful Commands

```bash
# Development
npm run dev                    # Start dev server
npm run build                  # Production build
npm run start                  # Start production server

# Database
npx prisma studio              # Visual database browser
npx prisma generate            # Regenerate Prisma client
npx prisma db push             # Push schema changes
npx prisma migrate dev         # Create migration

# Code Quality
npm run lint                   # Run ESLint
npm run type-check             # TypeScript check
```

---

## 📞 Getting Help

- **Documentation**: `/docs` directory
- **Examples**: `/examples` directory
- **Templates**: `/prompts` directory
- **Architecture**: `/docs/architecture.md`
- **Patterns**: `/docs/coding-patterns.md`

---

## 🎯 Your First Task

Try this:

1. Read `/docs/ai-instructions.md`
2. Look at `/examples` for reference
3. Choose a task from `/prompts`
4. Follow the pattern
5. Test your changes

**Good luck coding! 🚀**
```

---

## 📄 File 6: `/prompts/add-new-feature.md`

```markdown
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
```

---

## 📄 File 7: `/prompts/create-api-endpoint.md`

```markdown
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
```

---

## 📄 File 8: `/prompts/modify-database-schema.md`

```markdown
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
```

---

## 📄 File 9: `/docs/troubleshooting.md`

```markdown
# Troubleshooting Guide

Common issues and solutions when working with SaaSPilot.

## Database Issues

### Error: "Prisma Client is not generated"
```bash
# Solution: Generate Prisma client
npx prisma generate
```

### Error: Database connection failed
- Check DATABASE_URL in `.env.local`
- Verify MongoDB is running
- Check network/firewall settings
- Test connection: `npx prisma db pull`

### Error: Migration failed
```bash
# Reset database (WARNING: deletes all data)
npx prisma migrate reset

# Or push schema without migration
npx prisma db push
```

---

## Authentication Issues

### Error: "NextAuth session not found"
- Check NEXTAUTH_SECRET is set
- Verify NEXTAUTH_URL matches your domain
- Clear browser cookies
- Check middleware.ts configuration

### Social login not working
- Verify OAuth credentials in `.env.local`
- Check redirect URLs in OAuth provider settings
- Google: http://localhost:3000/api/auth/callback/google
- GitHub: http://localhost:3000/api/auth/callback/github

---

## Payment Issues

### Stripe webhook not receiving events
- Check webhook secret (STRIPE_WEBHOOK_SECRET)
- Verify endpoint URL in Stripe dashboard
- Use Stripe CLI for local testing:
```bash
stripe listen --forward-to localhost:3000/api/webhooks/stripe
```

### Subscription status not updating
- Check webhook is properly configured
- Verify handleStripeWebhook function
- Check Stripe dashboard for event history
- Look for errors in webhook logs

---

## Build Errors

### TypeScript errors
```bash
# Check for errors
npm run type-check

# Common fixes:
# 1. Regenerate Prisma types
npx prisma generate

# 2. Update dependencies
npm install

# 3. Clear Next.js cache
rm -rf .next
```

### Module not found
```bash
# Clear cache and reinstall
rm -rf node_modules package-lock.json
npm install
```

---

## Email Issues

### Emails not sending (Resend)
- Verify RESEND_API_KEY
- Check "from" email is verified domain
- Check email template path
- Review Resend dashboard for errors

### Emails not sending (SendGrid)
- Verify SENDGRID_API_KEY
- Check sender email is verified
- Review SendGrid activity feed
- Check email content for spam triggers

---

## Development Issues

### Port already in use
```bash
# Kill process on port 3000
# Mac/Linux:
lsof -ti:3000 | xargs kill -9

# Windows:
netstat -ano | findstr :3000
taskkill /PID [PID] /F
```

### Hot reload not working
- Check file watcher limits (Linux)
- Restart dev server: `npm run dev`
- Clear `.next` folder: `rm -rf .next`

### Environment variables not loading
- Restart dev server after changing `.env.local`
- Check variable names (no NEXT_PUBLIC_ for server-side)
- Verify `.env.local` exists and not in .gitignore

---

## Performance Issues

### Slow page loads
- Check database query efficiency
- Add indexes to frequently queried fields
- Use `select` to limit returned fields
- Implement pagination for large datasets

### Build taking too long
```bash
# Clear cache
rm -rf .next

# Check for large dependencies
npx bundle-analyzer
```

---

## Common Error Messages

### "Cannot read property 'id' of null"
- User session is null
- Add authentication check: `if (!session?.user) return`

### "Unique constraint failed"
- Trying to create duplicate record
- Check for existing record first
- Handle P2002 Prisma error code

### "Invalid CSRF token"
- Middleware configuration issue
- Check NEXTAUTH_URL setting
- Clear cookies and try again

### "Rate limit exceeded"
- Too many requests to API
- Implement exponential backoff
- Check rate limit configuration

---

## Debugging Tips

### Enable verbose logging
```typescript
// In server actions
console.log('Debug:', { variable1, variable2 })

// In Prisma queries
prisma.$on('query', (e) => {
  console.log('Query:', e.query)
  console.log('Duration:', e.duration, 'ms')
})
```

### Use Prisma Studio
```bash
npx prisma studio
# Opens visual database browser at localhost:5555
```

### Check Next.js build output
```bash
npm run build
# Look for errors and warnings
```

### Browser DevTools
- Network tab: Check API calls
- Console: Check for errors
- Application tab: Check cookies/local storage

---

## Getting More Help

1. Check documentation in `/docs`
2. Review examples in `/examples`
3. Search GitHub issues
4. Check Next.js documentation
5. Check Prisma documentation
6. Review Stripe/Resend documentation

---

## Reporting Bugs

When reporting issues, include:
1. Error message (full stack trace)
2. Steps to reproduce
3. Expected vs actual behavior
4. Environment (Node version, OS)
5. Relevant code snippets
6. Screenshots if applicable
```

---

## Implementation Priority

### Phase 1 (Start Here - Essential for AI Coding)
1. Create `/docs` directory
2. Add `ai-instructions.md`
3. Add `architecture.md`
4. Add `coding-patterns.md`
5. Create `/CLAUDE.md` at root
6. Add JSDoc comments to main files

### Phase 2 (High Value)
7. Create `/prompts` directory with templates
8. Add `api-documentation.md`
9. Create setup automation script
10. Add troubleshooting.md

### Phase 3 (Nice to Have)
11. Create `/examples` with sample projects
12. Add component relationship maps
13. Create dependency visualizations
14. Add more prompt templates

---

## Success Criteria

After implementation, an AI coding agent should be able to:

✅ Understand the codebase structure in < 5 minutes
✅ Add a new feature following established patterns
✅ Create API endpoints without breaking conventions
✅ Modify database schema safely
✅ Find relevant examples quickly
✅ Troubleshoot common issues independently
✅ Generate consistent, maintainable code

---

## Notes for Implementation

1. **Start with documentation** - This is the foundation
2. **Be consistent** - Follow the patterns you establish
3. **Add examples** - Real code examples are more valuable than descriptions
4. **Keep it updated** - Document as you add features
5. **Test with AI** - Actually use Claude/Cursor to verify the docs work

---

## Additional Recommendations

### For Video Series
- Add a `/examples` folder with 5-10 complete mini-SaaS projects
- Each example should be fully documented
- Include a README showing what was built and how long it took

### For Marketing
- Create a comparison document: "SaaSPilot vs Other Boilerplates"
- Highlight the AI-coding-ready aspect
- Show time-to-market metrics

### For Pro Version
- Keep advanced features (Admin Dashboard, Blog, Analytics) in Pro
- Offer discount codes to video viewers
- Create upgrade path documentation

---

## Questions for You

Before implementing, consider:

1. **Which framework version are you on?** (Next.js 14/15?)
2. **Do you have existing documentation** that should be preserved?
3. **Are there custom features** not in standard Next.js that need special docs?
4. **What's your MongoDB setup?** (Atlas, local, Docker?)
5. **Priority timeline?** (Which phases to implement first?)

---

## Implementation Checklist

Use this checklist to track progress:

### Documentation
- [ ] Create `/docs` directory
- [ ] Add `ai-instructions.md`
- [ ] Add `architecture.md`
- [ ] Add `coding-patterns.md`
- [ ] Add `api-documentation.md`
- [ ] Add `troubleshooting.md`

### Root Level Files
- [ ] Create `CLAUDE.md`

### Prompts Directory
- [ ] Create `/prompts` directory
- [ ] Add `add-new-feature.md`
- [ ] Add `create-api-endpoint.md`
- [ ] Add `modify-database-schema.md`
- [ ] Add `add-payment-plan.md`
- [ ] Add `customize-ui-component.md`

### Code Enhancements
- [ ] Add JSDoc comments to server actions
- [ ] Add JSDoc comments to API routes
- [ ] Add JSDoc comments to major components
- [ ] Add file headers with AI context

### Examples (Optional)
- [ ] Create `/examples` directory
- [ ] Add example project 1
- [ ] Add example project 2
- [ ] Add example project 3

### Scripts (Optional)
- [ ] Create `/scripts` directory
- [ ] Add `setup-ai-dev.sh`
- [ ] Add `generate-docs.js`
- [ ] Add `validate-structure.js`

---

**Ready to implement? Start with Phase 1 and work through the checklist systematically.**
