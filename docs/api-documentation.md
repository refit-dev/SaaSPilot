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

## Authentication Endpoints

### POST /api/auth/[...nextauth]
NextAuth.js authentication endpoint

**Auth Required**: No

**Handles**:
- `POST /api/auth/signin` - Sign in
- `POST /api/auth/signout` - Sign out
- `POST /api/auth/callback/*` - OAuth callbacks
- `GET /api/auth/session` - Get session
- `GET /api/auth/csrf` - Get CSRF token

**Provider Callbacks**:
- `/api/auth/callback/google` - Google OAuth
- `/api/auth/callback/github` - GitHub OAuth
- `/api/auth/callback/credentials` - Email/Password

---

## Webhook Endpoints

### POST /api/webhooks/stripe
Handle Stripe webhook events

**Auth Required**: No (Stripe signature verification)

**Events Handled**:
- `checkout.session.completed` - Payment completed
- `payment_intent.succeeded` - Payment successful
- `payment_intent.failed` - Payment failed

**Request Headers**:
```
stripe-signature: <signature>
```

**Webhook Flow**:
1. Verify Stripe signature
2. Parse event
3. Handle event type
4. Update database (add credits, create purchase record)
5. Send confirmation email
6. Return 200 OK

**Example Event** (checkout.session.completed):
```json
{
  "id": "evt_xxx",
  "type": "checkout.session.completed",
  "data": {
    "object": {
      "id": "cs_xxx",
      "customer": "cus_xxx",
      "amount_total": 1400,
      "metadata": {
        "userId": "user_xxx",
        "credits": "75"
      }
    }
  }
}
```

**Response**:
```json
{
  "received": true
}
```

---

## Email Endpoints

### POST /api/email/send
Send email (internal use)

**Auth Required**: Yes (Admin only)

**Request Body**:
```json
{
  "to": "user@example.com",
  "subject": "Email Subject",
  "template": "verification",
  "data": {
    "name": "John",
    "link": "https://..."
  }
}
```

**Response**:
```json
{
  "success": true,
  "messageId": "msg_xxx"
}
```

---

## Admin Endpoints

### GET /api/admin/users
List all users (Admin only)

**Auth Required**: Yes (Admin)

**Query Parameters**:
- `page` (number, optional): Page number (default: 1)
- `limit` (number, optional): Items per page (default: 20)
- `search` (string, optional): Search by name/email

**Response**:
```json
{
  "data": [
    {
      "id": "user_id",
      "name": "John Doe",
      "email": "john@example.com",
      "role": "USER",
      "createdAt": "2024-01-01T00:00:00.000Z"
    }
  ],
  "pagination": {
    "total": 100,
    "page": 1,
    "pages": 5
  }
}
```

### GET /api/admin/stats
Get platform statistics (Admin only)

**Auth Required**: Yes (Admin)

**Response**:
```json
{
  "data": {
    "totalUsers": 1000,
    "activeUsers": 750,
    "totalRevenue": 50000,
    "totalCreditsIssued": 100000
  }
}
```

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

---

## Server Actions (Alternative to API Routes)

SaaSPilot primarily uses Server Actions instead of API routes for most operations. Server Actions are located in `/actions/`.

### Common Server Actions:

#### register.ts
Register a new user

```typescript
import { register } from '@/actions/register'

const result = await register({
  name: "John Doe",
  email: "john@example.com",
  password: "password123"
})
```

#### login.ts
Login user

```typescript
import { login } from '@/actions/login'

const result = await login({
  email: "john@example.com",
  password: "password123"
})
```

#### createStripeSession.ts
Create Stripe checkout session

```typescript
import { createStripeSession } from '@/actions/createStripeSession'

const result = await createStripeSession({
  priceId: "price_xxx",
  credits: 75
})

// Returns: { url: "https://checkout.stripe.com/..." }
```

#### settings.ts
Update user settings

```typescript
import { updateSettings } from '@/actions/settings'

const result = await updateSettings({
  name: "New Name",
  email: "newemail@example.com"
})
```

---

## Testing API Endpoints

### Using cURL

```bash
# Get session
curl http://localhost:3000/api/auth/session \
  -H "Cookie: next-auth.session-token=xxx"

# Test webhook (with signature)
curl -X POST http://localhost:3000/api/webhooks/stripe \
  -H "stripe-signature: xxx" \
  -H "Content-Type: application/json" \
  -d '{"type":"checkout.session.completed",...}'
```

### Using Stripe CLI

```bash
# Forward webhooks to local server
stripe listen --forward-to localhost:3000/api/webhooks/stripe

# Trigger test events
stripe trigger checkout.session.completed
```

---

## Security Considerations

### API Routes
- Always validate authentication
- Use Zod for input validation
- Sanitize user inputs
- Rate limit sensitive endpoints
- Use HTTPS in production

### Webhooks
- Verify signatures
- Use idempotency keys
- Handle duplicate events
- Log all webhook events

### Server Actions
- Always check authentication
- Validate all inputs
- Use transactions for multi-step operations
- Return user-friendly error messages
