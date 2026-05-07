# CSS / SCSS Responsive Patterns

## Media Queries — Mobile-First Convention

Always use `min-width`, never `max-width`. The base styles are for mobile; each query
adds complexity upward. This reads naturally: "and above this width, do this."

```css
/* Base: mobile (0px+) */
.card {
  display: flex;
  flex-direction: column;
  padding: 1rem;
}

/* Tablet portrait and up */
@media (min-width: 640px) {
  .card {
    flex-direction: row;
    padding: 1.5rem;
  }
}

/* Desktop and up */
@media (min-width: 1024px) {
  .card {
    padding: 2rem;
    max-width: 800px;
    margin-inline: auto;
  }
}
```

### When max-width IS acceptable

Only when you need to apply a style below a threshold and the base already has the
opposite. Rare, but legitimate:

```css
/* Base: sidebar visible */
.sidebar { display: block; }

/* Hide sidebar on small screens only */
@media (max-width: 639px) {
  .sidebar { display: none; }
}
```

## Container Queries

When a component lives in different-width containers (sidebar, main content, modal),
viewport media queries break. Container queries solve this:

```css
/* Define the containment context */
.card-wrapper {
  container-type: inline-size;
  container-name: card;
}

/* Query the container, not the viewport */
@container card (min-width: 400px) {
  .card {
    flex-direction: row;
  }
}

@container card (min-width: 600px) {
  .card {
    padding: 2rem;
  }
}
```

Use container queries for **reusable components** that appear in multiple layout slots.
Use viewport media queries for **page-level layout** (header, sidebar, grid-template).

## Flexbox Responsive Patterns

### Auto-fill grid (replaces explicit breakpoints)
```css
.grid {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}

/* Each item: minimum 250px, grow to fill space */
.grid > * {
  flex: 1 1 250px;
}
```

### Equal-width switcher
```css
.switcher {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}

/* Side-by-side above threshold, stacked below */
.switcher > * {
  flex-basis: calc((30rem - 100%) * 999);
  flex-grow: 1;
}
```

## CSS Grid Responsive Patterns

### Auto-fill with minmax (no media queries needed)
```css
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 1rem;
}
```

### Explicit breakpoints for layout changes
```css
.page-layout {
  display: grid;
  grid-template-areas:
    "header"
    "main"
    "sidebar"
    "footer";
  grid-template-columns: 1fr;
}

@media (min-width: 768px) {
  .page-layout {
    grid-template-areas:
      "header  header"
      "main    sidebar"
      "footer  footer";
    grid-template-columns: 1fr 300px;
  }
}
```

## Fluid Typography

```css
/* Scale smoothly between min and max font sizes */
h1 { font-size: clamp(1.5rem, 1rem + 2.5vw, 3rem); }
h2 { font-size: clamp(1.25rem, 1rem + 1.5vw, 2rem); }
h3 { font-size: clamp(1.125rem, 0.875rem + 1vw, 1.5rem); }
body { font-size: 1rem; } /* Don't clamp body - let user preference win */

/* Line-height should also scale */
h1 { line-height: 1.1; }
p { line-height: 1.5; }
```

## Responsive Spacing

```css
/* Fluid padding scales with viewport */
.section {
  padding-block: clamp(2rem, 0.5rem + 5vw, 5rem);
  padding-inline: clamp(1rem, 0.5rem + 2.5vw, 2rem);
}

/* Gap scales but stays within bounds */
.grid {
  gap: clamp(0.75rem, 0.25rem + 1.5vw, 1.5rem);
}
```

## Viewport Units — Safe Usage

```css
/* dvh = dynamic viewport height (accounts for mobile browser chrome) */
.hero { min-height: 100dvh; }

/* svh = smallest viewport height (more stable on scroll) */
.full-screen { min-height: 100svh; }

/* Avoid plain 100vh on mobile — URL bar causes jump */
/* This is a top-5 responsive bug. */
```

## Responsive Images (CSS-side)

```css
/* Prevent overflow — always include this reset */
img, video, canvas, svg {
  max-width: 100%;
  height: auto;
}

/* Aspect ratio for consistent placeholders */
.image-wrapper {
  aspect-ratio: 16 / 9;
  overflow: hidden;
}
.image-wrapper img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

/* Responsive background images */
.hero {
  background-image: url('hero-mobile.jpg');
}
@media (min-width: 768px) {
  .hero {
    background-image: url('hero-desktop.jpg');
  }
}
```

## Common SCSS Mixins

```scss
// Breakpoint mixin (mobile-first)
@mixin from($width) {
  @media (min-width: $width) {
    @content;
  }
}

// Usage
.card {
  padding: 1rem;
  @include from(768px) {
    padding: 2rem;
  }
}

// Fluid value generator
@function fluid($min, $max, $min-vw: 400, $max-vw: 1200) {
  $slope: ($max - $min) / ($max-vw - $min-vw);
  @return clamp(#{$min}px, #{$min}px + #{$slope * 100}vw, #{$max}px);
}

// Usage
.card { padding: fluid(16, 32); }
```

## Responsive Audit Checklist

When reviewing pure CSS/SCSS, check for these anti-patterns:

- [ ] `max-width` media queries (usually wrong — should be `min-width`)
- [ ] Fixed pixel widths on containers (`width: 1200px`)
- [ ] Missing `max-width: 100%` on images
- [ ] `100vh` without fallback on mobile
- [ ] `line-height` in pixels instead of unitless
- [ ] `font-size` in pixels instead of rem
- [ ] No breakpoint between 320px and desktop — at least one mid-range check
- [ ] Horizontal scrollbar at 320px viewport
- [ ] Touch targets under 44x44px
