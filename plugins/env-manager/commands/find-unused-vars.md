You are analyzing environment variable files to find variables that are defined but never used in the codebase.

## Task

Identify environment variables in `.env` files that have no references in the code, which could indicate:
- Leftover variables from removed features
- Typos in variable names
- Variables that should be removed
- Dead code cleanup opportunities

## Analysis Steps

### 1. Parse All Environment Files

Read and extract variables from:
- `.env`
- `.env.local`
- `.env.development`
- `.env.production`
- `.env.example`

For each file, collect:
- Variable name
- Whether it has a value
- Line number

### 2. Search for Variable Usage

For each defined variable, search the entire codebase for:

**Direct References:**
```typescript
process.env.VARIABLE_NAME
process.env['VARIABLE_NAME']
process.env["VARIABLE_NAME"]
```

**Destructuring:**
```typescript
const { VARIABLE_NAME } = process.env
```

**Template Literals:**
```typescript
`${process.env.VARIABLE_NAME}`
```

**Comments/Documentation:**
```typescript
// Uses VARIABLE_NAME for configuration
```

### 3. Check Common Exception Cases

Some variables may be used indirectly:

**Next.js Built-in:**
- `NODE_ENV` - Used by Next.js framework
- `PORT` - Used by server
- `HOSTNAME` - Used by server

**Build Tools:**
- `ANALYZE` - Used by bundle analyzer
- `SKIP_ENV_VALIDATION` - Used by build scripts

**Framework Conventions:**
- `NEXTAUTH_*` - Used by NextAuth.js
- `DATABASE_URL` - Used by Prisma/ORMs

### 4. Categorize Unused Variables

**Definitely Unused:**
- No code references
- Not a known framework variable
- Not in config files

**Possibly Unused:**
- Only in comments
- In dead code
- In config files but not code

**False Positives:**
- Framework/tool variables
- Dynamic variable names
- Used in scripts/config

## Output Format

Present findings as:

### 🗑️ Unused Environment Variables

**Summary:**
- Total defined variables: X
- Variables in use: Y
- Potentially unused: Z
- Confirmed unused: N

**❌ Confirmed Unused (N variables)**

Variables with no references in the codebase:

**In .env.local (M variables):**
- `OLD_API_KEY` (Line 15)
  - **Status:** No references found
  - **Defined in:** `.env.local`
  - **Action:** Safe to remove (likely from old feature)

- `LEGACY_DATABASE_URL` (Line 8)
  - **Status:** No references found
  - **Defined in:** `.env.local`, `.env.example`
  - **Action:** Remove from both files

- `UNUSED_STRIPE_WEBHOOK` (Line 22)
  - **Status:** No references found
  - **Defined in:** `.env.local`
  - **Action:** Check if webhook is still configured, then remove

**In .env.example only (K variables):**
- `EXAMPLE_FEATURE_FLAG` (Line 30)
  - **Status:** No references in code
  - **Defined in:** `.env.example` only
  - **Action:** Remove or document purpose

**⚠️ Possibly Unused (Z variables)**

Variables that might be unused but need verification:

**Used in Comments Only:**
- `REDIS_URL` (Line 12)
  - **References:** Found in comments only
    - `lib/cache.ts:3` - "// TODO: Use REDIS_URL for caching"
  - **Action:** Either implement feature or remove variable

**Used in Dead Code:**
- `MAILCHIMP_API_KEY` (Line 18)
  - **References:** Only in commented out code
    - `lib/newsletter.ts:10-20` - Entire function commented out
  - **Action:** Remove commented code and variable

**Used in Config Files Only:**
- `SENTRY_DSN` (Line 25)
  - **References:** Only in `next.config.js`
  - **Note:** This is likely being used by Sentry SDK
  - **Action:** Keep (false positive)

**✅ Framework Variables (Keep)**

Variables used by frameworks/tools (not directly referenced in code):

- `NODE_ENV` - Used by Next.js runtime
- `PORT` - Used by Next.js server
- `DATABASE_URL` - Used by Prisma in `schema.prisma`
- `NEXTAUTH_SECRET` - Used by NextAuth.js
- `NEXTAUTH_URL` - Used by NextAuth.js
- `ANALYZE` - Used by @next/bundle-analyzer

### 📊 Cleanup Recommendations

**Priority 1 - Safe to Remove (N variables):**

Variables with zero references:
```bash
# Remove these from .env.local:
OLD_API_KEY
LEGACY_DATABASE_URL
UNUSED_STRIPE_WEBHOOK
DEPRECATED_CONFIG
```

**Priority 2 - Investigate (M variables):**

Variables that need verification:
```bash
# Check if these are still needed:
REDIS_URL          # Has TODO comment
MAILCHIMP_API_KEY  # In commented code
EXAMPLE_FEATURE    # Purpose unclear
```

**Priority 3 - Update .env.example:**

Remove unused variables from template:
```bash
# Remove from .env.example:
EXAMPLE_FEATURE_FLAG
OLD_SERVICE_KEY
```

### 🎯 Detailed Cleanup Plan

**Step 1: Backup Current Files**
```bash
# Create backups before cleanup
cp .env.local .env.local.backup
cp .env.example .env.example.backup
```

**Step 2: Remove Confirmed Unused**

**From .env.local:**
```diff
- OLD_API_KEY=xxx
- LEGACY_DATABASE_URL=postgresql://old-db
- UNUSED_STRIPE_WEBHOOK=whsec_xxx
```

**From .env.example:**
```diff
- EXAMPLE_FEATURE_FLAG=true
- OLD_SERVICE_KEY=your_key_here
```

**Step 3: Investigate Commented Code**

Check if these features should be re-enabled or removed:
- `lib/newsletter.ts` - Mailchimp integration (commented out)
- `lib/cache.ts` - Redis caching (TODO comment)

**Step 4: Document Decisions**

Add comments for kept variables:
```bash
# Stripe Webhooks (currently using webhook #1 only)
# Keep STRIPE_WEBHOOK_SECRET_1 but removed unused webhooks 2-5
STRIPE_WEBHOOK_SECRET_1=whsec_xxx
```

### ⚠️ Important Warnings

**1. Variables Used by External Tools**

Some variables aren't in your code but used by tools:
- **Vercel:** Build environment variables
- **Prisma:** `DATABASE_URL` in schema.prisma
- **NextAuth:** Provider configs in `/api/auth/[...nextauth]`

**2. Dynamic Variable Access**

Code using dynamic variable names won't be detected:
```typescript
// This won't be found by simple search!
const key = isDev ? 'DEV_KEY' : 'PROD_KEY'
const value = process.env[key]
```

**3. Serverless Functions**

Some variables might only be used in:
- Vercel serverless functions
- Middleware
- Build-time scripts

### 🔍 False Positive Detection

**Variables to KEEP despite no direct references:**

**Next.js Built-ins:**
- `NODE_ENV` - Framework uses automatically
- `PORT` - Server listens on this
- `HOSTNAME` - Server binds to this

**Tool Configuration:**
- `ANALYZE=true` - Bundle analyzer
- `SKIP_ENV_VALIDATION=1` - Skip validation in CI

**ORM/Database:**
- `DATABASE_URL` - Prisma uses in schema.prisma
- `SHADOW_DATABASE_URL` - Prisma migrations
- `DIRECT_URL` - Supabase direct connection

**Auth Libraries:**
- `NEXTAUTH_SECRET` - NextAuth.js internals
- `NEXTAUTH_URL` - NextAuth.js base URL

**Build Process:**
- Variables used in `next.config.js`
- Variables used in middleware
- Variables in custom server code

### 📝 Cleanup Script

Consider creating an automated cleanup helper:

```typescript
// scripts/cleanup-env.ts
import fs from 'fs'
import path from 'path'

const KEEP_LIST = [
  'NODE_ENV',
  'PORT',
  'DATABASE_URL',
  'NEXTAUTH_SECRET',
  'NEXTAUTH_URL',
  // ... other framework vars
]

const unusedVars = [
  'OLD_API_KEY',
  'LEGACY_DATABASE_URL',
  // ... confirmed unused
]

function removeUnusedVars(filepath: string) {
  const content = fs.readFileSync(filepath, 'utf-8')
  const lines = content.split('\n')

  const cleaned = lines.filter(line => {
    const match = line.match(/^([A-Z_]+)=/)
    if (!match) return true // Keep comments and empty lines

    const varName = match[1]
    return !unusedVars.includes(varName)
  })

  fs.writeFileSync(filepath, cleaned.join('\n'))
  console.log(`✅ Cleaned ${filepath}`)
}

// Usage
removeUnusedVars('.env.local')
removeUnusedVars('.env.example')
```

### 📚 Best Practices

**1. Regular Audits**

Run this check periodically:
```bash
# Add to package.json
{
  "scripts": {
    "audit-env": "claude /env-manager:find-unused-vars"
  }
}
```

**2. Document Purpose**

Add comments to clarify variable purpose:
```bash
# Used by Stripe webhook handler in /api/webhooks/stripe
STRIPE_WEBHOOK_SECRET=whsec_xxx

# Optional: Enable experimental feature (default: false)
NEXT_PUBLIC_ENABLE_BETA_FEATURES=true
```

**3. Version Control**

Track when variables were added:
```bash
# Added 2024-01-15 for new payment provider
NEW_PAYMENT_API_KEY=xxx
```

### ✅ Summary

**Recommended Actions:**
1. ✅ Remove N confirmed unused variables
2. ⚠️ Investigate M possibly unused variables
3. 📝 Document purpose of kept variables
4. 🔄 Run this check monthly

**Cleanup Benefits:**
- Reduced confusion for new developers
- Smaller environment file size
- Easier to identify required variables
- Better security (fewer credentials to manage)

Would you like me to:
1. Remove the confirmed unused variables?
2. Create a cleaned `.env.example` file?
3. Add documentation comments to kept variables?
4. Generate the cleanup script?
