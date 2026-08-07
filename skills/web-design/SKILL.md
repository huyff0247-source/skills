---
name: web-design
description: Web design principles: layout, color, typography, UX patterns, responsive design. Use when designing web pages, UI components, or landing pages.
trigger: \bthiết kế web\b ;; \bweb design\b ;; \bgiao diện\b ;; \bUI design\b ;; \bUX design\b ;; \blayout\b ;; \blanding page\b ;; \bresponsive\b ;; \bmobile first\b ;; \bwireframe\b ;; \bmockup\b ;; \bthiết kế giao diện\b ;; \bthiết kế trang\b ;; \bthiết kế landing\b
---

# Web Design (Compact)

## Layout System
- **12-column grid** — industry standard, flexible for any layout.
- **Max-width container**: 1200px for desktop, fluid below.
- **Spacing scale**: 4, 8, 16, 24, 32, 48, 64, 96, 128px (multiples of 4).
- **Z-pattern** for text-heavy pages, **F-pattern** for scanning.

## Responsive Breakpoints
```css
/* Mobile first — write base styles for mobile, override up */
/* sm */ @media (min-width: 640px)  { ... }
/* md */ @media (min-width: 768px)  { ... }
/* lg */ @media (min-width: 1024px) { ... }
/* xl */ @media (min-width: 1280px) { ... }
```

## Typography
- **Font stack**: system fonts first (`-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto...`).
- **Scale**: 12/14/16/18/20/24/30/36/48/60/72px.
- **Line height**: 1.5 for body, 1.2 for headings.
- **Max line length**: 65-75 characters for readability.
- **Hierarchy**: max 3 levels (h1→h2→h3). Use size, weight, color — not more fonts.

## Color System
- **60-30-10 rule**: 60% neutral, 30% primary, 10% accent.
- **Contrast ratio**: ≥4.5:1 for body text (WCAG AA).
- **Semantic colors**: success(green), warning(amber), error(red), info(blue).
- **Dark mode**: use CSS variables, toggle on `[data-theme="dark"]`.

## Visual Hierarchy
1. Size — bigger = more important.
2. Color — bright/warm advances, cool/dark recedes.
3. Contrast — high contrast draws eye first.
4. Spacing — isolated elements get attention.
5. Alignment — misaligned elements feel broken.

## UX Patterns
- **Above the fold**: value prop + CTA visible without scrolling.
- **Progressive disclosure**: show essentials, reveal details on demand.
- **Empty states**: guide user what to do, not just "no results".
- **Loading states**: skeleton screens > spinners (perceived performance).
- **Error states**: what happened + how to fix + next step.

## Anti-patterns
- Lorem ipsum in production.
- Pure black (#000) on pure white (#fff) — too harsh, use #111 on #fafafa.
- Tiny click targets (< 44x44px on mobile).
- Text over busy images without overlay.
- Hamburger menu on desktop.