<h1 align="center">🚀 SaaSPilot - AI-Ready SaaS Starter Kit</h1>
<p align="center">
  <img height="auto" width="400px" src="https://saas-pilot.vercel.app/images/logo/logo-2.svg" />
</p>

<p align="center">
  <strong>The Most AI-Coding-Agent-Friendly SaaS Boilerplate for Next.js</strong>
</p>

<p align="center">
  <a href="#-quick-start">Quick Start</a> •
  <a href="#-ai-coding-agent-ready">AI Ready</a> •
  <a href="#-features">Features</a> •
  <a href="#-tech-stack">Tech Stack</a> •
  <a href="#-documentation">Documentation</a>
</p>

<br />

SaaSPilot is a **production-ready, full-stack SaaS boilerplate** for Next.js - crafted with great attention to both front-end and back-end functionality. Built with developer experience in mind, it comes with all essential integrations and features, plus **comprehensive AI-agent documentation** for seamless AI-assisted development.

Perfect for launching AI SaaS applications with credit-based billing! 🎯

---

## ✨ What's New: AI-Coding-Agent Optimized! 🤖

SaaSPilot now includes **120KB of comprehensive documentation** specifically designed for AI coding assistants:

- 📚 **13 documentation files** covering every aspect
- 🎯 **3 ready-to-use prompt templates** for common tasks
- 🔍 **Pattern-based code examples** for consistency
- 🛡️ **Security-first patterns** built-in
- ⚡ **5-minute codebase understanding** for AI agents

**AI agents (Claude, Cursor, GitHub Copilot) can now understand and modify the codebase autonomously!**

---

## 🚀 Quick Start

```bash
# 1. Clone the repository
git clone https://github.com/your-repo/saaspilot.git
cd saaspilot

# 2. Install dependencies
npm install

# 3. Set up environment variables
cp .env.example .env.local
# Edit .env.local with your credentials

# 4. Set up the database
npx prisma generate
npx prisma db push

# 5. Run the development server
npm run dev
```

Visit `http://localhost:3000` 🎉

### For AI Agents
👉 Start here: **[CLAUDE.md](CLAUDE.md)** - Complete guide for AI coding assistants

---

## 🤖 AI Coding Agent Ready

SaaSPilot is **optimized for AI-assisted development** with comprehensive documentation designed specifically for AI assistants like Claude, Cursor, and GitHub Copilot.

### 📚 Documentation Structure

```
/
├── CLAUDE.md                        → Quick start for AI agents
├── /docs/                           → Comprehensive technical docs
│   ├── ai-instructions.md          → Core instructions & conventions ⭐
│   ├── architecture.md             → System architecture & diagrams
│   ├── coding-patterns.md          → Standard patterns & best practices
│   ├── database-schema.md          → Complete schema reference
│   ├── component-map.md            → Component relationships
│   ├── api-documentation.md        → API reference & server actions
│   └── troubleshooting.md          → Common issues & solutions
└── /prompts/                        → Task templates for AI agents
    ├── add-new-feature.md          → Feature addition template
    ├── create-api-endpoint.md      → API endpoint template
    └── modify-database-schema.md   → Schema modification guide
```

### 🎯 What AI Agents Can Do

With this documentation, AI coding agents can:

✅ Understand the codebase structure in < 5 minutes
✅ Add new features following established patterns
✅ Create API endpoints without breaking conventions
✅ Modify database schema safely
✅ Navigate components and understand relationships
✅ Troubleshoot issues independently
✅ Generate consistent, production-ready code

### 🚀 Getting Started with AI Agents

**For Claude/Cursor/Copilot:**
1. Read **[CLAUDE.md](CLAUDE.md)** for quick context
2. Review **[docs/ai-instructions.md](docs/ai-instructions.md)** for conventions
3. Reference **[docs/coding-patterns.md](docs/coding-patterns.md)** when writing code
4. Use templates in **[/prompts](prompts/)** for specific tasks

**Example AI Prompt:**
```
"Add a new task management feature to SaaSPilot following the patterns
in /docs/coding-patterns.md and using the template in /prompts/add-new-feature.md"
```

---

## 🎛 Tech Stack

**Frontend:**
- ⚛️ **React 18** - UI library
- 🔷 **Next.js 14** - Full-stack framework (App Router)
- 📘 **TypeScript** - Type safety
- 🎨 **Tailwind CSS** - Styling
- 🧩 **Shadcn/ui** - Component library

**Backend:**
- 🔐 **NextAuth.js v5** - Authentication
- 🗄️ **Prisma ORM** - Database toolkit
- 🍃 **MongoDB** - Database
- 💳 **Stripe** - Payments (credit-based)
- 📧 **Resend/SendGrid** - Email service

**Developer Experience:**
- ✅ **TypeScript** - Full type safety
- 📦 **Zod** - Schema validation
- 🎣 **React Hook Form** - Form management
- 🌐 **next-intl** - Internationalization
- 🎭 **Framer Motion** - Animations

---

## ⚡ Features

### 🤖 AI-Ready Documentation
- **Comprehensive guides** for AI coding agents
- **Pattern-based development** with code examples
- **Ready-to-use prompt templates** for common tasks
- **5-minute onboarding** for AI assistants

### 💳 Payments & Credits System
- **Stripe integration** for credit purchases
- **Credit-based billing** (perfect for AI SaaS)
- **Purchase history tracking**
- **Webhook handling** for payment events
- **3 pricing tiers** (Free, Starter, Pro)

### 🔒 Authentication
- **Email/Password authentication** with bcrypt
- **Social login** (Google, GitHub OAuth)
- **Email verification** system
- **Password reset** functionality
- **Two-factor authentication** (2FA) support
- **Role-based access** (User/Admin)

### 📧 Email System
- **Transactional emails** via Resend or SendGrid
- **React email templates** for beautiful emails
- **Verification emails**
- **Password reset emails**
- **Purchase confirmation emails**

### 🛠️ Database
- **MongoDB with Prisma ORM**
- **8 production-ready models**
- **Type-safe queries**
- **Easy schema migrations**
- **Optimized relationships**

### 👤 User Dashboard
- **Credit balance display**
- **Purchase history**
- **Account settings**
- **Profile management**
- **Email/username change**

### 🌐 Internationalization
- **Multi-language support** (English, German, Arabic)
- **RTL/LTR support** automatic detection
- **Translation management**
- **Locale switching**

### 🎨 UI Components
- **20+ Shadcn/ui components** included
- **Responsive design** (mobile-first)
- **Dark mode support** with next-themes
- **Accessible components** (Radix UI)
- **Landing page templates**
- **Marketing pages** (About, Contact, Pricing)

### 👩‍💼 Admin Features
- **Admin role detection** via environment variable
- **Admin-only API endpoints**
- **User management** (Pro version)
- **Analytics dashboard** (Pro version)

---

## 📜 Documentation

### Official Docs
📖 **Full Documentation:** https://cipherslab.gitbook.io/saas-pilot-docs

### AI-Ready Docs (New!)
Start with these for AI-assisted development:

| Document | Purpose | Size |
|----------|---------|------|
| **[CLAUDE.md](CLAUDE.md)** | Quick start for AI agents | 8 KB |
| **[docs/ai-instructions.md](docs/ai-instructions.md)** | Core instructions & conventions | 6 KB |
| **[docs/architecture.md](docs/architecture.md)** | System architecture | 8 KB |
| **[docs/coding-patterns.md](docs/coding-patterns.md)** | Standard code patterns | 10 KB |
| **[docs/database-schema.md](docs/database-schema.md)** | Database reference | 10 KB |
| **[docs/component-map.md](docs/component-map.md)** | Component relationships | 11 KB |
| **[docs/api-documentation.md](docs/api-documentation.md)** | API reference | 6 KB |
| **[docs/troubleshooting.md](docs/troubleshooting.md)** | Common issues | 7 KB |

📁 **[View all documentation](docs/)**

---

## 💻 Demo

🎬 **Live Demo (Pro version):** https://saas-pilot-demo.vercel.app/

🌐 **Website:** https://saas-pilot.vercel.app

🔥 **Pricing & FAQ:** https://saas-pilot.vercel.app/#pricing

*Note: Certain features are disabled in the free version or can't be included in the public repo.*

---

## 🆚 Free vs Pro Comparison

| Feature | Free | Pro |
|---------|------|-----|
| User Dashboard | ✅ | ✅ |
| Full Authentication (Email + OAuth) | ✅ | ✅ |
| User Profile Management | ✅ | ✅ |
| Landing & Pricing Pages | ✅ Single | ✅ Multiple |
| Marketing Pages (About, Contact) | ✅ | ✅ |
| Multi-Language Support (i18n) | ✅ | ✅ |
| Stripe Credit Purchases | ✅ | ✅ |
| Stripe Subscriptions | ❌ | ✅ |
| Admin Dashboard | ❌ | ✅ |
| Fully Featured Blog | ❌ | ✅ |
| Event-Based Analytics | ❌ | ✅ |
| AI-Ready Documentation | ✅ | ✅ |

---

## 🗂️ Project Structure

```
saaspilot/
├── app/                          # Next.js App Router
│   ├── (marketing)/             # Public pages
│   ├── (protected)/             # Auth-required pages
│   ├── auth/                    # Authentication pages
│   └── api/                     # API routes & webhooks
├── components/                   # React components
│   ├── ui/                      # Shadcn/ui components
│   ├── auth/                    # Auth components
│   └── Common/                  # Shared components
├── actions/                      # Server actions
├── lib/                         # Utilities & services
├── prisma/                      # Database schema
├── docs/                        # AI-ready documentation ⭐
├── prompts/                     # AI prompt templates ⭐
├── hooks/                       # React hooks
├── types/                       # TypeScript types
├── schemas/                     # Zod validation schemas
├── translations/                # i18n translations
└── config/                      # Configuration files
```

---

## 🛠️ Development

### Available Scripts

```bash
# Development
npm run dev              # Start dev server (http://localhost:3000)
npm run build           # Create production build
npm run start           # Start production server

# Database
npx prisma studio       # Open Prisma Studio (visual database)
npx prisma generate     # Regenerate Prisma client
npx prisma db push      # Push schema changes to database
npx prisma migrate dev  # Create and apply migration

# Code Quality
npm run lint            # Run ESLint
npm run type-check      # TypeScript type checking
```

### Environment Variables

Copy `.env.example` to `.env.local` and configure:

```env
# Database
DATABASE_URL="mongodb://..."

# Authentication
NEXTAUTH_SECRET="your-secret"
NEXTAUTH_URL="http://localhost:3000"

# OAuth Providers
GITHUB_CLIENT_ID="..."
GITHUB_CLIENT_SECRET="..."
GOOGLE_CLIENT_ID="..."
GOOGLE_CLIENT_SECRET="..."

# Stripe
STRIPE_SECRET_KEY="sk_test_..."
STRIPE_WEBHOOK_SECRET="whsec_..."

# Email
RESEND_API_KEY="re_..."

# App Configuration
NEXT_PUBLIC_APP_URL="http://localhost:3000"
ADMIN_EMAILS="admin@example.com"
INITIAL_CREDITS_FOR_NEW="20"
```

---

## 🎯 Use Cases

Perfect for building:

- 🤖 **AI SaaS Applications** (ChatGPT-style apps, AI tools)
- 💼 **B2B SaaS Platforms**
- 📊 **Analytics Dashboards**
- 🎨 **Creative Tools**
- 📝 **Content Management Systems**
- 🔧 **Developer Tools**
- 📱 **Mobile-First Web Apps**

The **credit-based billing system** is especially suited for AI/ML applications with usage-based pricing.

---

## 🚀 Deployment

### Vercel (Recommended)

1. Push your code to GitHub
2. Import project in Vercel
3. Add environment variables
4. Deploy! ✨

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/your-repo/saaspilot)

### Other Platforms

SaaSPilot works on any platform supporting Node.js:
- **Railway**
- **Render**
- **DigitalOcean App Platform**
- **AWS (Amplify, ECS)**
- **Google Cloud Run**

---

## 🎓 Learning Resources

### Documentation
- 📖 [Next.js Docs](https://nextjs.org/docs)
- 🔐 [NextAuth.js Docs](https://next-auth.js.org)
- 🗄️ [Prisma Docs](https://www.prisma.io/docs)
- 💳 [Stripe Docs](https://stripe.com/docs)
- 🎨 [Tailwind CSS Docs](https://tailwindcss.com/docs)
- 🧩 [Shadcn/ui Docs](https://ui.shadcn.com)

### SaaSPilot Specific
- 🤖 [AI Agent Guide](CLAUDE.md)
- 📚 [Full Documentation](docs/)
- 🎯 [Prompt Templates](prompts/)
- ❓ [Troubleshooting](docs/troubleshooting.md)

---

## 🤝 Contributing

Contributions are welcome! Here's how:

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Contribution Guidelines
- Follow the established coding patterns (see [docs/coding-patterns.md](docs/coding-patterns.md))
- Add tests for new features
- Update documentation
- Keep commits clean and descriptive

---

## 🐛 Issues & Support

- 🐞 **Bug Reports:** [Open an issue](https://github.com/your-repo/saaspilot/issues)
- 💬 **Questions:** Check [docs/troubleshooting.md](docs/troubleshooting.md) first
- 📧 **Contact:** support@saaspilot.com

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## ⭐ Show Your Support

If you find SaaSPilot helpful:

- ⭐ **Star this repository** to show your support
- 🐦 **Tweet about it** and tag us
- 📝 **Write a blog post** about your experience
- 🎥 **Create a video tutorial**
- 💬 **Share with your network**

Every star helps! 🌟

---

## 🎉 Credits

Built with ❤️ by the SaaSPilot team

**Special Thanks:**
- [Next.js](https://nextjs.org) team for the amazing framework
- [Vercel](https://vercel.com) for hosting and deployment
- [Shadcn](https://ui.shadcn.com) for the beautiful components
- The open-source community

---

## 🔗 Links

- 🌐 **Website:** https://saas-pilot.vercel.app
- 📖 **Documentation:** https://cipherslab.gitbook.io/saas-pilot-docs
- 🎬 **Demo:** https://saas-pilot-demo.vercel.app
- 🐙 **GitHub:** https://github.com/your-repo/saaspilot
- 🐦 **Twitter:** @saaspilot

---

<p align="center">
  <strong>Built for developers, optimized for AI agents 🤖</strong>
  <br />
  <br />
  <a href="#-quick-start">Get Started</a> •
  <a href="CLAUDE.md">AI Guide</a> •
  <a href="docs/">Documentation</a>
</p>

<p align="center">
  Made with ❤️ for the developer community
</p>
