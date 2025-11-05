You are identifying all Pages Router files in a Next.js application to help plan migration to App Router.

## Task

Find and categorize all files in the `pages/` directory to understand the migration scope.

## Search Strategy

### 1. Find All Pages Router Files

Search for files in the `pages/` directory:
- Regular pages: `pages/**/*.tsx`, `pages/**/*.jsx`, `pages/**/*.ts`, `pages/**/*.js`
- API routes: `pages/api/**/*`
- Special files: `_app`, `_document`, `_error`, `404`, `500`

### 2. Categorize Files

**Special Files:**
- `_app.tsx` - Root app component (migrates to `app/layout.tsx`)
- `_document.tsx` - HTML document (migrates to `app/layout.tsx`)
- `_error.tsx` - Error handling (migrates to `app/error.tsx`)
- `404.tsx` - Not found page (migrates to `app/not-found.tsx`)
- `500.tsx` - Server error page

**Route Types:**
- Static routes: `about.tsx` → `app/about/page.tsx`
- Dynamic routes: `[id].tsx` → `app/[id]/page.tsx`
- Catch-all routes: `[...slug].tsx` → `app/[...slug]/page.tsx`
- Optional catch-all: `[[...slug]].tsx` → `app/[[...slug]]/page.tsx`

**API Routes:**
- Regular API: `api/hello.ts` → `app/api/hello/route.ts`
- Dynamic API: `api/users/[id].ts` → `app/api/users/[id]/route.ts`

### 3. Detect Data Fetching Methods

For each page file, check if it uses:
- `getServerSideProps` (server-side rendering)
- `getStaticProps` (static generation)
- `getStaticPaths` (dynamic static routes)
- `getInitialProps` (legacy, needs careful migration)

## Output Format

Present findings as:

### 📁 Pages Router Files

**Summary:**
- Total Files: X
- Page Components: Y
- API Routes: Z
- Special Files: N

**🔧 Special Files**

Files that require careful migration to root layout:
- `pages/_app.tsx` → `app/layout.tsx`
  - Migrate global providers, styles, and layouts
- `pages/_document.tsx` → `app/layout.tsx`
  - Merge custom `<html>` and `<body>` modifications
- `pages/_error.tsx` → `app/error.tsx`
  - Update error handling pattern
- `pages/404.tsx` → `app/not-found.tsx`
  - Convert to new not-found pattern

**📄 Page Components**

**Static Routes (X files):**
Simple one-to-one migrations:
```
pages/index.tsx           → app/page.tsx
pages/about.tsx           → app/about/page.tsx
pages/contact.tsx         → app/contact/page.tsx
pages/blog/index.tsx      → app/blog/page.tsx
```

**Dynamic Routes (Y files):**
Require param type updates:
```
pages/blog/[slug].tsx              → app/blog/[slug]/page.tsx
pages/products/[id].tsx            → app/products/[id]/page.tsx
pages/categories/[...slug].tsx     → app/categories/[...slug]/page.tsx
pages/docs/[[...slug]].tsx         → app/docs/[[...slug]]/page.tsx
```

**Nested Routes (Z files):**
Maintain directory structure:
```
pages/dashboard/index.tsx          → app/dashboard/page.tsx
pages/dashboard/settings.tsx       → app/dashboard/settings/page.tsx
pages/dashboard/users/[id].tsx     → app/dashboard/users/[id]/page.tsx
```

**🔌 API Routes (N files)**

Can stay in `pages/api/` or migrate to `app/api/`:
```
pages/api/hello.ts                 → app/api/hello/route.ts (optional)
pages/api/users/index.ts           → app/api/users/route.ts (optional)
pages/api/users/[id].ts            → app/api/users/[id]/route.ts (optional)
pages/api/auth/[...nextauth].ts    → Keep in pages/api/ (auth libs may require it)
```

**⚡ Data Fetching Analysis**

**Server-Side Rendering (X files):**
Files using `getServerSideProps`:
- `pages/index.tsx:25` - Fetches user data
- `pages/dashboard.tsx:40` - Requires authentication
- `pages/products/[id].tsx:15` - Fetches product details

**Static Generation (Y files):**
Files using `getStaticProps`:
- `pages/blog/[slug].tsx:30` - Generates blog posts
- `pages/docs/[...slug].tsx:20` - Generates documentation

**Static Paths (Z files):**
Files using `getStaticPaths`:
- `pages/blog/[slug].tsx:45` - Defines blog post paths
- `pages/products/[id].tsx:50` - Defines product paths

**Legacy Pattern (N files):**
Files using `getInitialProps` (⚠️ needs manual review):
- `pages/legacy-page.tsx:10` - Uses old pattern

**📊 Migration Priority**

**Phase 1 - Quick Wins (Simple Pages):**
Pages with no data fetching can be migrated immediately:
- `pages/about.tsx`
- `pages/contact.tsx`
- `pages/terms.tsx`

**Phase 2 - Standard Patterns:**
Pages with standard data fetching:
- `pages/index.tsx` - `getServerSideProps`
- `pages/blog/[slug].tsx` - `getStaticProps` + `getStaticPaths`

**Phase 3 - Complex Patterns:**
Pages requiring careful migration:
- `pages/_app.tsx` - Global state and providers
- `pages/legacy-page.tsx` - Uses `getInitialProps`

**Phase 4 - API Routes (Optional):**
- All API routes can remain in `pages/api/`
- Or migrate to `app/api/` for consistency

**💡 Migration Tips**

1. **Start Small**: Begin with simple static pages
2. **Test Incrementally**: Verify each migrated page
3. **Run Both Routers**: Keep `pages/` and `app/` coexisting
4. **App Router Precedence**: App Router routes override Pages Router
5. **API Routes**: Can stay in `pages/api/` indefinitely

**📋 Next Steps**

1. Review the file list above
2. Identify pages to migrate first
3. Use `/analyze-migration` for detailed migration plan
4. Use `/modernize-patterns` to update deprecated patterns

Would you like me to help migrate specific files?
