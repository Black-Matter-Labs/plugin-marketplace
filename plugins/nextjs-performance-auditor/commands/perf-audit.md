You are running a comprehensive Next.js performance audit. Analyze the codebase and identify optimization opportunities in the following areas:

## 1. Client Component Analysis
Search for components with "use client" directive and evaluate:
- Could this component be a Server Component instead?
- Does it use browser-only APIs (localStorage, window, document)?
- Does it have event handlers or state that requires client-side rendering?
- Is "use client" used unnecessarily (component has no interactivity)?

## 2. Image Optimization
Find all image usage (`<img>`, `<Image>`) and check:
- Are regular `<img>` tags used instead of Next.js `<Image>`?
- Are images missing width/height attributes?
- Could images benefit from lazy loading?
- Are images in optimal formats (WebP, AVIF)?

## 3. Bundle Size Analysis
Identify potential bundle bloat:
- Large third-party packages imported on client side
- Entire libraries imported when only specific functions are needed
- Missing dynamic imports for heavy components
- Client Components that could be lazy-loaded

## 4. Server/Client Boundary Issues
Look for anti-patterns:
- Server Components importing Client Components incorrectly
- Props passed from Server to Client Components that aren't serializable
- Mixing server and client code incorrectly

## Output Format

Present your findings in a structured report:

### 🎯 Performance Audit Results

**Summary:**
- Total issues found: X
- High priority: Y
- Medium priority: Z

**1. Unnecessary "use client" Directives**
List files with "use client" that could be Server Components:
- `path/to/component.tsx:1` - No interactivity detected, can be Server Component
- `path/to/another.tsx:1` - Only uses props, no state/effects/handlers

**2. Image Optimization Opportunities**
- `path/to/page.tsx:45` - Using `<img>` instead of `<Image>`
- `path/to/component.tsx:23` - Missing width/height on Image component

**3. Bundle Size Optimizations**
- `path/to/file.tsx:5` - Importing entire lodash library, use specific imports
- `path/to/heavy.tsx:12` - Large component could be lazy-loaded with dynamic import

**4. Server/Client Boundary Issues**
- `path/to/server.tsx:20` - Passing non-serializable prop to Client Component

### 📋 Recommended Actions

Prioritize fixes in this order:
1. [High Priority Items]
2. [Medium Priority Items]
3. [Low Priority Items]

Would you like me to fix any of these issues automatically?
