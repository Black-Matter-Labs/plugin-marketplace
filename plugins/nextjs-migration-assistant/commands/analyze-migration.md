You are analyzing a Next.js application to help migrate from Pages Router to App Router. Provide a comprehensive migration assessment.

## Task

Analyze the codebase to identify:
1. Current router type (Pages Router, App Router, or hybrid)
2. Files that need migration
3. Patterns that need modernization
4. Estimated migration complexity

## Analysis Steps

### 1. Detect Router Type

Check for:
- `pages/` directory (Pages Router)
- `app/` directory (App Router)
- Both (hybrid setup)

### 2. Identify Pages Router Files

Search for files in `pages/` directory:
- Page components (`pages/**/*.tsx`, `pages/**/*.jsx`)
- API routes (`pages/api/**/*.ts`)
- Special files (`_app.tsx`, `_document.tsx`, `_error.tsx`)
- Dynamic routes (`[slug].tsx`, `[...slug].tsx`)

### 3. Check for Pages Router Patterns

Look for patterns that need migration:
- `getServerSideProps` - Migrate to Server Components with async/await
- `getStaticProps` - Migrate to Server Components with fetch caching
- `getStaticPaths` - Migrate to `generateStaticParams`
- `getInitialProps` - Needs manual migration (legacy)
- `useRouter` from `next/router` - Replace with `next/navigation`
- `<Link>` without automatic `<a>` - Update usage pattern
- `<Head>` from `next/head` - Replace with `metadata` export
- `<Image>` with old props - Update to new API

### 4. Analyze API Routes

Check API routes in `pages/api/`:
- Can stay in Pages Router or move to App Router route handlers
- Note: App Router uses `route.ts` files instead

### 5. Check Dependencies

Look for packages that may need updates:
- Next.js version (App Router requires 13.4+)
- React version (18+ required)
- Router-dependent packages

### 6. Estimate Complexity

Categorize migration difficulty:
- **Simple**: Static pages with no data fetching
- **Moderate**: Pages with `getStaticProps` or `getServerSideProps`
- **Complex**: Pages with `getInitialProps`, complex routing, or many API routes

## Output Format

Present findings as:

### 🔍 Migration Analysis Report

**Current Setup:**
- Router Type: [Pages Router / App Router / Hybrid]
- Next.js Version: X.X.X
- Total Pages: X
- API Routes: Y

**📊 Migration Scope**

**Pages Router Files Found (X files):**
```
pages/
├── _app.tsx
├── _document.tsx
├── index.tsx
├── about.tsx
├── blog/
│   ├── [slug].tsx
│   └── index.tsx
└── api/
    ├── hello.ts
    └── users/
        └── [id].ts
```

**🔄 Patterns to Migrate**

**Data Fetching (X instances):**
- `pages/index.tsx:15` - `getServerSideProps` → Server Component
- `pages/blog/[slug].tsx:45` - `getStaticProps` + `getStaticPaths` → Server Component + `generateStaticParams`
- `pages/about.tsx:10` - `getStaticProps` → Server Component

**Routing & Navigation (Y instances):**
- `components/nav.tsx:8` - `useRouter` from `next/router` → `useRouter` from `next/navigation`
- `components/link.tsx:12` - `<Link><a>` → `<Link>` (automatic anchor)

**Metadata (Z instances):**
- `pages/index.tsx:5` - `<Head>` → `metadata` export
- `pages/blog/[slug].tsx:8` - Dynamic `<Head>` → `generateMetadata`

**Legacy Patterns (N instances):**
- `pages/dashboard.tsx:20` - `getInitialProps` (⚠️ requires manual review)

**📁 Suggested App Router Structure**

```
app/
├── layout.tsx        (migrate from _app.tsx)
├── page.tsx          (migrate from pages/index.tsx)
├── about/
│   └── page.tsx      (migrate from pages/about.tsx)
├── blog/
│   ├── page.tsx      (migrate from pages/blog/index.tsx)
│   └── [slug]/
│       └── page.tsx  (migrate from pages/blog/[slug].tsx)
└── api/
    ├── hello/
    │   └── route.ts  (migrate from pages/api/hello.ts)
    └── users/
        └── [id]/
            └── route.ts
```

**⚡ Migration Complexity**

- **Simple Pages (X):** No data fetching, straightforward migration
  - `pages/about.tsx`
  - `pages/contact.tsx`

- **Moderate Pages (Y):** Standard data fetching patterns
  - `pages/index.tsx` - Has `getServerSideProps`
  - `pages/blog/[slug].tsx` - Has `getStaticProps` + `getStaticPaths`

- **Complex Pages (Z):** Requires careful migration
  - `pages/dashboard.tsx` - Uses `getInitialProps`
  - `pages/auth/callback.tsx` - Complex client-side routing

**🎯 Migration Recommendations**

### Phase 1: Setup (Low Risk)
1. Ensure Next.js 13.4+ and React 18+
2. Create `app/` directory alongside `pages/`
3. Create root layout (`app/layout.tsx`) migrating logic from `_app.tsx`

### Phase 2: Simple Pages (Low Risk)
1. Migrate static pages first (no data fetching)
2. Test each page individually
3. Keep both routers running during migration

### Phase 3: Data Fetching Pages (Moderate Risk)
1. Convert `getServerSideProps` → Server Components
2. Convert `getStaticProps` → Server Components with fetch caching
3. Update `getStaticPaths` → `generateStaticParams`

### Phase 4: Complex Patterns (High Risk)
1. Manually review `getInitialProps` usage
2. Update routing hooks and navigation
3. Migrate metadata and SEO

### Phase 5: API Routes (Optional)
1. Keep in `pages/api/` (still supported)
2. Or migrate to `app/api/*/route.ts` for consistency

### Phase 6: Cleanup
1. Remove `pages/` directory
2. Update imports and dependencies
3. Remove Pages Router specific code

**⚠️ Important Notes:**
- You can run both routers simultaneously during migration
- App Router takes precedence over Pages Router for matching routes
- API routes in `pages/api/` continue to work even with App Router
- Test thoroughly at each phase before proceeding

### 📚 Migration Examples

**Example 1: getServerSideProps → Server Component**
```tsx
// Before (Pages Router)
export async function getServerSideProps() {
  const data = await fetchData()
  return { props: { data } }
}

export default function Page({ data }) {
  return <div>{data.title}</div>
}

// After (App Router)
async function fetchData() {
  const res = await fetch('https://api.example.com/data', {
    cache: 'no-store' // equivalent to getServerSideProps
  })
  return res.json()
}

export default async function Page() {
  const data = await fetchData()
  return <div>{data.title}</div>
}
```

**Example 2: getStaticProps → Server Component**
```tsx
// Before (Pages Router)
export async function getStaticProps() {
  const data = await fetchData()
  return { props: { data }, revalidate: 60 }
}

// After (App Router)
async function fetchData() {
  const res = await fetch('https://api.example.com/data', {
    next: { revalidate: 60 } // equivalent to revalidate in getStaticProps
  })
  return res.json()
}
```

**Example 3: Dynamic Routes**
```tsx
// Before (Pages Router)
export async function getStaticPaths() {
  const posts = await getPosts()
  return {
    paths: posts.map(post => ({ params: { slug: post.slug } })),
    fallback: false
  }
}

// After (App Router)
export async function generateStaticParams() {
  const posts = await getPosts()
  return posts.map(post => ({ slug: post.slug }))
}
```

Would you like me to help migrate specific files or patterns?
