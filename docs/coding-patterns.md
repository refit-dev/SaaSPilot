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
import { User, Credit, Purchase } from '@prisma/client'

// With relations
type UserWithCredits = User & {
  credits: Credit | null
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

export type UserRole = 'USER' | 'ADMIN'

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
- Models: `PascalCase` (e.g., `User`, `Credit`)
- Fields: `camelCase` (e.g., `createdAt`, `userId`)
- Enum values: `UPPER_SNAKE_CASE` or `PascalCase`

---

## Comments & Documentation

### JSDoc for Functions
```typescript
/**
 * Retrieves user data by ID
 *
 * @param userId - The unique user identifier
 * @param includeCredits - Whether to include credit data
 * @returns Promise resolving to user data or null
 * @throws {PrismaClientKnownRequestError} If database query fails
 *
 * @ai-context Core user data retrieval function
 * @ai-dependencies Prisma, Auth session
 * @ai-modify-safe Yes - can extend with additional includes
 */
export async function getUserById(
  userId: string,
  includeCredits = false
): Promise<UserWithCredits | null> {
  // Implementation
}
```

### Inline Comments for Complex Logic
```typescript
// Calculate credit balance after purchase
// Stripe amount is in cents, convert to credits (100 cents = 1 credit)
const creditsToAdd = stripeAmount / 100
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

---

## Credit Management Pattern

### Adding Credits
```typescript
import { addCredits } from '@/lib/credits'

// Add credits after successful payment
await addCredits(userId, amount, 'PURCHASE', purchaseId)
```

### Spending Credits
```typescript
import { spendCredits } from '@/lib/credits'

// Deduct credits when user uses a feature
const result = await spendCredits(userId, cost)
if (!result.success) {
  return { error: 'Insufficient credits' }
}
```

### Checking Credit Balance
```typescript
import { db } from '@/db'

const creditRecord = await db.credit.findUnique({
  where: { userId }
})

if (!creditRecord || creditRecord.balance < requiredAmount) {
  return { error: 'Insufficient credits' }
}
```
