# Environment Variable Manager

A plugin for managing environment variables in Next.js projects. Validate configurations, generate template files, detect missing variables, and clean up unused ones.

## Features

- ✅ Validate environment variable configuration
- 📝 Generate `.env.example` from existing env files
- 🔍 Find variables used in code but not defined
- 🗑️ Detect unused environment variables
- 🔐 Security best practices enforcement
- 📊 Type safety recommendations
- 🚨 Prevent production bugs from missing env vars

## Commands

### `/validate-env`

Comprehensive validation of environment variable configuration.

**What it checks:**
- Variables used in code vs defined in env files
- Proper `NEXT_PUBLIC_` prefix for client-side vars
- Server variables accidentally used in client components
- Missing variables that could cause runtime errors
- Variables only in `.env.example` (need to be set)

**Usage:**
```bash
/validate-env
```

**Output includes:**
- Configuration status summary
- Properly configured variables
- Missing variables with locations
- Client/server naming issues
- Security warnings
- Action items with priority

### `/generate-env-example`

Generate or update `.env.example` with all environment variables.

**What it does:**
- Scans all `.env` files for variable names
- Creates sanitized placeholder values
- Categorizes variables by type
- Preserves helpful comments
- Adds documentation and examples

**Usage:**
```bash
/generate-env-example
```

**Output includes:**
- Well-organized `.env.example` file
- Categorized variables (Database, Auth, API Keys, etc.)
- Helpful comments and instructions
- Where to get values
- Default values when applicable

### `/check-missing-vars`

Find environment variables used in code but not defined in any env file.

**What it finds:**
- Variables referenced in code
- Missing from all `.env` files
- Only defined in `.env.example`
- Typos in variable names
- Client/server usage issues

**Usage:**
```bash
/check-missing-vars
```

**Output includes:**
- Complete list of missing variables
- File locations and line numbers
- Impact assessment (critical/warning)
- Suggested fixes
- TypeScript type definitions

### `/find-unused-vars`

Detect environment variables defined but never used in the codebase.

**What it finds:**
- Variables in `.env` files with no code references
- Leftover from removed features
- Possible typos
- Variables safe to remove
- False positives (framework vars)

**Usage:**
```bash
/find-unused-vars
```

**Output includes:**
- Unused variables by file
- Safe to remove vs needs investigation
- Framework/tool exceptions
- Cleanup recommendations
- Backup instructions

## Installation

### From Black Matter Labs Marketplace

```bash
# Add the marketplace (if not already added)
/plugin marketplace add Black-Matter-Labs/plugin-marketplace

# Install the plugin
/plugin install env-manager
```

### Manual Installation

```bash
/plugin install github:Black-Matter-Labs/plugin-marketplace --path plugins/env-manager
```

## Common Use Cases

### 1. New Project Setup

Generate a proper `.env.example` template:
```bash
/generate-env-example
```

### 2. Production Deployment

Validate all required variables are set:
```bash
/validate-env
```

Fix any issues before deploying.

### 3. Code Review

Check for missing or incorrectly named variables:
```bash
/check-missing-vars
```

### 4. Spring Cleaning

Find and remove unused environment variables:
```bash
/find-unused-vars
```

### 5. Onboarding New Developers

Ensure `.env.example` is up-to-date:
```bash
/generate-env-example
```

New developers can then:
```bash
cp .env.example .env.local
# Fill in actual values
```

## Environment Variable Best Practices

### Client vs Server Variables

**Client-Side (Browser Accessible):**
```typescript
// ✅ Correct - starts with NEXT_PUBLIC_
const apiUrl = process.env.NEXT_PUBLIC_API_URL

// ❌ Wrong - server var in client component
'use client'
const secret = process.env.SECRET_KEY // Will be undefined!
```

**Server-Side Only:**
```typescript
// ✅ Correct - used in Server Component or API route
const dbUrl = process.env.DATABASE_URL
```

### File Structure

**`.env.local` (gitignored)**
Your actual secrets:
```bash
DATABASE_URL=postgresql://user:pass@localhost:5432/db
NEXTAUTH_SECRET=your_actual_secret
STRIPE_SECRET_KEY=sk_live_actual_key
```

**`.env.example` (committed)**
Template for team:
```bash
DATABASE_URL=postgresql://user:password@localhost:5432/dbname
NEXTAUTH_SECRET=generate_with_openssl_rand_base64_32
STRIPE_SECRET_KEY=sk_test_your_stripe_secret_key
```

### Required Variables

Always include:
```bash
# Database
DATABASE_URL=postgresql://...

# Authentication (if using NextAuth)
NEXTAUTH_SECRET=xxx
NEXTAUTH_URL=http://localhost:3000

# App URL
NEXT_PUBLIC_APP_URL=http://localhost:3000
```

### Type Safety

Create `env.d.ts` for autocomplete:
```typescript
declare global {
  namespace NodeJS {
    interface ProcessEnv {
      DATABASE_URL: string
      NEXTAUTH_SECRET: string
      NEXT_PUBLIC_API_URL: string
      // ... more variables
    }
  }
}

export {}
```

### Validation

Add startup validation:
```typescript
// lib/env.ts
const required = [
  'DATABASE_URL',
  'NEXTAUTH_SECRET',
] as const

required.forEach(key => {
  if (!process.env[key]) {
    throw new Error(`Missing: ${key}`)
  }
})
```

## Common Issues Detected

### 1. Missing NEXT_PUBLIC_ Prefix

**Problem:**
```tsx
// components/Header.tsx (Client Component)
const apiUrl = process.env.API_URL // undefined in browser!
```

**Fix:**
```tsx
// Rename to NEXT_PUBLIC_API_URL
const apiUrl = process.env.NEXT_PUBLIC_API_URL
```

### 2. Server Var in Client Component

**Problem:**
```tsx
'use client'

function Settings() {
  const db = process.env.DATABASE_URL // undefined!
}
```

**Fix:**
Move logic to Server Component or API route.

### 3. Variables Only in .env.example

**Problem:**
```bash
# Only in .env.example
STRIPE_SECRET_KEY=sk_test_xxx

# Missing from .env.local
```

**Fix:**
```bash
# Add to .env.local with real value
STRIPE_SECRET_KEY=sk_test_your_actual_key
```

### 4. Typos

**Problem:**
```typescript
// Typo: DATABSE_URL
const db = process.env.DATABSE_URL
```

**Fix:**
```typescript
const db = process.env.DATABASE_URL
```

## Integration with Tools

### t3-env (Recommended)

Use with [`@t3-oss/env-nextjs`](https://env.t3.gg) for runtime validation:

```typescript
import { createEnv } from "@t3-oss/env-nextjs"
import { z } from "zod"

export const env = createEnv({
  server: {
    DATABASE_URL: z.string().url(),
    STRIPE_SECRET_KEY: z.string().startsWith("sk_"),
  },
  client: {
    NEXT_PUBLIC_API_URL: z.string().url(),
  },
  runtimeEnv: process.env,
})
```

### Vercel

Environment variables in Vercel:
1. Project Settings → Environment Variables
2. Add variables for each environment
3. Pull to local: `vercel env pull .env.local`

### Railway

Environment variables in Railway:
1. Project → Variables tab
2. Add variables
3. Link to local: Use Railway CLI

## Workflow

### Daily Development

1. Pull latest code
2. Run `/validate-env` to check configuration
3. Update `.env.local` if needed

### Before Commit

1. Run `/find-unused-vars` to cleanup
2. Run `/generate-env-example` to update template
3. Commit updated `.env.example`

### Before Deployment

1. Run `/validate-env` to ensure all vars are set
2. Fix any missing or incorrectly named variables
3. Verify environment-specific vars in deployment platform

### Monthly Maintenance

1. Run `/find-unused-vars` for cleanup
2. Update documentation for remaining vars
3. Remove obsolete variables

## Security Notes

### Never Commit Secrets

Ensure `.gitignore` contains:
```
.env*.local
.env.local
```

### Rotate Secrets Regularly

Change these periodically:
- `NEXTAUTH_SECRET`
- API keys
- Database passwords

### Use Different Keys Per Environment

Don't reuse keys across:
- Development
- Staging
- Production

### Validate at Runtime

Don't let the app start with missing critical vars:
```typescript
if (!process.env.DATABASE_URL) {
  throw new Error('DATABASE_URL is required')
}
```

## FAQ

**Q: Should I commit `.env.local`?**
A: No! Always add to `.gitignore`. Only commit `.env.example`.

**Q: What's the difference between `.env` and `.env.local`?**
A: `.env` can be committed (defaults), `.env.local` is gitignored (secrets).

**Q: How do I share env vars with my team?**
A: Update `.env.example` with placeholders, commit it. Team copies to `.env.local`.

**Q: Can I use env vars in client components?**
A: Only if they start with `NEXT_PUBLIC_`. Server vars are not accessible in browser.

**Q: When should I use this plugin?**
A:
- Setting up new projects
- Onboarding developers
- Before deployments
- Code reviews
- Monthly cleanup

## Troubleshooting

### "Variable is undefined in browser"

Client components can only access `NEXT_PUBLIC_*` variables. Either:
1. Rename to `NEXT_PUBLIC_VARIABLE_NAME`
2. Move logic to server component
3. Create API route

### "Missing required environment variable"

1. Check variable name spelling
2. Ensure it's in `.env.local`
3. Restart dev server (`yarn dev`)
4. Check `.env.example` for reference

### "Variable defined but not used"

Safe to remove if:
- No code references
- Not a framework variable
- Not used by external tools

Keep if:
- Used by Next.js/Prisma/etc
- Used in build scripts
- Used in deployment

## Contributing

Found a bug or have a suggestion? [Open an issue](https://github.com/Black-Matter-Labs/plugin-marketplace/issues)

## Resources

- [Next.js Environment Variables](https://nextjs.org/docs/basic-features/environment-variables)
- [t3-env Documentation](https://env.t3.gg)
- [The Twelve-Factor App - Config](https://12factor.net/config)

## License

MIT
