# responsive-design

Responsive design review and guidance skill for Claude Code. Covers strategy (mobile-first, fluid sizing, content-driven breakpoints), implementation (CSS/SCSS, Tailwind, container queries), and framework-specific patterns (React, Vue, Next.js, Nuxt).

## Features

- **Audit** existing UI code for responsive issues (P0 layout breakage → P1 usability → P2 polish)
- **Review** CSS/SCSS, Tailwind, and framework components for cross-device compatibility
- **Guide** fluid typography, responsive spacing, image optimization, and touch targets
- **Framework-aware**: React `useMediaQuery`, Next.js SSR strategies, Vue composables, Nuxt patterns

## Install

```bash
# Clone into ~/.claude/skills/
git clone https://github.com/francisxjun-commits/responsive-design.git \
  ~/.claude/skills/responsive-design
```

Or copy just `SKILL.md` and `references/` into `~/.claude/skills/responsive-design/`.

## Triggers

This skill activates when you mention: responsive design, mobile layout, breakpoints, media queries, mobile-first, RWD, adaptive layout, screen size adaptation, "make it responsive", "mobile-friendly", 响应式, 断点, 适配移动端.

## Structure

```
├── SKILL.md                  # Core principles, workflow, review format
└── references/
    ├── tailwind.md           # Tailwind responsive patterns & pitfalls
    ├── css-scss.md           # CSS/SCSS media queries, container queries, fluid patterns
    └── frameworks.md         # React, Vue, Next.js, Nuxt strategies
```
