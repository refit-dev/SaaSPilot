# Component Map & Relationships

This document maps out the component hierarchy and relationships in SaaSPilot.

## Component Categories

### 1. Layout Components
Located in `/app` and `/components`

#### RootLayout
- **Location**: `/app/layout.tsx`
- **Type**: Server Component
- **Purpose**: Root layout with providers
- **Children**: All pages
- **Providers**:
  - SessionProvider (NextAuth)
  - ThemeProvider (next-themes)
  - CreditsProvider (Credit context)
  - IntlProvider (next-intl)

---

### 2. Authentication Components
Located in `/components/auth`

#### LoginForm
- **Location**: `/components/auth/login-form.tsx`
- **Type**: Client Component
- **Dependencies**:
  - `login` server action
  - React Hook Form
  - Zod validation
- **Features**: Email/password login, OAuth buttons
- **Used in**: `/app/auth/login/page.tsx`

#### RegisterForm
- **Location**: `/components/auth/register-form.tsx`
- **Type**: Client Component
- **Dependencies**:
  - `register` server action
  - React Hook Form
  - Zod validation
- **Features**: User registration, email verification
- **Used in**: `/app/auth/register/page.tsx`

#### NewPasswordForm
- **Location**: `/components/auth/new-password-form.tsx`
- **Type**: Client Component
- **Purpose**: Password reset form
- **Dependencies**: `newPassword` server action

#### ResetForm
- **Location**: `/components/auth/reset-form.tsx`
- **Type**: Client Component
- **Purpose**: Request password reset
- **Dependencies**: `reset` server action

#### ErrorCard
- **Location**: `/components/auth/error-card.tsx`
- **Type**: Client Component
- **Purpose**: Display auth errors
- **Used in**: `/app/auth/error/page.tsx`

#### Social
- **Location**: `/components/auth/social.tsx`
- **Type**: Client Component
- **Purpose**: OAuth login buttons (Google, GitHub)
- **Dependencies**: NextAuth signIn

---

### 3. UI Components (Shadcn)
Located in `/components/ui`

Base UI components built on Radix UI:
- `button.tsx` - Button component
- `card.tsx` - Card container
- `dialog.tsx` - Modal dialog
- `dropdown-menu.tsx` - Dropdown menu
- `form.tsx` - Form components
- `input.tsx` - Text input
- `label.tsx` - Form label
- `select.tsx` - Select dropdown
- `separator.tsx` - Divider
- `sheet.tsx` - Side panel
- `switch.tsx` - Toggle switch
- `table.tsx` - Data table
- `tabs.tsx` - Tab navigation
- `toast.tsx` - Notification toast
- And more...

**Usage**: Import from `@/components/ui/[component]`

---

### 4. Navigation Components

#### Navbar
- **Location**: `/components/Navbar.tsx`
- **Type**: Client Component
- **Purpose**: Main navigation bar
- **Features**:
  - Logo and branding
  - Language switcher
  - User menu
  - Mobile responsive
- **Used in**: Marketing pages

#### DashboardNav
- **Location**: `/components/Dashboard.tsx` (or similar)
- **Type**: Client Component
- **Purpose**: Dashboard navigation
- **Used in**: Protected dashboard pages

---

### 5. Marketing Components
Located in `/components` and `/app/(marketing)`

#### Hero
- **Location**: `/components/Hero.tsx` (or in page)
- **Type**: Server/Client Component
- **Purpose**: Landing page hero section
- **Features**: CTA, animations

#### Features
- **Type**: Server Component
- **Purpose**: Feature showcase
- **Used in**: Landing page

#### Testimonials
- **Location**: `/components/Testimonials/`
- **Purpose**: Customer testimonials
- **Used in**: Landing, About pages

#### Pricing
- **Purpose**: Pricing cards/table
- **Features**:
  - Display credit packages
  - Stripe checkout integration
- **Used in**: Pricing page

---

### 6. Dashboard Components
Located in `/app/(protected)/dashboard`

#### CreditBalance
- **Type**: Client Component
- **Purpose**: Display user's credit balance
- **Dependencies**: useCredits hook
- **Used in**: Dashboard pages

#### PurchaseHistory
- **Type**: Server Component
- **Purpose**: Display purchase history
- **Data Source**: Purchase model via Prisma
- **Used in**: Billing page

#### SettingsForm
- **Type**: Client Component
- **Purpose**: User settings management
- **Dependencies**: `updateSettings` server action
- **Used in**: Settings page

---

### 7. Common/Shared Components
Located in `/components/Common`

#### LocaleSwitcher
- **Type**: Client Component
- **Purpose**: Language selection dropdown
- **Languages**: English, German, Arabic
- **Used in**: Navbar, Footer

#### ThemeToggle
- **Type**: Client Component
- **Purpose**: Dark/light mode toggle
- **Dependencies**: next-themes
- **Used in**: Navbar

#### Footer
- **Type**: Server Component
- **Purpose**: Site footer
- **Used in**: All marketing pages

---

## Component Hierarchy

```
App Root
├── RootLayout (Providers)
│   ├── (marketing)
│   │   ├── Home Page
│   │   │   ├── Navbar
│   │   │   ├── Hero
│   │   │   ├── Features
│   │   │   ├── Testimonials
│   │   │   ├── Pricing
│   │   │   └── Footer
│   │   ├── About Page
│   │   ├── Contact Page
│   │   └── Pricing Page
│   │
│   ├── (auth)
│   │   ├── Login Page
│   │   │   └── LoginForm
│   │   │       ├── Social (OAuth)
│   │   │       └── UI Components
│   │   ├── Register Page
│   │   │   └── RegisterForm
│   │   ├── Reset Page
│   │   │   └── ResetForm
│   │   ├── New Password Page
│   │   │   └── NewPasswordForm
│   │   └── Error Page
│   │       └── ErrorCard
│   │
│   └── (protected)
│       └── Dashboard
│           ├── DashboardNav
│           ├── Dashboard Page
│           │   ├── CreditBalance
│           │   └── Dashboard Stats
│           ├── Billing Page
│           │   ├── CreditBalance
│           │   ├── Pricing Cards
│           │   └── PurchaseHistory
│           └── Settings Page
│               └── SettingsForm
```

---

## Data Flow Patterns

### Authentication Flow
```
LoginForm → login action → NextAuth → Session → Redirect
```

### Credit Purchase Flow
```
Pricing Card → createStripeSession action → Stripe Checkout
→ Payment Success → Webhook → Update Credits → Dashboard
```

### Settings Update Flow
```
SettingsForm → updateSettings action → Prisma → Database
→ Revalidate → Updated UI
```

---

## Component Communication

### Props Passing (Parent → Child)
```typescript
<ChildComponent prop1="value" prop2={data} />
```

### Server Actions (Child → Server)
```typescript
const result = await serverAction(data)
```

### Context (Global State)
```typescript
const { credits } = useCredits()
```

### Hooks (Component Logic)
```typescript
const user = useCurrentUser()
const session = useSession()
```

---

## Key Hooks

### useCurrentUser
- **Location**: `/hooks/use-current-user.ts`
- **Purpose**: Get current user on client
- **Returns**: User object or undefined
- **Usage**: Client components needing user data

### useCurrentRole
- **Location**: `/hooks/use-current-role.ts`
- **Purpose**: Get current user role
- **Returns**: UserRole or undefined
- **Usage**: Role-based UI rendering

### useCredits
- **Location**: `/context/CreditsContext.tsx`
- **Purpose**: Access and manage credits
- **Returns**: `{ credits, addCredits, spendCredits }`
- **Usage**: Components showing/using credits

---

## Server vs Client Components

### Server Components (Default)
- Landing pages
- Dashboard pages (data fetching)
- Static content
- SEO-important pages

### Client Components ('use client')
- Forms with state
- Interactive UI
- Event handlers
- Browser APIs
- Hooks usage

---

## Component Best Practices

### When Creating New Components

1. **Decide Server vs Client**
   - Default to Server Component
   - Use Client only if needed

2. **Naming Convention**
   - PascalCase for components
   - Descriptive names (e.g., `UserProfileCard`)

3. **File Organization**
   - Group related components
   - Co-locate with features when specific

4. **Props Interface**
   ```typescript
   interface ComponentProps {
     required: string
     optional?: number
     children?: React.ReactNode
   }
   ```

5. **Documentation**
   ```typescript
   /**
    * @ai-context Component purpose
    * @ai-dependencies List dependencies
    */
   ```

---

## Common Patterns

### Form Component Pattern
```typescript
'use client'

export function MyForm() {
  const [state, formAction] = useFormState(serverAction, null)

  return (
    <form action={formAction}>
      {/* form fields */}
    </form>
  )
}
```

### Data Display Pattern
```typescript
// Server Component
export default async function DataPage() {
  const data = await db.model.findMany()

  return <DataList data={data} />
}

// Client Component
'use client'
export function DataList({ data }: { data: Item[] }) {
  // Interactive rendering
}
```

### Protected Component Pattern
```typescript
import { auth } from '@/auth'

export default async function ProtectedPage() {
  const session = await auth()

  if (!session) {
    redirect('/auth/login')
  }

  return <ProtectedContent />
}
```

---

## Styling Patterns

### Tailwind Classes
```typescript
<div className="flex items-center gap-4 rounded-lg border p-6">
  {/* content */}
</div>
```

### Responsive Design
```typescript
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3">
  {/* responsive grid */}
</div>
```

### Dark Mode
```typescript
<div className="bg-white dark:bg-gray-900 text-black dark:text-white">
  {/* theme-aware */}
</div>
```

---

## Adding New Components

### Checklist
1. [ ] Determine if Server or Client component
2. [ ] Create in appropriate directory
3. [ ] Define Props interface
4. [ ] Add TypeScript types
5. [ ] Implement component logic
6. [ ] Add proper documentation
7. [ ] Test rendering and functionality
8. [ ] Update this component map if significant

### Example Structure
```typescript
// /components/features/MyComponent.tsx

'use client' // if needed

import { ComponentProps } from './types'

/**
 * @ai-context Brief description
 * @ai-dependencies List deps
 * @ai-modify-safe Yes/No
 */
export function MyComponent({ prop1, prop2 }: ComponentProps) {
  // Implementation

  return (
    <div>
      {/* JSX */}
    </div>
  )
}
```

---

## Related Documentation
- [Architecture](architecture.md) - System architecture
- [Coding Patterns](coding-patterns.md) - Code patterns
- [Database Schema](database-schema.md) - Data models
