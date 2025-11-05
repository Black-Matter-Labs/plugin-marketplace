# Next.js Performance Auditor

A plugin for identifying performance optimization opportunities in Next.js applications.

## Features

- 🔍 Detect unnecessary "use client" directives
- 🖼️ Find image optimization opportunities
- 📦 Identify bundle size issues
- ⚡ Analyze Server/Client component boundaries
- 🎯 Prioritized recommendations with file locations

## Commands

### `/perf-audit`

Run a comprehensive performance audit on your Next.js application.

Analyzes:
- Client Component usage
- Image optimization opportunities
- Bundle size issues
- Server/Client boundary problems

**Usage:**
```bash
/perf-audit
```

### `/check-use-client`

Specifically analyze "use client" directives to find components that could be Server Components.

Identifies:
- Unnecessary "use client" directives
- Components that could benefit from Server Component rendering
- Proper vs improper client component usage

**Usage:**
```bash
/check-use-client
```

### `/check-images`

Analyze image usage for optimization opportunities.

Checks for:
- HTML `<img>` tags that should use Next.js `<Image>`
- Missing width/height attributes
- Missing priority props on above-the-fold images
- Missing alt text
- Unoptimized image formats

**Usage:**
```bash
/check-images
```

## Installation

### From Black Matter Labs Marketplace

```bash
# Add the marketplace (if not already added)
/plugin marketplace add Black-Matter-Labs/plugin-marketplace

# Install the plugin
/plugin install nextjs-performance-auditor
```

### Manual Installation

```bash
/plugin install github:Black-Matter-Labs/plugin-marketplace --path plugins/nextjs-performance-auditor
```

## What It Checks

### Client Component Analysis

Identifies components with "use client" that don't actually need client-side rendering:
- Components with no state, effects, or event handlers
- Pure presentational components
- Components that could benefit from Server Component rendering

### Image Optimization

Finds images that could be optimized:
- Regular `<img>` tags instead of Next.js `<Image>`
- Missing dimensions causing layout shift
- Large images without lazy loading
- Above-the-fold images missing `priority` prop

### Bundle Size

Detects potential bundle bloat:
- Large libraries imported on the client side
- Components that could be lazy-loaded
- Full library imports when only specific functions are needed

### Server/Client Boundaries

Catches common anti-patterns:
- Non-serializable props passed to Client Components
- Incorrect import patterns
- Mixed server/client code

## Example Output

```
🎯 Performance Audit Results

Summary:
- Total issues found: 12
- High priority: 4
- Medium priority: 8

1. Unnecessary "use client" Directives
   - app/components/card.tsx:1 - No interactivity detected
   - app/components/header.tsx:1 - Only uses props, no state

2. Image Optimization Opportunities
   - app/page.tsx:45 - Using <img> instead of <Image>
   - app/products/page.tsx:23 - Missing width/height

3. Bundle Size Optimizations
   - app/dashboard/page.tsx:5 - Import entire lodash
   - app/components/chart.tsx:12 - Heavy component could be lazy-loaded

📋 Recommended Actions
1. Remove unnecessary "use client" from 2 components
2. Replace 3 <img> tags with <Image>
3. Add dimensions to 4 images
4. Lazy-load dashboard chart component
```

## Best Practices

### Server Components by Default

Start with Server Components and only add "use client" when needed:
- Interactive elements (buttons, forms)
- Browser APIs (localStorage, window)
- React hooks (useState, useEffect)

### Image Optimization

Always use Next.js `<Image>`:
- Automatic lazy loading
- WebP conversion
- Responsive images
- Prevent layout shift

### Bundle Size

Keep client bundles small:
- Use dynamic imports for heavy components
- Import specific functions, not entire libraries
- Analyze bundle with `@next/bundle-analyzer`

## Contributing

Found a bug or have a feature request? [Open an issue](https://github.com/Black-Matter-Labs/plugin-marketplace/issues)

## License

MIT
