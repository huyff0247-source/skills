---
name: web-performance
description: Web performance optimization: Core Web Vitals, loading strategies, bundle optimization, caching, images. Use when optimizing page speed or debugging slow websites.
trigger: \bweb performance\b ;; \bpage speed\b ;; \bcore web vitals\b ;; \bLCP\b ;; \bINP\b ;; \bCLS\b ;; \blighthouse\b ;; \bslow website\b ;; \btối ưu web\b ;; \bload time\b ;; \bbundle size\b ;; \bweb.*chậm\b ;; \bweb.*slow\b ;; \bweb.*nhanh\b ;; \bweb.*tối ưu\b ;; \btrang web.*chậm\b ;; \btrang web.*tối ưu\b
---

# Web Performance (Compact)

## Core Web Vitals (Google Ranking Factor)
| Metric | Good | Needs Work | Poor |
|---|---|---|---|
| **LCP** (Largest Contentful Paint) | ≤2.5s | ≤4.0s | >4.0s |
| **INP** (Interaction to Next Paint) | ≤200ms | ≤500ms | >500ms |
| **CLS** (Cumulative Layout Shift) | ≤0.1 | ≤0.25 | >0.25 |

## Loading Strategies
1. **Critical CSS inline** — above-the-fold styles in `<head>`, rest deferred.
2. **Font optimization**: `font-display: swap`, subset fonts, preload.
3. **Image strategy**: WebP/AVIF, `srcset`, lazy load (`loading="lazy"`), explicit width/height.
4. **Code splitting**: dynamic `import()`, route-based splitting.
5. **Preload/Prefetch**: `<link rel="preload">` for critical assets, `<link rel="prefetch">` for next-page resources.

## Bundle Optimization
```js
// Tree-shakeable imports
import { debounce } from 'lodash-es';     // ✅ only debounce
import _ from 'lodash';                    // ❌ entire lodash

// Dynamic import
const HeavyChart = lazy(() => import('./HeavyChart'));

// Analyze bundle
// npm: npx vite-bundle-visualizer or webpack-bundle-analyzer
```

## Caching Strategy
```
HTML        — no-cache (always validate)
CSS/JS      — max-age=1year + content hash in filename
Images      — max-age=1year + content hash
API data    — stale-while-revalidate (SWR)
Fonts       — max-age=1year (immutable)
```

## Quick Wins (Highest Impact)
1. **Compress images** — 60% of page weight. Use WebP, lazy load.
2. **Enable compression** — gzip/brotli on server.
3. **Reduce render-blocking JS** — defer/async non-critical scripts.
4. **CDN** for static assets.
5. **Remove unused CSS/JS** — PurgeCSS, tree shaking.
6. **Preconnect** to third-party origins: `<link rel="preconnect" href="https://api.example.com">`.

## Measurement Tools
- **Lighthouse** (Chrome DevTools) — overall score + opportunities.
- **WebPageTest** — filmstrip view, waterfall, real device testing.
- **Chrome Performance tab** — runtime performance, JS profiling.
- **Bundle analyzer** — what's taking space.

## Anti-patterns
- Loading full moment.js for one date format (use `date-fns` or native `Intl`).
- Synchronous third-party scripts in `<head>`.
- Serving 4000px images for 400px containers.
- `font-display: block` — users see nothing until font loads.