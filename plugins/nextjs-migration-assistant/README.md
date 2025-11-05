# Next.js Migration Assistant

A plugin to help migrate Next.js applications from Pages Router to App Router with automated analysis, pattern detection, and migration guidance.

## Features

- 🔍 Analyze entire codebase for migration readiness
- 📁 Identify all Pages Router files that need migration
- 🔄 Detect deprecated patterns and suggest modern alternatives
- 📊 Estimate migration complexity and effort
- 📝 Provide step-by-step migration plans
- 💡 Show code examples for common migration patterns

## Commands

### `/analyze-migration`

Run a comprehensive migration analysis on your Next.js application.

**What it does:**
- Detects current router type (Pages Router, App Router, or hybrid)
- Identifies all files that need migration
- Analyzes data fetching patterns (`getServerSideProps`, `getStaticProps`, etc.)
- Estimates migration complexity
- Provides phase-by-phase migration plan
- Shows before/after code examples

**Usage:**
```bash
/analyze-migration
```

**Output includes:**
- Current setup overview
- Complete file structure mapping
- Pattern-by-pattern migration guide
- Complexity assessment
- Prioritized action items

### `/find-pages`

Identify all Pages Router files and categorize them for migration planning.

**What it does:**
- Lists all files in the `pages/` directory
- Categorizes by type (static, dynamic, API routes, special files)
- Shows data fetching methods used
- Maps old structure to new App Router structure
- Prioritizes files by migration difficulty

**Usage:**
```bash
/find-pages
```

**Output includes:**
- Complete file inventory
- Route type categorization
- Data fetching analysis
- Suggested App Router structure
- Migration priority order

### `/modernize-patterns`

Find deprecated Next.js patterns and get modern replacements.

**What it does:**
- Detects `next/router` usage (should be `next/navigation`)
- Finds old `<Link>` patterns with `<a>` tags
- Identifies `<Head>` usage (should be `metadata` exports)
- Checks for deprecated Image component props
- Finds old font loading patterns
- Suggests modern alternatives with code examples

**Usage:**
```bash
/modernize-patterns
```

**Output includes:**
- Pattern-by-pattern analysis
- Priority categorization
- Before/after code examples
- Migration checklist

## Installation

### From Black Matter Labs Marketplace

```bash
# Add the marketplace (if not already added)
/plugin marketplace add Black-Matter-Labs/plugin-marketplace

# Install the plugin
/plugin install nextjs-migration-assistant
```

### Manual Installation

```bash
/plugin install github:Black-Matter-Labs/plugin-marketplace --path plugins/nextjs-migration-assistant
```

## Migration Guide

### Step 1: Analysis

Start with a comprehensive analysis:
```bash
/analyze-migration
```

This will give you:
- Full scope of migration work
- Complexity estimate
- Recommended migration phases

### Step 2: Inventory

Get a detailed file inventory:
```bash
/find-pages
```

This shows:
- All files that need migration
- Suggested new structure
- Priority order

### Step 3: Pattern Modernization

Check for deprecated patterns:
```bash
/modernize-patterns
```

This identifies:
- Old patterns to update
- Modern replacements
- Priority updates

### Step 4: Execute Migration

Follow the phased approach suggested by the analysis:

**Phase 1: Setup**
- Ensure Next.js 13.4+ and React 18+
- Create `app/` directory
- Create root layout

**Phase 2: Simple Pages**
- Migrate static pages first
- Test each page

**Phase 3: Data Fetching**
- Convert `getServerSideProps`
- Convert `getStaticProps`
- Update `getStaticPaths`

**Phase 4: Complex Patterns**
- Migrate `getInitialProps`
- Update navigation hooks
- Modernize metadata

**Phase 5: Cleanup**
- Remove `pages/` directory
- Clean up deprecated imports

## What Gets Migrated

### Data Fetching

**getServerSideProps → Server Component**
```tsx
// Before
export async function getServerSideProps() {
  const data = await fetchData()
  return { props: { data } }
}

// After
async function fetchData() {
  const res = await fetch('...', { cache: 'no-store' })
  return res.json()
}

export default async function Page() {
  const data = await fetchData()
  return <div>{data.title}</div>
}
```

**getStaticProps → Server Component with Revalidation**
```tsx
// Before
export async function getStaticProps() {
  const data = await fetchData()
  return { props: { data }, revalidate: 60 }
}

// After
async function fetchData() {
  const res = await fetch('...', { next: { revalidate: 60 } })
  return res.json()
}
```

**getStaticPaths → generateStaticParams**
```tsx
// Before
export async function getStaticPaths() {
  const items = await getItems()
  return {
    paths: items.map(item => ({ params: { id: item.id } })),
    fallback: false
  }
}

// After
export async function generateStaticParams() {
  const items = await getItems()
  return items.map(item => ({ id: item.id }))
}
```

### Routing & Navigation

**Router Hooks**
```tsx
// Before
import { useRouter } from 'next/router'
const router = useRouter()
const { pathname, query } = router

// After
import { usePathname, useSearchParams } from 'next/navigation'
const pathname = usePathname()
const searchParams = useSearchParams()
```

**Link Component**
```tsx
// Before
<Link href="/about">
  <a>About</a>
</Link>

// After
<Link href="/about">About</Link>
```

### Metadata

**Static Metadata**
```tsx
// Before
import Head from 'next/head'

<Head>
  <title>About</title>
  <meta name="description" content="..." />
</Head>

// After
export const metadata = {
  title: 'About',
  description: '...',
}
```

**Dynamic Metadata**
```tsx
// Before
<Head>
  <title>{post.title}</title>
</Head>

// After
export async function generateMetadata({ params }) {
  const post = await getPost(params.id)
  return {
    title: post.title,
  }
}
```

### File Structure

**Pages Router:**
```
pages/
├── _app.tsx
├── _document.tsx
├── index.tsx
├── about.tsx
└── blog/
    ├── [slug].tsx
    └── index.tsx
```

**App Router:**
```
app/
├── layout.tsx
├── page.tsx
├── about/
│   └── page.tsx
└── blog/
    ├── page.tsx
    └── [slug]/
        └── page.tsx
```

## Best Practices

### 1. Incremental Migration

Run both routers simultaneously during migration:
- Keep `pages/` directory while building `app/`
- App Router routes take precedence
- Migrate page by page, testing each

### 2. API Routes

API routes can remain in `pages/api/`:
- Still fully supported with App Router
- No need to migrate unless you want to
- Use `app/api/*/route.ts` for new routes

### 3. Testing

Test thoroughly at each phase:
- Verify functionality after each migration
- Check data fetching behavior
- Test dynamic routes
- Validate metadata and SEO

### 4. Client Components

Remember to add `'use client'` when needed:
- Interactive components
- Browser APIs
- React hooks (useState, useEffect)
- Navigation hooks from `next/navigation`

## Common Pitfalls

### 1. Forgetting 'use client'

Navigation hooks require client components:
```tsx
'use client'

import { useRouter } from 'next/navigation'
```

### 2. Non-Serializable Props

Can't pass functions or class instances to Client Components:
```tsx
// ❌ Bad
<ClientComponent callback={() => {}} />

// ✅ Good
<ClientComponent onAction={serverAction} />
```

### 3. Mixing Router Imports

Don't mix `next/router` and `next/navigation`:
```tsx
// ❌ Bad
import { useRouter } from 'next/router'

// ✅ Good (App Router)
import { useRouter } from 'next/navigation'
```

## FAQ

**Q: Can I run both routers at the same time?**
A: Yes! You can have both `pages/` and `app/` directories. App Router takes precedence for matching routes.

**Q: Do I need to migrate API routes?**
A: No, API routes in `pages/api/` continue to work with App Router.

**Q: What about getInitialProps?**
A: This requires manual migration. Convert to Server Components or Client Components with appropriate data fetching.

**Q: Will my existing pages break?**
A: No, Pages Router continues to work. Migrate incrementally at your own pace.

**Q: What Next.js version do I need?**
A: App Router requires Next.js 13.4 or higher and React 18+.

## Contributing

Found a bug or have a suggestion? [Open an issue](https://github.com/Black-Matter-Labs/plugin-marketplace/issues)

## Resources

- [Next.js App Router Documentation](https://nextjs.org/docs/app)
- [Migration Guide](https://nextjs.org/docs/app/building-your-application/upgrading/app-router-migration)
- [Data Fetching](https://nextjs.org/docs/app/building-your-application/data-fetching)

## License

MIT
