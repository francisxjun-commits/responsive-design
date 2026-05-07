---
name: responsive-design
description: >-
  Responsive design review and guidance. Use this skill whenever the user mentions
  responsive design, mobile layout, breakpoints, media queries, mobile-first, RWD,
  adaptive layout, screen size adaptation, or asks to review UI code for cross-device
  compatibility. Also trigger when reviewing CSS/Tailwind/SCSS code, building new UI
  components, or when the user says things like "make it responsive", "mobile-friendly",
  "适配移动端", "响应式", "断点". Proactively check for responsive issues in any
  frontend code you write or review — don't wait for the user to ask.
---

# Responsive Design Best Practices

Guide users toward responsive, accessible, cross-device UI implementations. This skill
covers strategy (breakpoint choices, mobile-first philosophy), implementation (media
queries, container queries, fluid sizing), and review (auditing existing code for
responsive gaps).

## When this skill is active

- User writes or reviews CSS, SCSS, Tailwind, or styled-component code
- User builds a new UI component or page layout
- User asks about breakpoints, media queries, fluid typography, or responsive images
- User mentions "mobile", "tablet", "desktop" layout concerns
- User reports a layout bug on a specific screen size

## Core principles

These are the non-negotiable foundations. Reference them early when reasoning about
any responsive decision:

1. **Mobile-first**: Write base styles for the smallest viewport, then add complexity
   upward with `min-width` breakpoints. This forces prioritization of content and
   prevents the common trap of "the desktop version doesn't fit on mobile."
2. **Content decides breakpoints, not device lists**: Add a breakpoint where the
   content actually breaks, not at 768px because "that's iPad width." The market has
   hundreds of screen sizes; individual-device breakpoints rot immediately.
3. **Fluid before fixed**: Prefer `clamp()`, `%`, `fr`, `vw`, `em`/`rem` over
   hardcoded `px` values. Screens vary — let the browser do the math.
4. **Touch targets >= 44x44px**: Interactive elements smaller than this are physically
   hard to tap. This is both a UX and an accessibility requirement (WCAG 2.5.5).
5. **Test at every breakpoint range**: A layout that works at 375px and 1440px can
   still be broken at 768px. Check the in-between states.

## Workflow

When asked to review or implement responsive design:

1. **Identify the tech stack** — Pure CSS? Tailwind? CSS-in-JS? This determines which
   reference file to consult. Read the relevant reference:
   - `references/css-scss.md` — Media queries, container queries, fluid patterns
   - `references/tailwind.md` — Breakpoint prefixes, responsive utilities, common pitfalls
   - `references/frameworks.md` — React/Vue/Next.js/Nuxt component-level strategies

2. **Audit the current state** (if reviewing existing code):
   - Are base styles mobile-first or desktop-first?
   - Are there hardcoded widths that should be fluid?
   - Do images have proper `srcset`/`sizes` or at least `max-width: 100%`?
   - Are touch targets large enough on small screens?
   - Does text remain readable (no horizontal scroll at 320px)?

3. **Fix issues in priority order**:
   - **P0 — Layout breakage**: horizontal overflow, overlapping elements, cropped content
   - **P1 — Usability**: touch targets too small, text too small/large, unreadable contrast
   - **P2 — Polish**: spacing imbalance, font scale not fluid, images not optimized

## Recommended breakpoints

These are sensible defaults, not dogma. Adjust to the project's content:

| Label    | Min-width | Typical devices                      |
| -------- | --------- | ------------------------------------ |
| Base     | 0         | All phones (mobile-first default)    |
| Small    | 640px     | Large phones, small tablets          |
| Medium   | 768px     | Tablets in portrait                  |
| Large    | 1024px    | Tablets in landscape, small laptops  |
| XL       | 1280px    | Desktops                             |
| 2XL      | 1536px    | Large desktops                       |

Tailwind users: these map to `sm:`, `md:`, `lg:`, `xl:`, `2xl:` respectively.

## Fluid sizing quick reference

Prefer these over fixed values wherever content permits:

```
/* Fluid font size: 16px → 24px between 400px → 1200px viewports */
font-size: clamp(1rem, 0.5rem + 2vw, 1.5rem);

/* Fluid spacing: 16px → 48px */
padding: clamp(1rem, -0.5rem + 6vw, 3rem);

/* Fluid width: 300px → 600px */
width: clamp(18.75rem, 3.125rem + 50vw, 37.5rem);
```

Formula: `clamp(MIN, PREFERRED, MAX)` where PREFERRED = `MINrem + (MAXpx - MINpx) / (MAXvw - MINvw) * 100vw`

## Responsive images

Always include these three patterns when images appear in reviewed code:

```html
<!-- 1. Resolution switching (same aspect ratio, different densities) -->
<img srcset="img-1x.jpg 1x, img-2x.jpg 2x" src="img-1x.jpg" alt="..." />

<!-- 2. Art direction + sizes (different crops per breakpoint) -->
<picture>
  <source media="(min-width: 1024px)" srcset="desktop.jpg" />
  <source media="(min-width: 640px)" srcset="tablet.jpg" />
  <img src="mobile.jpg" alt="..." loading="lazy" />
</picture>

<!-- 3. CSS-only base guard -->
img { max-width: 100%; height: auto; }
```

## Accessibility in responsive design

- **Touch targets**: minimum 44x44px at every breakpoint (WCAG 2.5.5)
- **Focus indicators**: visible and 2px+ thick at all sizes
- **Readable without zoom**: no horizontal scrollbar at 320px width (WCAG 1.4.10)
- **Contrast**: maintain 4.5:1 (AA) ratio regardless of layout shifts
- **Content doesn't disappear**: never use `display: none` to "fix" mobile layouts
  — restructure or collapse into an accordion instead

## Review output format

When reviewing responsive design, structure feedback as:

```
## Responsive Design Review

### Critical (P0)
- [specific issue with file:line and fix recommendation]

### Usability (P1)
- [specific issue with file:line and fix recommendation]

### Polish (P2)
- [specific issue with file:line and fix recommendation]

### Summary
[1-2 sentences on overall responsive quality]
```
