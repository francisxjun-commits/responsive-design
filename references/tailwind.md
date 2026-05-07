# Tailwind CSS Responsive Patterns

## Mobile-First Workflow

Tailwind's breakpoint prefixes are mobile-first. The unprefixed class is the base
(mobile) style; prefixed classes apply at that breakpoint and above:

```html
<!-- Correct: mobile-first -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3">
  <!-- 1 col on mobile, 2 cols from md up, 3 cols from lg up -->
</div>

<!-- Wrong: desktop-first thinking -->
<div class="hidden md:block">
  <!-- Hidden on mobile, visible md+ — this is sometimes correct
       for navigation menus, but "hide on mobile" should be a
       deliberate choice, not the default -->
</div>
```

## Breakpoint Prefixes

| Prefix | Min-width | Use for                          |
| ------ | --------- | -------------------------------- |
| (none) | 0         | Mobile base styles               |
| sm     | 640px     | Large phones, tablets portrait   |
| md     | 768px     | Tablets, small laptops           |
| lg     | 1024px    | Laptops, desktops                |
| xl     | 1280px    | Large desktops                   |
| 2xl    | 1536px    | Extra large screens              |

## Responsive Layout Patterns

### Grid auto-fit (no breakpoints needed)
```html
<div class="grid grid-cols-[repeat(auto-fill,minmax(280px,1fr))] gap-4">
  <!-- Cards flow naturally, no breakpoint prefix needed -->
</div>
```

### Sidebar layout
```html
<div class="flex flex-col lg:flex-row">
  <aside class="w-full lg:w-64 lg:shrink-0">Sidebar</aside>
  <main class="flex-1">Content</main>
</div>
```

### Navigation — hide on mobile, show on desktop
```html
<nav class="hidden md:flex md:gap-4">
  <a href="/">Home</a>
  <a href="/about">About</a>
</nav>
<!-- Mobile hamburger remains visible below md -->
<button class="md:hidden" aria-label="Menu"><!-- icon --></button>
```

### Responsive spacing
```html
<section class="px-4 sm:px-6 lg:px-8 py-8 md:py-12 lg:py-16">
  <!-- Padding scales up with viewport -->
</section>
```

### Responsive typography
```html
<h1 class="text-xl sm:text-2xl lg:text-3xl xl:text-4xl">
  <!-- Scale font size smoothly across breakpoints -->
</h1>

<!-- For true fluid typography, use arbitrary values with clamp: -->
<h1 class="text-[clamp(1.5rem,1rem+2.5vw,3rem)]">
```

## Responsive Flexbox Utilities

```html
<!-- Stack on mobile, row on desktop -->
<div class="flex flex-col md:flex-row gap-4">

<!-- Full-width on mobile, auto-width on desktop -->
<div class="w-full md:w-auto">

<!-- Center on mobile, left-align on desktop -->
<div class="text-center md:text-left">

<!-- Gap scales with viewport -->
<div class="flex gap-2 md:gap-4 lg:gap-6">
```

## Responsive Grid Utilities

```html
<!-- 1 col → 2 cols → 3 cols → 4 cols -->
<div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4">

<!-- Asymmetric grid -->
<div class="grid grid-cols-1 lg:grid-cols-[1fr_300px]">
  <main>Content</main>
  <aside>Sidebar</aside>
</div>
```

## Common Tailwind Responsive Pitfalls

### 1. Over-prefixing
```html
<!-- Bad: prefix on every class -->
<div class="text-sm md:text-base lg:text-lg px-2 md:px-4 lg:px-6">

<!-- Better: only what changes -->
<div class="text-sm lg:text-base px-4">
```
The rule: if a value is the same at all sizes, don't prefix it. Tailwind is verbose
enough already; over-prefixing hurts readability.

### 2. Using max-width breakpoints
Tailwind supports `max-sm:`, `max-md:`, etc. but they read backwards from mobile-first
logic. Only use them when the alternative is worse:
```html
<!-- Acceptable: hiding something only on the smallest screens -->
<aside class="max-sm:hidden">...</aside>

<!-- Prefer this instead (mobile-first equivalent): -->
<aside class="hidden sm:block">...</aside>
```

### 3. Hidden overflow without checking
```html
<!-- Bad: hides content without telling you there's a problem -->
<div class="overflow-hidden">

<!-- Better: use overflow-auto or overflow-x-auto with clear intent -->
<div class="overflow-x-auto">
```

### 4. Neglecting container queries
Tailwind 3.4+ supports container queries via `@container`:
```html
<div class="@container">
  <div class="grid grid-cols-1 @md:grid-cols-2 @lg:grid-cols-3">
    <!-- Adapts to parent container width, not viewport -->
  </div>
</div>
```
Use this for components embedded in varying-width slots.

## Custom Breakpoints

When the defaults don't fit, configure in `tailwind.config.js`:

```js
// tailwind.config.js
module.exports = {
  theme: {
    screens: {
      'phone': '480px',   // Custom named breakpoint
      'tablet': '768px',
      'laptop': '1024px',
      'desktop': '1280px',
    }
  }
}
```

## Responsive Images in Tailwind

```html
<!-- Aspect ratio prevents layout shift -->
<div class="aspect-video overflow-hidden">
  <img class="w-full h-full object-cover" src="..." alt="..." />
</div>

<!-- Conditionally load different images -->
<img class="block sm:hidden" src="mobile-hero.jpg" alt="..." />
<img class="hidden sm:block" src="desktop-hero.jpg" alt="..." />

<!-- Or use the <picture> element + Tailwind classes -->
<picture>
  <source media="(min-width: 1024px)" srcset="desktop.jpg" />
  <img class="w-full h-auto" src="mobile.jpg" alt="..." />
</picture>
```

## Review Checklist for Tailwind

- [ ] Base classes represent mobile styles (not desktop)
- [ ] No `max-sm:` / `max-md:` without a good reason
- [ ] No hardcoded widths (use `w-full`, `max-w-*`, or grid instead of `w-[1200px]`)
- [ ] Images have `max-width` protection (Tailwind's reset covers this, but check for overrides)
- [ ] Touch targets use `min-h-[44px] min-w-[44px]` on interactive elements
- [ ] Text doesn't overflow at 320px (no `truncate` on critical content)
- [ ] Layout tested at each breakpoint range, not just mobile + desktop
