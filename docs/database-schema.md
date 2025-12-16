# Database Schema Documentation

## Overview
SaaSPilot uses MongoDB with Prisma ORM. The database schema is defined in `/prisma/schema.prisma`.

## Models

### User
Main user account model with authentication and billing information.

```prisma
model User {
  id                      String   @id @default(auto()) @map("_id") @db.ObjectId
  name                    String?
  email                   String?  @unique
  emailVerified           DateTime?
  image                   String?
  password                String?
  role                    UserRole @default(USER)
  username                String   @unique
  isTwoFactorEnabled      Boolean  @default(false)

  // Stripe billing
  stripeCustomerId            String?
  currentStripeSubscriptionId String?

  // Relations
  accounts                Account[]
  twoFactorConfirmation   TwoFactorConfirmation?
  credits                 Credit?
  purchases               Purchase[]
}
```

**Fields**:
- `id` - Unique MongoDB ObjectId
- `name` - User's display name
- `email` - Unique email address
- `emailVerified` - Email verification timestamp
- `image` - Profile image URL
- `password` - Hashed password (for credentials auth)
- `role` - User role (USER or ADMIN)
- `username` - Unique username
- `isTwoFactorEnabled` - 2FA status
- `stripeCustomerId` - Stripe customer ID
- `currentStripeSubscriptionId` - Active subscription (future use)

**Relations**:
- One-to-Many: Account (OAuth accounts)
- One-to-One: Credit (credit balance)
- One-to-Many: Purchase (purchase history)
- One-to-One: TwoFactorConfirmation

---

### Account
OAuth provider accounts linked to users.

```prisma
model Account {
  id                String  @id @default(auto()) @map("_id") @db.ObjectId
  userId            String  @db.ObjectId
  type              String
  provider          String  // "google", "github", "credentials"
  providerAccountId String
  refresh_token     String?
  access_token      String?
  expires_at        Int?
  token_type        String?
  scope             String?
  id_token          String?
  session_state     String?

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@unique([provider, providerAccountId])
}
```

**Purpose**: Stores OAuth provider information (Google, GitHub) and credentials.

**Unique Constraint**: One account per provider per user.

---

### Credit
User credit balance tracking.

```prisma
model Credit {
  id        String   @id @default(auto()) @map("_id") @db.ObjectId
  userId    String   @db.ObjectId @unique
  balance   Int      @default(0)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  user User @relation(fields: [userId], references: [id])
}
```

**Fields**:
- `balance` - Current credit balance
- `userId` - User who owns these credits

**Usage**:
- Credits added on purchase via Stripe
- Credits deducted when using features
- One credit record per user

---

### Purchase
Purchase history for audit and analytics.

```prisma
model Purchase {
  id                     String   @id @default(auto()) @map("_id") @db.ObjectId
  userId                 String   @db.ObjectId @unique
  stripeId               String   @unique
  stripeSubscriptionId   String?
  stripePriceId          String?
  amount                 Float
  creditsAdded           Int
  packageName            String
  createdAt              DateTime @default(now())

  user User @relation(fields: [userId], references: [id])
}
```

**Fields**:
- `stripeId` - Stripe session or payment intent ID (unique)
- `amount` - Cost in dollars
- `creditsAdded` - Number of credits added
- `packageName` - "Free", "Starter", "Pro"

**Purpose**: Track all purchases for analytics and support.

---

### VerificationToken
Email verification tokens.

```prisma
model VerificationToken {
  id      String   @id @default(auto()) @map("_id") @db.ObjectId
  email   String
  token   String   @unique
  expires DateTime

  @@unique([email, token])
}
```

**Purpose**: One-time tokens for email verification.

**Lifecycle**:
1. Created on registration
2. Sent via email
3. Validated on click
4. Deleted after use

---

### PasswordResetToken
Password reset tokens.

```prisma
model PasswordResetToken {
  id      String   @id @default(auto()) @map("_id") @db.ObjectId
  email   String
  token   String   @unique
  expires DateTime

  @@unique([email, token])
}
```

**Purpose**: One-time tokens for password reset.

**Lifecycle**:
1. Created on "Forgot Password"
2. Sent via email
3. Validated on reset
4. Deleted after use or expiration

---

### TwoFactorToken
Two-factor authentication codes.

```prisma
model TwoFactorToken {
  id      String   @id @default(auto()) @map("_id") @db.ObjectId
  email   String
  token   String   @unique
  expires DateTime

  @@unique([email, token])
}
```

**Purpose**: Temporary 2FA codes for login.

**Lifecycle**:
1. Generated on login (if 2FA enabled)
2. Sent via email
3. Validated within expiry time
4. Deleted after successful validation

---

### TwoFactorConfirmation
Two-factor authentication confirmation state.

```prisma
model TwoFactorConfirmation {
  id     String @id @default(auto()) @map("_id") @db.ObjectId
  userId String @db.ObjectId

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@unique([userId])
}
```

**Purpose**: Tracks successful 2FA completion for current session.

---

## Enums

### UserRole
```prisma
enum UserRole {
  ADMIN
  USER
}
```

**Values**:
- `USER` - Default role for regular users
- `ADMIN` - Admin role (set via ADMIN_EMAILS env var)

**Usage**: Role-based access control for admin features.

---

## Relationships Diagram

```
User
├── accounts (Account[]) - OAuth providers
├── credits (Credit?) - Credit balance
├── purchases (Purchase[]) - Purchase history
└── twoFactorConfirmation (TwoFactorConfirmation?) - 2FA state

Account → User (many-to-one)
Credit → User (one-to-one)
Purchase → User (many-to-one)
TwoFactorConfirmation → User (one-to-one)

Independent Models:
- VerificationToken
- PasswordResetToken
- TwoFactorToken
```

---

## Indexes

Current indexes (implicit from schema):
- `User.email` - Unique index
- `User.username` - Unique index
- `Account.[provider, providerAccountId]` - Composite unique index
- `Credit.userId` - Unique index
- `Purchase.userId` - Unique index
- `Purchase.stripeId` - Unique index

**Recommended Additional Indexes** (for performance):
```prisma
model User {
  @@index([role])
  @@index([createdAt])
}

model Purchase {
  @@index([createdAt])
  @@index([userId, createdAt])
}
```

---

## Common Queries

### Get User with Credits
```typescript
const user = await db.user.findUnique({
  where: { id: userId },
  include: { credits: true }
})
```

### Get User Purchase History
```typescript
const purchases = await db.purchase.findMany({
  where: { userId },
  orderBy: { createdAt: 'desc' }
})
```

### Check Email Verification Token
```typescript
const token = await db.verificationToken.findUnique({
  where: { token: tokenString }
})

if (!token || token.expires < new Date()) {
  // Invalid or expired
}
```

### Get User with All Relations
```typescript
const user = await db.user.findUnique({
  where: { id: userId },
  include: {
    accounts: true,
    credits: true,
    purchases: true,
    twoFactorConfirmation: true,
  }
})
```

---

## Database Operations

### Adding New Model

1. Update `prisma/schema.prisma`
2. Format: `npx prisma format`
3. Generate client: `npx prisma generate`
4. Push to DB: `npx prisma db push`

### Modifying Existing Model

1. Update schema
2. Run `npx prisma format`
3. Run `npx prisma generate`
4. Push changes: `npx prisma db push`
5. Update affected TypeScript code

### Migrations (Production)

```bash
# Create migration
npx prisma migrate dev --name migration_name

# Apply migration
npx prisma migrate deploy
```

---

## Best Practices

### Do's
✅ Always use Prisma client for queries
✅ Use transactions for related operations
✅ Include only needed fields with `select`
✅ Add indexes for frequently queried fields
✅ Use `onDelete: Cascade` for dependent records

### Don'ts
❌ Don't use raw queries unless necessary
❌ Don't forget to regenerate client after schema changes
❌ Don't store sensitive data unencrypted
❌ Don't create circular dependencies
❌ Don't skip validation before database operations

---

## Credit System Implementation

### Initial Credits
```typescript
// On user registration
await db.credit.create({
  data: {
    userId: newUser.id,
    balance: parseInt(process.env.INITIAL_CREDITS_FOR_NEW || '20')
  }
})
```

### Add Credits (Purchase)
```typescript
await db.credit.update({
  where: { userId },
  data: {
    balance: { increment: creditsToAdd }
  }
})
```

### Spend Credits
```typescript
await db.credit.update({
  where: { userId },
  data: {
    balance: { decrement: creditCost }
  }
})
```

### Check Balance
```typescript
const credit = await db.credit.findUnique({
  where: { userId }
})

if (!credit || credit.balance < requiredAmount) {
  throw new Error('Insufficient credits')
}
```

---

## Migration Guide

### From Subscription to Credits
If migrating from subscription model:

1. Keep `currentStripeSubscriptionId` for backward compatibility
2. Focus on credit-based billing
3. Convert existing subscriptions to credit packages

### Adding New Payment Plans
Update `/config/stripe.ts` with new plans, no schema changes needed.

---

## Troubleshooting

### Schema not syncing
```bash
npx prisma generate
npx prisma db push
```

### Type errors after schema change
```bash
rm -rf node_modules/.prisma
npx prisma generate
```

### Connection issues
- Verify DATABASE_URL in `.env.local`
- Check MongoDB is accessible
- Test with `npx prisma db pull`
