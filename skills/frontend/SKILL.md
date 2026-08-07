---
name: frontend
description: Modern frontend development: React, Vue, component architecture, state management, build tools. Use when building frontend apps, components, or SPAs.
trigger: \bReact\b ;; \bVue\b ;; \bAngular\b ;; \bSvelte\b ;; \bcomponent\b ;; \bSPA\b ;; \bsingle page\b ;; \bstate management\b ;; \bprops\b ;; \bhooks\b ;; \buseState\b ;; \buseEffect\b ;; \bfrontend\b ;; tạo component ;; viết component
---

# Frontend Development (Compact)

## Component Architecture
- **Single responsibility**: one component = one job.
- **Container/Presentational split**: containers fetch data, presentational render it.
- **Composition over inheritance**: pass children, use slots.
- **Props down, events up**: unidirectional data flow.

## React Patterns
```jsx
// Custom hook — extract reusable logic
function useDebounce(value, delay) {
  const [debounced, setDebounced] = useState(value);
  useEffect(() => {
    const timer = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);
  return debounced;
}

// Compound component — flexible API
<Tabs>
  <Tabs.List>
    <Tabs.Tab>Tab 1</Tabs.Tab>
    <Tabs.Tab>Tab 2</Tabs.Tab>
  </Tabs.List>
  <Tabs.Panel>Content 1</Tabs.Panel>
  <Tabs.Panel>Content 2</Tabs.Panel>
</Tabs>
```

## State Management
- **Local state**: `useState` — form inputs, toggles, UI state.
- **Shared state**: lift to nearest common ancestor or context.
- **Server state**: React Query / SWR — caching, refetch, optimistic updates.
- **Global state**: Zustand / Jotai — only when truly global (auth, theme).
- **URL state**: query params for filters, pagination, sort — shareable and bookmarkable.

## Performance
- `React.memo` for expensive pure components.
- `useMemo` / `useCallback` only when profiler shows benefit.
- Lazy load routes: `React.lazy(() => import('./Page'))`.
- Virtualize long lists: `react-window` or `@tanstack/virtual`.
- Image optimization: `srcset`, WebP, lazy loading.

## File Structure
```
src/
├── components/     # Shared UI components
├── features/       # Feature-specific (pages, hooks, types)
├── hooks/          # Shared custom hooks
├── lib/            # Utilities, API client
├── styles/         # Global styles, theme
└── types/          # TypeScript types
```

## Anti-patterns
- Prop drilling > 3 levels → use context or composition.
- `useEffect` for derived state → compute during render instead.
- Index as key → use stable unique IDs.
- Inline object/function props without memoization → re-renders.
- Giant components (>200 lines) → extract.