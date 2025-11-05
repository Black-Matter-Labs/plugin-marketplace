You are analyzing Next.js components to identify unnecessary "use client" directives.

## Task

Search the codebase for all files containing "use client" and analyze whether the directive is actually necessary.

## Analysis Criteria

For each file with "use client", check if it:

### Requires Client-Side Rendering (Keep "use client"):
- ✅ Uses React hooks: `useState`, `useEffect`, `useReducer`, etc.
- ✅ Has event handlers: `onClick`, `onChange`, `onSubmit`, etc.
- ✅ Uses browser APIs: `window`, `document`, `localStorage`, etc.
- ✅ Uses client-only libraries: DOM manipulation libraries, browser-specific packages
- ✅ Has animations or interactions requiring JavaScript

### Could Be Server Component (Remove "use client"):
- ❌ Only renders static content with props
- ❌ No state, effects, or event handlers
- ❌ No browser API usage
- ❌ Only uses server-safe operations (data fetching, etc.)

## Search Strategy

1. Find all files with "use client" directive
2. Read each file and analyze its code
3. Categorize each file as:
   - **Correctly using "use client"** - Needs client-side features
   - **Unnecessarily using "use client"** - Could be Server Component
   - **Unclear** - Needs manual review

## Output Format

Present findings as:

### 🔍 "use client" Analysis

**Files Analyzed:** X total

**✅ Correctly Using "use client" (Y files)**
Files that need client-side rendering:
- `path/to/interactive-button.tsx` - Uses onClick and useState
- `path/to/modal.tsx` - Uses useEffect and DOM portal

**⚠️ Unnecessary "use client" (Z files)**
Files that could be Server Components:
- `path/to/static-card.tsx:1` - Only renders props, no interactivity
- `path/to/layout-wrapper.tsx:1` - Pure layout component, no state
- `path/to/text-display.tsx:1` - Static content only

**❓ Needs Review (N files)**
Files requiring manual inspection:
- `path/to/complex-component.tsx` - Mixed server/client logic

### 💡 Recommendations

For files with unnecessary "use client":
1. Remove the directive to enable Server Component benefits
2. Test that no client-side features were missed
3. Consider splitting if component has both server and client logic

Would you like me to remove unnecessary "use client" directives?
