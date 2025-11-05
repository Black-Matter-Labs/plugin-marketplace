You are analyzing a Next.js project to find environment variables used in code but not defined in any environment files.

## Task

Search the codebase for environment variable references and identify which ones are missing from `.env` files.

## Search Strategy

### 1. Find All Environment Variable References

Search for these patterns in `.ts`, `.tsx`, `.js`, `.jsx` files:

**Pattern 1: process.env**
```typescript
process.env.VARIABLE_NAME
process.env['VARIABLE_NAME']
process.env["VARIABLE_NAME"]
```

**Pattern 2: Destructuring**
```typescript
const { DATABASE_URL, API_KEY } = process.env
```

**Pattern 3: Optional Chaining**
```typescript
process.env?.VARIABLE_NAME
```

**Pattern 4: Template Literals**
```typescript
`${process.env.BASE_URL}/api`
```

**Pattern 5: Default Values**
```typescript
process.env.PORT || 3000
process.env.NODE_ENV ?? 'development'
```

### 2. Parse Environment Files

Read existing `.env` files:
- `.env`
- `.env.local`
- `.env.development`
- `.env.production`
- `.env.example`

Extract all defined variable names.

### 3. Compare Usage vs Definitions

For each variable found in code:
- Check if defined in any `.env` file
- Track file location and line number
- Note if only in `.env.example`

### 4. Categorize Missing Variables

**Critical:**
- Used in server-side code (API routes, server components)
- Required for app to function

**Warning:**
- Used in client components
- Has fallback/default value

**Info:**
- Only in `.env.example`
- Commented out in env files

## Output Format

Present findings as:

### 🔍 Missing Environment Variables

**Summary:**
- Variables in code: X
- Defined in env files: Y
- Missing completely: Z
- Only in .env.example: N

**❌ Completely Missing (Z variables)**

Variables used in code but not defined anywhere:

**Critical - Server-Side (M variables):**
- `DATABASE_URL` - Used in 3 locations:
  - `lib/db.ts:5` - Database connection
  - `prisma/schema.prisma:3` - Prisma datasource
  - `app/api/users/route.ts:12` - User queries
  - **Impact:** App will crash without this
  - **Fix:** Add to `.env.local` and `.env.example`

- `NEXTAUTH_SECRET` - Used in 1 location:
  - `app/api/auth/[...nextauth]/route.ts:15` - Auth configuration
  - **Impact:** Authentication will fail
  - **Fix:** Generate with `openssl rand -base64 32`

- `STRIPE_SECRET_KEY` - Used in 2 locations:
  - `app/api/checkout/route.ts:8` - Payment processing
  - `lib/stripe.ts:5` - Stripe client initialization
  - **Impact:** Payments won't work
  - **Fix:** Get from Stripe Dashboard

**Warning - Client-Side (N variables):**
- `NEXT_PUBLIC_API_URL` - Used in 5 locations:
  - `components/api-client.tsx:10` - API calls
  - `hooks/useData.ts:8` - Data fetching
  - `app/dashboard/page.tsx:20` - Dashboard data
  - **Impact:** API calls will fail, using undefined
  - **Fix:** Add to `.env.local` with your API URL

- `NEXT_PUBLIC_GA_ID` - Used in 1 location:
  - `app/layout.tsx:25` - Google Analytics
  - **Impact:** Analytics won't track (non-critical)
  - **Fix:** Add GA measurement ID or remove code

**⚠️ Only in .env.example (N variables)**

Variables in `.env.example` but not in active env files:

- `SENDGRID_API_KEY` - Used in 1 location:
  - `lib/email.ts:5` - Email sending
  - **Status:** In `.env.example` but not `.env.local`
  - **Impact:** Email functionality won't work
  - **Fix:** Copy from `.env.example` to `.env.local` and set real value

- `AWS_ACCESS_KEY_ID` - Used in 2 locations:
  - `lib/s3.ts:8` - File upload
  - `app/api/upload/route.ts:15` - Upload endpoint
  - **Status:** In `.env.example` but not `.env.local`
  - **Impact:** File uploads will fail
  - **Fix:** Add AWS credentials to `.env.local`

**✅ Properly Defined (Y variables)**

These variables are correctly configured:
- `NODE_ENV` - Defined in `.env`
- `PORT` - Defined in `.env.local`
- `NEXT_PUBLIC_APP_NAME` - Defined in `.env.local`

### 📊 Usage Breakdown by File

**Most Used Variables:**
1. `NEXT_PUBLIC_API_URL` - 5 references across 3 files
2. `DATABASE_URL` - 3 references across 3 files
3. `STRIPE_SECRET_KEY` - 2 references across 2 files

**Files with Most Missing Vars:**
1. `app/api/checkout/route.ts` - 3 missing vars
2. `lib/email.ts` - 2 missing vars
3. `components/api-client.tsx` - 2 missing vars

### 🎯 Required Actions

**Step 1: Create/Update .env.local**

Add these critical missing variables:
```bash
# Database
DATABASE_URL=postgresql://user:password@localhost:5432/myapp

# Authentication
NEXTAUTH_SECRET=generate_with_openssl_rand_base64_32
NEXTAUTH_URL=http://localhost:3000

# Payment Processing
STRIPE_SECRET_KEY=sk_test_your_stripe_secret_key
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_test_your_stripe_key

# API Configuration
NEXT_PUBLIC_API_URL=http://localhost:3000/api

# Email Service
SENDGRID_API_KEY=SG.your_sendgrid_api_key

# File Storage
AWS_ACCESS_KEY_ID=your_aws_access_key
AWS_SECRET_ACCESS_KEY=your_aws_secret_key
AWS_REGION=us-east-1
AWS_S3_BUCKET=your-bucket-name
```

**Step 2: Update .env.example**

Document all variables for team members:
```bash
# Copy these to .env.example as well
# with placeholder values and helpful comments
```

**Step 3: Add Type Safety**

Create `env.d.ts` for TypeScript autocomplete:
```typescript
declare global {
  namespace NodeJS {
    interface ProcessEnv {
      // Database
      DATABASE_URL: string

      // Authentication
      NEXTAUTH_SECRET: string
      NEXTAUTH_URL: string

      // Stripe
      STRIPE_SECRET_KEY: string
      NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY: string

      // API
      NEXT_PUBLIC_API_URL: string

      // Email
      SENDGRID_API_KEY: string

      // AWS
      AWS_ACCESS_KEY_ID: string
      AWS_SECRET_ACCESS_KEY: string
      AWS_REGION: string
      AWS_S3_BUCKET: string
    }
  }
}

export {}
```

### ⚠️ Common Issues Found

**1. Client Component Using Server Var**

Found client components accessing server-only variables:
- `components/settings.tsx:20` - Accessing `DATABASE_URL`
  - ❌ This will be `undefined` in the browser
  - **Fix:** Move logic to server component or API route

**2. Missing NEXT_PUBLIC_ Prefix**

Found client-side usage without proper prefix:
- `components/config.ts:5` - Using `API_URL` in client code
  - ❌ Should be `NEXT_PUBLIC_API_URL`
  - **Fix:** Rename variable and update all references

**3. Typos in Variable Names**

Possible typos detected:
- `lib/config.ts:8` - `DATABSE_URL` (missing 'A')
  - Should be: `DATABASE_URL`
- `app/api/send/route.ts:5` - `SENDGIRD_API_KEY` (typo)
  - Should be: `SENDGRID_API_KEY`

### 🛡️ Recommendations

**1. Add Startup Validation**

Create a validation script that runs on app start:
```typescript
// lib/validate-env.ts
const required = [
  'DATABASE_URL',
  'NEXTAUTH_SECRET',
  'STRIPE_SECRET_KEY',
] as const

const missing = required.filter(key => !process.env[key])

if (missing.length > 0) {
  console.error('❌ Missing required environment variables:')
  missing.forEach(key => console.error(`  - ${key}`))
  throw new Error('Environment validation failed')
}

console.log('✅ All required environment variables are set')
```

**2. Use t3-env for Type-Safe Validation**

```typescript
import { createEnv } from "@t3-oss/env-nextjs"
import { z } from "zod"

export const env = createEnv({
  server: {
    DATABASE_URL: z.string().url(),
    NEXTAUTH_SECRET: z.string().min(32),
    STRIPE_SECRET_KEY: z.string().startsWith("sk_"),
  },
  client: {
    NEXT_PUBLIC_API_URL: z.string().url(),
    NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY: z.string().startsWith("pk_"),
  },
  runtimeEnv: process.env,
})
```

**3. Document Where to Get Values**

Add helpful comments in `.env.example`:
```bash
# Stripe Secret Key
# 1. Go to https://dashboard.stripe.com/apikeys
# 2. Copy the "Secret key" (starts with sk_)
# 3. Use test key (sk_test_) for development
STRIPE_SECRET_KEY=sk_test_your_key_here

# Database URL
# Format: postgresql://user:password@host:port/database
# Local: postgresql://postgres:postgres@localhost:5432/myapp
# Supabase: Check your project settings for connection string
DATABASE_URL=postgresql://user:password@localhost:5432/myapp
```

### 📚 Next Steps

1. **Immediate:** Add missing critical variables to `.env.local`
2. **Short-term:** Update `.env.example` with all variables
3. **Long-term:** Add environment validation and TypeScript types

Would you like me to:
1. Create the `.env.local` file with missing variables?
2. Update `.env.example` with proper documentation?
3. Generate the TypeScript type definitions?
4. Create a validation script?
