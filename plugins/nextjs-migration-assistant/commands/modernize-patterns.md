You are analyzing Next.js code to identify and suggest modern pattern replacements for deprecated or outdated patterns.

## Task

Find outdated Next.js patterns in the codebase and suggest modern App Router alternatives.

## Patterns to Find

### 1. Router Hooks

**Old Pattern:**
```tsx
import { useRouter } from 'next/router'

const router = useRouter()
const { query, pathname, push, replace } = router
```

**New Pattern:**
```tsx
import { useRouter, usePathname, useSearchParams } from 'next/navigation'

const router = useRouter()
const pathname = usePathname()
const searchParams = useSearchParams()
```

### 2. Link Component

**Old Pattern:**
```tsx
import Link from 'next/link'

<Link href="/about">
  <a>About</a>
</Link>
```

**New Pattern:**
```tsx
import Link from 'next/link'

<Link href="/about">About</Link>
```

### 3. Head Component

**Old Pattern:**
```tsx
import Head from 'next/head'

export default function Page() {
  return (
    <>
      <Head>
        <title>My Page</title>
        <meta name="description" content="Description" />
      </Head>
      <div>Content</div>
    </>
  )
}
```

**New Pattern:**
```tsx
import type { Metadata } from 'next'

export const metadata: Metadata = {
  title: 'My Page',
  description: 'Description',
}

export default function Page() {
  return <div>Content</div>
}
```

### 4. Dynamic Head (Runtime Metadata)

**Old Pattern:**
```tsx
import Head from 'next/head'

export default function Page({ post }) {
  return (
    <>
      <Head>
        <title>{post.title}</title>
      </Head>
      <div>{post.content}</div>
    </>
  )
}
```

**New Pattern:**
```tsx
import type { Metadata } from 'next'

export async function generateMetadata({ params }): Promise<Metadata> {
  const post = await getPost(params.id)
  return {
    title: post.title,
  }
}

export default async function Page({ params }) {
  const post = await getPost(params.id)
  return <div>{post.content}</div>
}
```

### 5. Script Component

**Old Pattern:**
```tsx
import Script from 'next/script'

<Script src="https://example.com/script.js" />
```

**New Pattern (if in layout):**
```tsx
import Script from 'next/script'

<Script src="https://example.com/script.js" strategy="afterInteractive" />
```

### 6. Image Component (Old Props)

**Old Pattern:**
```tsx
<Image src="/image.jpg" layout="fill" objectFit="cover" />
<Image src="/image.jpg" layout="responsive" />
```

**New Pattern:**
```tsx
<Image src="/image.jpg" fill style={{ objectFit: 'cover' }} />
<Image src="/image.jpg" width={800} height={600} style={{ width: '100%', height: 'auto' }} />
```

### 7. Font Optimization

**Old Pattern:**
```tsx
import Head from 'next/head'

<Head>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap" rel="stylesheet" />
</Head>
```

**New Pattern:**
```tsx
import { Inter } from 'next/font/google'

const inter = Inter({ subsets: ['latin'] })

export default function RootLayout({ children }) {
  return (
    <html className={inter.className}>
      <body>{children}</body>
    </html>
  )
}
```

### 8. Middleware

**Old Pattern:**
```tsx
// pages/_middleware.ts
import { NextRequest, NextResponse } from 'next/server'

export function middleware(request: NextRequest) {
  return NextResponse.next()
}
```

**New Pattern:**
```tsx
// middleware.ts (at root)
import { NextRequest, NextResponse } from 'next/server'

export function middleware(request: NextRequest) {
  return NextResponse.next()
}

export const config = {
  matcher: '/dashboard/:path*',
}
```

### 9. Environment Variables

**Old Pattern:**
```tsx
const apiUrl = process.env.NEXT_PUBLIC_API_URL
```

**New Pattern (still valid, but with better typing):**
```tsx
// env.ts
declare global {
  namespace NodeJS {
    interface ProcessEnv {
      NEXT_PUBLIC_API_URL: string
      DATABASE_URL: string
    }
  }
}

const apiUrl = process.env.NEXT_PUBLIC_API_URL
```

## Search Strategy

1. Search for imports from `next/router`, `next/head`, etc.
2. Find usage patterns of old APIs
3. Check for deprecated component props
4. Identify files that can be modernized

## Output Format

Present findings as:

### 🔄 Pattern Modernization Report

**Summary:**
- Deprecated patterns found: X
- High priority updates: Y
- Low priority updates: Z

**🚨 High Priority - Router Changes**

**`next/router` Imports (X instances):**
Update to `next/navigation`:
- `components/nav.tsx:1` - Import from `next/router`
- `components/header.tsx:3` - Import from `next/router`
- `app/dashboard/page.tsx:5` - Uses `useRouter` from `next/router`

**Migration:**
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

**⚠️ Medium Priority - Component Updates**

**Link Component (Y instances):**
Remove unnecessary `<a>` tags:
- `components/nav.tsx:15` - `<Link><a>...</a></Link>`
- `components/footer.tsx:20` - `<Link><a>...</a></Link>`

**Migration:**
```tsx
// Before
<Link href="/about">
  <a className="link">About</a>
</Link>

// After
<Link href="/about" className="link">
  About
</Link>
```

**Head Component (Z instances):**
Convert to metadata exports:
- `app/about/page.tsx:5` - Uses `<Head>` component
- `app/blog/page.tsx:8` - Uses `<Head>` component

**Migration:**
```tsx
// Before
import Head from 'next/head'

export default function Page() {
  return (
    <>
      <Head>
        <title>About</title>
      </Head>
      <div>Content</div>
    </>
  )
}

// After
import type { Metadata } from 'next'

export const metadata: Metadata = {
  title: 'About',
}

export default function Page() {
  return <div>Content</div>
}
```

**💡 Low Priority - Optimizations**

**Image Component (N instances):**
Update deprecated layout props:
- `components/hero.tsx:12` - Uses `layout="fill"`
- `components/card.tsx:8` - Uses `layout="responsive"`

**Migration:**
```tsx
// Before
<Image src="/hero.jpg" layout="fill" objectFit="cover" />

// After
<Image src="/hero.jpg" fill style={{ objectFit: 'cover' }} />
```

**Font Loading (M instances):**
Use next/font for optimization:
- `app/layout.tsx:5` - Loading Google Fonts via `<link>`
- `components/header.tsx:3` - External font link

**Migration:**
```tsx
// Before
<link href="https://fonts.googleapis.com/css2?family=Inter&display=swap" rel="stylesheet" />

// After
import { Inter } from 'next/font/google'

const inter = Inter({ subsets: ['latin'] })

<html className={inter.className}>
```

**📋 Modernization Checklist**

### Phase 1: Critical Updates
- [ ] Update all `next/router` imports to `next/navigation`
- [ ] Update router hook usage (`useRouter`, `usePathname`, etc.)
- [ ] Test navigation and routing thoroughly

### Phase 2: Component Updates
- [ ] Remove `<a>` tags from `<Link>` components
- [ ] Convert `<Head>` to `metadata` exports
- [ ] Update dynamic metadata with `generateMetadata`

### Phase 3: Optimizations
- [ ] Update Image component deprecated props
- [ ] Migrate to `next/font` for font loading
- [ ] Update Script component usage

### Phase 4: Testing
- [ ] Test all navigation flows
- [ ] Verify SEO metadata
- [ ] Check image loading and optimization
- [ ] Validate font loading

**🔍 Detailed Examples**

**Example 1: Complex Router Migration**
```tsx
// Before (Pages Router)
import { useRouter } from 'next/router'

export default function Page() {
  const router = useRouter()
  const { id } = router.query

  const handleClick = () => {
    router.push('/dashboard')
  }

  return <button onClick={handleClick}>Go</button>
}

// After (App Router)
'use client'

import { useRouter, useParams } from 'next/navigation'

export default function Page() {
  const router = useRouter()
  const params = useParams()
  const id = params.id

  const handleClick = () => {
    router.push('/dashboard')
  }

  return <button onClick={handleClick}>Go</button>
}
```

**Example 2: Dynamic Metadata**
```tsx
// Before (Pages Router)
import Head from 'next/head'

export default function BlogPost({ post }) {
  return (
    <>
      <Head>
        <title>{post.title}</title>
        <meta name="description" content={post.excerpt} />
        <meta property="og:image" content={post.image} />
      </Head>
      <article>{post.content}</article>
    </>
  )
}

// After (App Router)
import type { Metadata } from 'next'

type Props = {
  params: { slug: string }
}

export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const post = await getPost(params.slug)

  return {
    title: post.title,
    description: post.excerpt,
    openGraph: {
      images: [post.image],
    },
  }
}

export default async function BlogPost({ params }: Props) {
  const post = await getPost(params.slug)
  return <article>{post.content}</article>
}
```

**⚠️ Important Notes**

1. **Client Components**: Navigation hooks require `'use client'` directive
2. **Breaking Changes**: `next/router` is NOT compatible with App Router
3. **Incremental Migration**: Can update patterns gradually
4. **Testing**: Test each change thoroughly, especially routing

Would you like me to automatically update any of these patterns?
