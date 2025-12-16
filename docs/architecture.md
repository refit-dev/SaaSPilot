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
  - `/auth` - Authentication components
  - Feature-specific components in feature directories

### `/db` - Database
- **Purpose**: Database client and utilities
- **Pattern**: Centralized Prisma instance

### `/lib` - Utilities & Services
- **Purpose**: Helper functions, utilities, and external integrations
- **Examples**: Stripe, Email, Credits, Authentication helpers

### `/hooks` - React Hooks
- **Purpose**: Reusable React hooks
- **Pattern**: `use` prefix (e.g., `useCurrentUser`)

### `/types` - TypeScript Types
- **Purpose**: Shared type definitions
- **Pattern**: Interface and type declarations

### `/schemas` - Validation Schemas
- **Purpose**: Zod schemas for validation
- **Usage**: Forms, API inputs, database validation

## Data Flow Examples

### User Authentication Flow
```
User → Login Form → Server Action (login)
  → NextAuth → Prisma → MongoDB
  → Session Created → Redirect to Dashboard
```

### Credit Purchase Flow
```
User → Pricing Page → Checkout
  → Stripe Checkout → Payment Success
  → Webhook → Update User Credits
  → Database Updated → User Notified
```

### Protected Action Flow
```
User → Dashboard Form → Server Action
  → Auth Check → Validate Input → Prisma Query
  → MongoDB → Success Response
  → UI Updated → Confirmation
```

## Key Technologies Integration

### Authentication (NextAuth.js v5)
- **Config**: `/auth.config.ts` and `/auth.ts`
- **Providers**: Credentials, Google, GitHub
- **Session**: JWT-based
- **Database**: Prisma adapter for MongoDB

### Payments (Stripe)
- **Setup**: `/lib/stripe.ts`
- **Config**: `/config/stripe.ts`
- **Webhooks**: `/app/api/webhooks/stripe/route.ts`
- **Events**: checkout.session.completed, payment_intent.succeeded
- **Model**: Credit-based (not subscription)

### Database (MongoDB + Prisma)
- **Schema**: `/prisma/schema.prisma`
- **Client**: `/db/index.ts`
- **Migrations**: Prisma migrate or db push
- **Models**: User, Account, Credit, Purchase, Verification tokens

### Email (Resend/SendGrid)
- **Templates**: `/email-templates/`
- **Service**: `/lib/mail.ts`
- **Usage**: Verification, password reset, transactional emails

### Internationalization (next-intl)
- **Config**: `/i18n/config.ts`
- **Languages**: English, German, Arabic
- **Translations**: `/translations/*.json`
- **RTL Support**: Automatic detection and handling

## Security Architecture

### Middleware Protection
```typescript
// /middleware.ts
export default auth((req) => {
  // Auth checks
  // Route protection
  // Locale handling
})
```

### API Security
- JWT verification via NextAuth
- CSRF protection (built-in)
- Input validation (Zod schemas)
- Rate limiting (ready to add)

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
  - Hooks usage

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
2. Create service wrapper in `/lib`
3. Add environment variables
4. Initialize in appropriate location
5. Document usage

### Custom API Routes
1. Create in `/app/api/[route]/route.ts`
2. Implement HTTP methods
3. Add authentication
4. Handle errors
5. Document in API docs

## Credit System Architecture

### How Credits Work
1. **Initial Credits**: New users receive credits on registration (configurable via `INITIAL_CREDITS_FOR_NEW`)
2. **Purchase Credits**: Users buy credit packs via Stripe
3. **Spend Credits**: Features deduct credits via `spendCredits()` function
4. **Track Usage**: Purchase history stored for analytics

### Credit Flow
```
New User Registration → Initial Credits Added
User Selects Plan → Stripe Checkout
Payment Success → Webhook Triggers
Credits Added to Balance → Purchase Record Created
User Uses Feature → Credits Deducted
Balance Updated → Transaction Logged
```

### Files Involved
- `/lib/credits.ts` - Credit management functions
- `/context/CreditsContext.tsx` - Client-side credit state
- `/actions/createStripeSession.ts` - Checkout creation
- `/app/api/webhooks/stripe/route.ts` - Payment processing
- `/config/stripe.ts` - Pricing configuration
