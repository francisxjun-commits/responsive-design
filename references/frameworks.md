# Framework-Specific Responsive Patterns

## React

### Custom Hook — useMediaQuery

Avoid `useEffect` + `resize` listeners (performance). Use `matchMedia` with a hook:

```tsx
import { useState, useEffect } from 'react';

function useMediaQuery(query: string): boolean {
  const [matches, setMatches] = useState(() => {
    if (typeof window === 'undefined') return false;
    return window.matchMedia(query).matches;
  });

  useEffect(() => {
    const mql = window.matchMedia(query);
    const handler = (e: MediaQueryListEvent) => setMatches(e.matches);
    mql.addEventListener('change', handler);
    return () => mql.removeEventListener('change', handler);
  }, [query]);

  return matches;
}

// Usage
function MyComponent() {
  const isDesktop = useMediaQuery('(min-width: 1024px)');
  return isDesktop ? <DesktopLayout /> : <MobileLayout />;
}
```

### CSS Modules — Responsive Classes

```css
/* Component.module.css */
.card {
  display: flex;
  flex-direction: column;
  padding: 1rem;
}

@media (min-width: 768px) {
  .card {
    flex-direction: row;
    padding: 2rem;
  }
}
```

### Conditional rendering — Be careful

```tsx
// BAD: causes layout shift and hydration mismatch
const isMobile = useMediaQuery('(max-width: 768px)');
if (isMobile) return null; // Never do this for layout

// GOOD: render both, hide one with CSS
<aside className="hidden lg:block">...</aside>
// Or: render both, let CSS handle layout
```

### Next.js — SSR + Responsive

The server doesn't know viewport size. Four strategies:

1. **CSS-first (preferred)** — use media queries, no JS needed. Works perfectly with SSR.
2. **next/image with sizes** — the `sizes` prop helps the browser choose the right image before JS loads:
   ```tsx
   <Image
     src="/hero.jpg"
     alt="Hero"
     fill
     sizes="(max-width: 768px) 100vw, 50vw"
     className="object-cover"
   />
   ```
3. **Client-only hooks** — use `'use client'` + `useMediaQuery`, but accept that the first render
   will show the base (mobile) layout. This is actually correct behavior for mobile-first.
4. **User-Agent sniffing** (avoid) — fragile, incomplete, doesn't reflect actual window size.

### next/image responsive patterns

```tsx
// Responsive image with art direction
<picture>
  <source media="(min-width: 1024px)" srcSet="/hero-desktop.jpg" />
  <Image src="/hero-mobile.jpg" alt="Hero" width={375} height={200} />
</picture>

// Fluid-width image inside a constrained container
<div className="relative aspect-video max-w-4xl">
  <Image src="/banner.jpg" alt="Banner" fill sizes="(max-width: 768px) 100vw, 800px" />
</div>
```

## Vue 3

### Composable — useMediaQuery

```ts
// composables/useMediaQuery.ts
import { ref, onMounted, onUnmounted } from 'vue';

export function useMediaQuery(query: string) {
  const matches = ref(false);

  let mql: MediaQueryList;
  const handler = (e: MediaQueryListEvent) => { matches.value = e.matches; };

  onMounted(() => {
    mql = window.matchMedia(query);
    matches.value = mql.matches;
    mql.addEventListener('change', handler);
  });

  onUnmounted(() => {
    mql?.removeEventListener('change', handler);
  });

  return matches;
}
```

### Scoped styles — responsive

```vue
<style scoped>
.card {
  display: flex;
  flex-direction: column;
}

@media (min-width: 768px) {
  .card {
    flex-direction: row;
  }
}
</style>
```

### Conditional rendering vs CSS

Same rule as React: prefer CSS over `v-if` for responsive layout decisions.
CSS avoids layout shift, hydration issues, and JS bundle cost.

### Nuxt 3 — SSR + Responsive

Same SSR constraints as Next.js. Nuxt 3 adds:

- **@nuxt/image** — equivalent to next/image, with `sizes` and `densities` support
- **Built-in transitions** — `<Transition>` works well with responsive layout changes
- **useMediaQuery from @vueuse/core** — pre-built, SSR-safe composable:
  ```ts
  import { useMediaQuery } from '@vueuse/core';
  const isLarge = useMediaQuery('(min-width: 1024px)');
  ```

## Framework-agnostic patterns

### Data attribute approach
Instead of coupling responsive state to a specific JS framework, drive it from CSS
and read it in JS when needed:

```css
/* CSS */
body::after { display: none; content: 'mobile'; }
@media (min-width: 768px) { body::after { content: 'tablet'; } }
@media (min-width: 1024px) { body::after { content: 'desktop'; } }
```

```ts
// Read in JS (framework-agnostic)
function getBreakpoint(): string {
  return getComputedStyle(document.body, '::after').content.replace(/"/g, '');
}
```

### Avoid: Resize event listeners

`window.addEventListener('resize', ...)` fires dozens of times per second during resize
and requires manual debouncing. Use `matchMedia` listeners instead — they fire only when
a threshold is crossed.

## Review Checklist for Frameworks

- [ ] Responsive layout driven by CSS, not JS conditional rendering
- [ ] No `useEffect` + `resize` — use `matchMedia` instead
- [ ] Next.js/Nuxt images use `sizes` prop for proper SSR behavior
- [ ] `useMediaQuery` hooks handle SSR (fallback to `false` on server)
- [ ] No User-Agent detection for layout decisions
- [ ] Component adapts correctly when rendered inside different-width containers
