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

### Error: "Unique constraint failed on the fields: (email)"
- Email already exists in database
- Use `findFirst` to check before creating
- Handle P2002 error code in catch block

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
- Google: `http://localhost:3000/api/auth/callback/google`
- GitHub: `http://localhost:3000/api/auth/callback/github`

### Error: "CSRF token mismatch"
- Middleware configuration issue
- Check NEXTAUTH_URL setting
- Clear cookies and try again
- Verify domain matches in production

### Session expires immediately
- Check AUTH_SECRET is set
- Verify JWT expiration settings in auth.ts
- Check for clock sync issues

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

### Test card not working
```
Test Card: 4242 4242 4242 4242
Expiry: Any future date
CVC: Any 3 digits
ZIP: Any 5 digits
```

### Credits not added after payment
- Check webhook logs in Stripe dashboard
- Verify STRIPE_WEBHOOK_SECRET is correct
- Check database for Purchase records
- Look at console logs in webhook handler

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

### Error: "Cannot find module '@/...'"
- Check tsconfig.json paths configuration
- Verify import paths use correct alias
- Restart TypeScript server in IDE

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

### Verification emails not received
- Check spam folder
- Verify email service is configured
- Check console logs for send errors
- Test with different email provider

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
- Make sure no spaces around = in .env file

### Error: "window is not defined"
- Using browser API in server component
- Add 'use client' directive
- Check for useEffect/useState in server components

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

### Database queries slow
- Add indexes in schema.prisma
- Use database query logging
- Optimize WHERE clauses
- Consider Redis for caching

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

### "Webhook signature verification failed"
- STRIPE_WEBHOOK_SECRET is incorrect
- Using wrong webhook secret (test vs live)
- Payload was modified in transit

---

## Debugging Tips

### Enable verbose logging
```typescript
// In server actions
console.log('Debug:', { variable1, variable2 })

// In Prisma queries
// Add to db/index.ts
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

### Server-side debugging
```typescript
// Add console.logs in server actions
console.log('Session:', session)
console.log('Input:', data)
console.log('Result:', result)
```

---

## Prisma-Specific Issues

### Error: "Unknown arg `connectOrCreate`"
- Update Prisma version
- Run `npm install @prisma/client@latest`

### Error: "Can't reach database server"
- Check DATABASE_URL format
- Verify MongoDB is running
- Check network connectivity
- Verify MongoDB Atlas IP whitelist

### Schema changes not reflected
```bash
# Regenerate client
npx prisma generate

# Push changes to database
npx prisma db push
```

---

## Stripe-Specific Issues

### Webhook events not in dashboard
- Check endpoint URL is correct
- Verify webhook is enabled
- Check event version compatibility

### Test mode vs Live mode confusion
- Use test keys for development
- Switch to live keys for production
- Verify which mode you're in Stripe dashboard

### Customer not created
- Check Stripe customer creation in webhook
- Verify metadata is being passed
- Check for errors in Stripe logs

---

## Internationalization Issues

### Wrong language displayed
- Check locale detection in middleware
- Verify translation files exist
- Check browser language settings

### Missing translations
- Check translation keys in JSON files
- Verify key exists in all language files
- Add fallback to default language

### RTL layout issues
- Check rtl-detect implementation
- Verify Tailwind directives
- Test with Arabic locale

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

---

## Useful Debugging Commands

```bash
# Check Node version
node -v

# Check npm version
npm -v

# Check installed packages
npm list --depth=0

# Verify environment variables are loaded
echo $DATABASE_URL

# Test database connection
npx prisma db pull

# View generated Prisma client
cat node_modules/.prisma/client/index.d.ts

# Check Next.js info
npx next info
```
