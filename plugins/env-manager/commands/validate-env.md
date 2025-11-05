You are validating environment variables in a Next.js project to ensure all required variables are properly configured.

## Task

Check if environment variables used in the codebase are defined in the appropriate `.env` files.

## Analysis Steps

### 1. Find Environment Variable Usage

Search for environment variable references in the codebase:

**Client-Side Variables (NEXT_PUBLIC_*):**
- Search for `process.env.NEXT_PUBLIC_`
- Search for `import.meta.env.NEXT_PUBLIC_` (Vite projects)

**Server-Side Variables:**
- Search for `process.env.` (excluding NEXT_PUBLIC_)
- Common patterns in API routes, server components, server actions

### 2. Find Environment Files

Look for these files in the project root:
- `.env` - Base environment variables
- `.env.local` - Local overrides (gitignored)
- `.env.development` - Development-specific vars
- `.env.production` - Production-specific vars
- `.env.example` - Example/template file

### 3. Parse Environment Files

Read each `.env` file and extract:
- Variable names
- Whether they have values
- Comments/descriptions

### 4. Cross-Reference Usage vs Definition

For each variable used in code:
- ✅ Defined in at least one `.env` file
- ⚠️ Only in `.env.example` (needs to be set)
- ❌ Not defined anywhere (missing!)

### 5. Check Next.js Specific Rules

Validate Next.js environment variable rules:
- Client-side vars MUST start with `NEXT_PUBLIC_`
- Server-side vars should NOT start with `NEXT_PUBLIC_`
- Built-in vars: `NODE_ENV`, `PORT`, `HOSTNAME`

## Output Format

Present findings as:

### 🔍 Environment Variable Validation

**Summary:**
- Variables in use: X
- Properly configured: Y
- Missing or incomplete: Z
- Environment files found: N

**📁 Environment Files**

Found the following environment files:
- ✅ `.env.local` (X variables)
- ✅ `.env.example` (Y variables)
- ⚠️ `.env.development` (missing)
- ⚠️ `.env.production` (missing)

**✅ Properly Configured (Y variables)**

Variables that are correctly set:
- `NEXT_PUBLIC_API_URL` - Defined in `.env.local`
- `DATABASE_URL` - Defined in `.env.local`
- `NEXTAUTH_SECRET` - Defined in `.env.local`
- `NEXTAUTH_URL` - Defined in `.env.local`

**⚠️ Missing in .env.local (Z variables)**

Variables used in code but only defined in `.env.example`:
- `NEXT_PUBLIC_STRIPE_KEY` - Used in `app/checkout/page.tsx:15`
  - Only in `.env.example`, needs to be set in `.env.local`
- `STRIPE_SECRET_KEY` - Used in `app/api/checkout/route.ts:8`
  - Only in `.env.example`, needs to be set in `.env.local`

**❌ Completely Missing (N variables)**

Variables used in code but not defined anywhere:
- `NEXT_PUBLIC_GA_ID` - Used in `app/layout.tsx:25`
  - Not found in any `.env` file
  - Add to `.env.local` and `.env.example`
- `EMAIL_SERVER` - Used in `lib/email.ts:12`
  - Not found in any `.env` file
  - Add to `.env.local` and `.env.example`

**🚨 Potential Issues**

**Incorrect Naming:**
- `API_URL` in `components/client.tsx:8` - Client component using non-public var
  - ❌ Should be `NEXT_PUBLIC_API_URL` for client-side access
  - Current: `process.env.API_URL` (will be undefined in browser)
  - Fix: Rename to `NEXT_PUBLIC_API_URL` or move to server component

**Server Vars in Client Components:**
- `components/dashboard.tsx:15` - Client component accessing `DATABASE_URL`
  - ⚠️ Server-only variables are not available in client components
  - This will always be `undefined` in the browser

**Hardcoded Values:**
Found potential hardcoded values that should be environment variables:
- `lib/config.ts:5` - Hardcoded API URL: `https://api.example.com`
  - Consider moving to `NEXT_PUBLIC_API_URL`
- `app/api/stripe/route.ts:12` - Hardcoded webhook secret
  - Should use `STRIPE_WEBHOOK_SECRET` environment variable

### 📋 Required Actions

**Priority 1 - Critical (Blocks Development):**
1. Add missing variables to `.env.local`:
```bash
# Copy these to your .env.local:
NEXT_PUBLIC_GA_ID=your_ga_id_here
EMAIL_SERVER=smtp://user:pass@smtp.example.com
NEXT_PUBLIC_STRIPE_KEY=pk_test_...
STRIPE_SECRET_KEY=sk_test_...
```

**Priority 2 - Fix Naming Issues:**
1. Rename `API_URL` to `NEXT_PUBLIC_API_URL` in code and env files
2. Update all references in client components

**Priority 3 - Add to .env.example:**
1. Document missing variables in `.env.example`:
```bash
# Add to .env.example:
NEXT_PUBLIC_GA_ID=your_google_analytics_id
EMAIL_SERVER=your_email_smtp_url
```

### 🎯 Best Practices

**1. Use .env.local for secrets:**
```bash
# .env.local (gitignored)
DATABASE_URL=postgresql://...
NEXTAUTH_SECRET=secret123
STRIPE_SECRET_KEY=sk_live_...
```

**2. Use .env.example as template:**
```bash
# .env.example (committed to git)
DATABASE_URL=postgresql://user:password@localhost:5432/dbname
NEXTAUTH_SECRET=generate_a_random_secret
STRIPE_SECRET_KEY=sk_test_your_key_here
```

**3. Client vs Server Variables:**
```tsx
// ✅ Client-side (browser accessible)
const apiUrl = process.env.NEXT_PUBLIC_API_URL

// ✅ Server-side only
const dbUrl = process.env.DATABASE_URL

// ❌ Wrong - server var in client component
'use client'
const dbUrl = process.env.DATABASE_URL // Will be undefined!
```

**4. TypeScript Type Safety:**
Create an `env.ts` file for type-safe access:
```tsx
// env.ts
declare global {
  namespace NodeJS {
    interface ProcessEnv {
      DATABASE_URL: string
      NEXTAUTH_SECRET: string
      NEXT_PUBLIC_API_URL: string
      NEXT_PUBLIC_STRIPE_KEY: string
    }
  }
}

export {}
```

### ⚙️ Validation Script

Consider adding this to `package.json`:
```json
{
  "scripts": {
    "validate-env": "node scripts/validate-env.js"
  }
}
```

Create `scripts/validate-env.js`:
```javascript
const required = [
  'DATABASE_URL',
  'NEXTAUTH_SECRET',
  'NEXT_PUBLIC_API_URL'
]

const missing = required.filter(key => !process.env[key])

if (missing.length > 0) {
  console.error('Missing required env vars:', missing)
  process.exit(1)
}

console.log('✅ All required environment variables are set')
```

### 📚 Resources

- [Next.js Environment Variables](https://nextjs.org/docs/basic-features/environment-variables)
- [Environment Variable Best Practices](https://nextjs.org/docs/pages/building-your-application/configuring/environment-variables)

Would you like me to:
1. Create the missing `.env.local` entries?
2. Generate a complete `.env.example` file?
3. Fix client/server variable naming issues?
