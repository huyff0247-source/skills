---
name: css-styling
description: CSS & styling: Tailwind, CSS modules, animations, layouts, responsive patterns. Use when styling web pages, building CSS, or creating visual effects.
trigger: \bđẹp\b.*(web|CSS|style|giao diện|trang|button|component|landing|UI|UX) ;; (web|CSS|style|giao diện|trang|button|component).*\bđẹp\b ;; \bCSS\b ;; \bTailwind\b ;; \bSCSS\b ;; \bSASS\b ;; \banimation\b ;; \btransition\b ;; \bflexbox\b ;; \bgrid\b ;; \bresponsive\b ;; \bdark mode\b ;; \btheme\b ;; \bbeautiful\b ;; \btrang trí\b ;; \bstyling\b ;; \bCSS animation\b ;; \bCSS transition\b ;; \bCSS layout\b ;; \bstyle cho web\b ;; \bstyle cho trang\b ;; \bstyle cho button\b ;; \bstyle cho component\b
---

# CSS & Styling (Compact)

## Layout Patterns
```css
/* Centering — the 3 ways */
/* 1. Flexbox (1D) */
.center-flex { display: flex; justify-content: center; align-items: center; }

/* 2. Grid (2D) */
.center-grid { display: grid; place-items: center; }

/* 3. Absolute (overlay) */
.center-absolute { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); }

/* Holy Grail Layout */
body { display: grid; grid-template: auto 1fr auto / 1fr; min-height: 100vh; }
```

## Flexbox Cheatsheet
| Property | Values |
|---|---|
| `justify-content` | start, end, center, space-between, space-around, space-evenly |
| `align-items` | start, end, center, stretch, baseline |
| `flex-direction` | row, column, row-reverse, column-reverse |
| `flex-wrap` | nowrap, wrap, wrap-reverse |
| `gap` | any length (prefer over margin hacks) |

## Grid Cheatsheet
```css
.grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 1rem; }
/* auto-fit: fills row, auto-fill: adds empty tracks */
```

## Tailwind Quick Reference
- Layout: `flex`, `grid`, `container`, `mx-auto`
- Spacing: `p-{n}`, `m-{n}`, `gap-{n}` (n = 0.25rem × n)
- Sizing: `w-{n}`, `h-{n}`, `max-w-{size}`
- Typography: `text-{size}`, `font-{weight}`, `leading-{n}`
- Colors: `bg-{color}-{shade}`, `text-{color}-{shade}`
- Responsive: `sm:`, `md:`, `lg:`, `xl:` prefixes
- Dark mode: `dark:` prefix

## Animations
```css
/* Smooth hover */
.card { transition: transform 0.2s ease, box-shadow 0.2s ease; }
.card:hover { transform: translateY(-4px); box-shadow: 0 12px 24px rgba(0,0,0,0.15); }

/* Fade in */
@keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
.fade-in { animation: fadeIn 0.4s ease forwards; }

/* Skeleton loading */
.skeleton { background: linear-gradient(90deg, #e0e0e0 25%, #f0f0f0 50%, #e0e0e0 75%); background-size: 200% 100%; animation: shimmer 1.5s infinite; }
@keyframes shimmer { 0% { background-position: 200% 0; } 100% { background-position: -200% 0; } }
```

## CSS Variables (Design Tokens)
```css
:root {
  --color-primary: #3b82f6;
  --color-bg: #ffffff;
  --color-text: #111827;
  --radius: 8px;
  --shadow: 0 1px 3px rgba(0,0,0,0.1);
}
[data-theme="dark"] {
  --color-bg: #0f172a;
  --color-text: #e2e8f0;
}
```

## Anti-patterns
- `!important` — fix specificity instead.
- Deep nesting (>3 levels) — flat selectors are faster and clearer.
- Fixed pixel widths everywhere — use `max-width`, `%`, `vw`, `clamp()`.
- `display: none` for accessibility — use visually-hidden pattern.
- Animating `width/height/top/left` — use `transform` and `opacity` (GPU-accelerated).