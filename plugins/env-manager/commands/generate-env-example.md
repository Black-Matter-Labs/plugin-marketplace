You are generating a `.env.example` file from existing environment variable files in a Next.js project.

## Task

Create or update `.env.example` with all environment variables found in `.env`, `.env.local`, `.env.development`, and `.env.production`, but with placeholder values for security.

## Analysis Steps

### 1. Find Existing Environment Files

Search for these files in the project root:
- `.env`
- `.env.local`
- `.env.development`
- `.env.production`
- `.env.example` (to update if exists)

### 2. Parse All Environment Variables

Extract all unique variable names from found files:
- Parse each file
- Extract variable names (ignore comments)
- Keep track of which file each var came from
- Preserve comments/descriptions

### 3. Categorize Variables

Group variables by type:
- **Database**: `DATABASE_URL`, `DB_*`, `POSTGRES_*`, etc.
- **Authentication**: `NEXTAUTH_*`, `AUTH_*`, `JWT_*`, etc.
- **API Keys**: `*_API_KEY`, `*_SECRET_KEY`, etc.
- **Public Config**: `NEXT_PUBLIC_*`
- **Third-Party**: `STRIPE_*`, `SENDGRID_*`, `AWS_*`, etc.
- **App Config**: `NODE_ENV`, `PORT`, `APP_URL`, etc.

### 4. Generate Placeholder Values

Replace actual values with descriptive placeholders:
- **URLs**: `https://example.com`, `postgresql://user:password@localhost:5432/dbname`
- **Secrets**: `your_secret_key_here`, `generate_random_secret`
- **API Keys**: `your_api_key_here`, `pk_test_your_key`
- **Boolean**: `true`, `false`
- **Numbers**: `3000`, `5432`

### 5. Preserve Documentation

Keep helpful comments:
- Variable descriptions
- Where to get the value
- Default values
- Required vs optional

## Output Format

Present findings and generate the file:

### 📝 Generating .env.example

**Environment Files Found:**
- `.env.local` - 12 variables
- `.env.development` - 5 variables
- `.env.production` - 8 variables

**Variables Discovered:**
- Total unique variables: 15
- Client-side (NEXT_PUBLIC_*): 3
- Server-side: 12

**Categorization:**
- Database: 2 variables
- Authentication: 4 variables
- API Keys: 5 variables
- Public Config: 3 variables
- App Config: 1 variable

### 📄 Generated .env.example

```bash
# =============================================================================
# Next.js Application Environment Variables
# =============================================================================
# Copy this file to .env.local and fill in your actual values
# NEVER commit .env.local to version control!

# -----------------------------------------------------------------------------
# App Configuration
# -----------------------------------------------------------------------------
NODE_ENV=development
PORT=3000
NEXT_PUBLIC_APP_URL=http://localhost:3000

# -----------------------------------------------------------------------------
# Database
# -----------------------------------------------------------------------------
# PostgreSQL connection string
# Format: postgresql://user:password@host:port/database
DATABASE_URL=postgresql://user:password@localhost:5432/myapp

# Prisma direct database URL (for migrations)
DIRECT_URL=postgresql://user:password@localhost:5432/myapp

# -----------------------------------------------------------------------------
# Authentication
# -----------------------------------------------------------------------------
# NextAuth.js configuration
# Generate secret: openssl rand -base64 32
NEXTAUTH_SECRET=your_nextauth_secret_here
NEXTAUTH_URL=http://localhost:3000

# OAuth Providers
GITHUB_ID=your_github_oauth_id
GITHUB_SECRET=your_github_oauth_secret

GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret

# -----------------------------------------------------------------------------
# API Keys & Services
# -----------------------------------------------------------------------------
# Stripe (Payment Processing)
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_test_your_stripe_publishable_key
STRIPE_SECRET_KEY=sk_test_your_stripe_secret_key
STRIPE_WEBHOOK_SECRET=whsec_your_webhook_secret

# SendGrid (Email)
SENDGRID_API_KEY=SG.your_sendgrid_api_key

# AWS S3 (File Storage)
AWS_ACCESS_KEY_ID=your_aws_access_key
AWS_SECRET_ACCESS_KEY=your_aws_secret_key
AWS_REGION=us-east-1
AWS_S3_BUCKET=your-bucket-name

# -----------------------------------------------------------------------------
# Third-Party Services
# -----------------------------------------------------------------------------
# Google Analytics
NEXT_PUBLIC_GA_MEASUREMENT_ID=G-XXXXXXXXXX

# Sentry (Error Tracking)
NEXT_PUBLIC_SENTRY_DSN=https://xxx@xxx.ingest.sentry.io/xxx

# -----------------------------------------------------------------------------
# Feature Flags
# -----------------------------------------------------------------------------
NEXT_PUBLIC_ENABLE_ANALYTICS=true
NEXT_PUBLIC_ENABLE_CHAT=false

# -----------------------------------------------------------------------------
# Development Only
# -----------------------------------------------------------------------------
# Set to 'true' to enable debug logging
DEBUG=false
```

### ✅ File Created Successfully

Created `.env.example` with 15 variables organized into 7 categories.

### 🔐 Security Notes

**Sanitized Values:**
The following types of values were replaced with placeholders:
- Actual secrets and API keys
- Database credentials
- OAuth secrets
- Webhook secrets

**Original Values Preserved In:**
- `.env.local` (gitignored - your actual secrets are safe!)

### 📋 Next Steps

**For New Team Members:**
```bash
# Copy the example file
cp .env.example .env.local

# Fill in actual values
# Edit .env.local with your real API keys and secrets
```

**For CI/CD:**
```bash
# Ensure these variables are set in your deployment environment:
# - Vercel: Project Settings → Environment Variables
# - Railway: Variables tab
# - GitHub Actions: Repository Secrets
```

### ⚠️ Important Reminders

**1. Gitignore Check:**
Ensure `.env.local` is in `.gitignore`:
```
# .gitignore
.env*.local
.env.local
```

**2. Required vs Optional:**
Consider adding comments to indicate which variables are required:
```bash
# REQUIRED: Database connection
DATABASE_URL=postgresql://...

# OPTIONAL: Enable analytics (defaults to false)
NEXT_PUBLIC_ENABLE_ANALYTICS=true
```

**3. Default Values:**
Document default values when applicable:
```bash
# Port for development server (default: 3000)
PORT=3000

# Node environment (development | production | test)
NODE_ENV=development
```

### 🎯 Validation

Consider adding these checks:

**1. Startup Validation:**
```typescript
// lib/env.ts
const required = [
  'DATABASE_URL',
  'NEXTAUTH_SECRET',
] as const

required.forEach(key => {
  if (!process.env[key]) {
    throw new Error(`Missing required environment variable: ${key}`)
  }
})
```

**2. Type Safety:**
```typescript
// env.d.ts
declare global {
  namespace NodeJS {
    interface ProcessEnv {
      DATABASE_URL: string
      NEXTAUTH_SECRET: string
      NEXT_PUBLIC_APP_URL: string
      // ... other variables
    }
  }
}
```

### 📚 Documentation Tips

Add these sections to your README:

**Environment Setup:**
````markdown
## Environment Variables

1. Copy the example file:
   ```bash
   cp .env.example .env.local
   ```

2. Fill in the required values:
   - `DATABASE_URL` - Your PostgreSQL connection string
   - `NEXTAUTH_SECRET` - Generate with `openssl rand -base64 32`
   - `STRIPE_SECRET_KEY` - Get from [Stripe Dashboard](https://dashboard.stripe.com)

3. Optional variables:
   - `NEXT_PUBLIC_GA_MEASUREMENT_ID` - For Google Analytics
   - `SENTRY_DSN` - For error tracking
````

Would you like me to:
1. Create the `.env.example` file now?
2. Update existing `.env.example` with new variables?
3. Add comments and documentation to existing variables?
