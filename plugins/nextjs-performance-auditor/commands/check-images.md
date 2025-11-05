You are analyzing image usage in a Next.js application to identify optimization opportunities.

## Task

Search the codebase for image usage and identify optimization opportunities to improve performance and Core Web Vitals.

## What to Look For

### 1. HTML `<img>` Tags Instead of Next.js `<Image>`
- Search for `<img` tags in .tsx, .jsx files
- These should use `next/image` for automatic optimization

### 2. Missing Next.js Image Import
- Check if files using `<Image>` have imported it: `import Image from 'next/image'`

### 3. Missing Width/Height Attributes
- `<Image>` components without `width` and `height` props
- This causes Cumulative Layout Shift (CLS) issues
- Exception: Images with `fill` prop don't need width/height

### 4. Missing Priority Prop
- Large "above the fold" images without `priority` prop
- These should be prioritized for LCP (Largest Contentful Paint)

### 5. Unoptimized Image Formats
- Check for large PNG/JPG files in public/images
- Could be converted to WebP/AVIF

### 6. Missing Alt Text
- Accessibility issue: images without alt attributes

## Search Strategy

1. Find all `.tsx`, `.jsx` files
2. Search for `<img` and `<Image` tags
3. Analyze each usage for optimization opportunities
4. Check public/images directory for large unoptimized images

## Output Format

Present findings as:

### 🖼️ Image Optimization Analysis

**Images Found:** X total

**🚨 High Priority Issues**

**Using HTML `<img>` instead of Next.js `<Image>` (Y instances)**
Replace with next/image for automatic optimization:
- `app/components/hero.tsx:45` - `<img src="/hero.jpg" />`
- `app/products/page.tsx:23` - `<img src={product.image} />`

**Missing Width/Height (Z instances)**
Add dimensions to prevent layout shift:
- `app/components/card.tsx:12` - `<Image src="/card.jpg" />` missing width/height
- `app/gallery/page.tsx:34` - `<Image src={photo} />` missing dimensions

**⚠️ Medium Priority Issues**

**Missing Priority Prop (N instances)**
Add priority for above-the-fold images:
- `app/page.tsx:10` - Hero image should have `priority` prop
- `app/landing/page.tsx:15` - Banner image needs `priority`

**Missing Alt Text (M instances)**
Add alt text for accessibility:
- `app/components/logo.tsx:5` - Image missing alt attribute

**💡 Low Priority Optimizations**

**Large Unoptimized Images**
Consider converting to WebP/AVIF:
- `public/images/hero.png` - 2.5MB, could be optimized
- `public/images/background.jpg` - 1.8MB, convert to WebP

### 📋 Recommended Actions

1. **Replace `<img>` with `<Image>`** - Automatic optimization, lazy loading
2. **Add width/height** - Prevent layout shift, improve CLS
3. **Add `priority` to hero images** - Improve LCP score
4. **Add alt text** - Improve accessibility and SEO
5. **Optimize large images** - Reduce bundle size

### Example Fix

```tsx
// Before
<img src="/hero.jpg" />

// After
import Image from 'next/image'

<Image
  src="/hero.jpg"
  alt="Hero image"
  width={1200}
  height={600}
  priority
/>
```

Would you like me to fix these image optimization issues?
