# test2 reviewer notes

## Architecture

This is a strict TypeScript React 18 application built and served with Vite (`package.json`, `vite.config.ts`). `src/App.tsx` acts as a large application/gallery registry, importing domain-oriented components from `src/components`, `src/editors`, `src/views`, and chart modules from `src/charts`. Shared reusable UI primitives live under `src/ui`; the README describes them as components consumed by widgets rather than standalone demos.

## Conventions

- Use TypeScript React components with named exports and, where applicable, a default export; chart files such as `src/charts/AccountChart.tsx` export both `AccountChart` and `default AccountChart`.
- Keep domain components in domain-specific directories and use PascalCase filenames: `src/components/OrderCard.tsx`, `src/editors/OrderEditor.tsx`, and `src/views/OrderGrid.tsx`.
- Chart modules follow a repeated domain-specific contract: `{Domain}Point`, `{Domain}ChartMode`, `{Domain}ChartProps`, `generate{Domain}Series`, and `{Domain}Chart` (`src/charts/AccountChart.tsx`, `src/charts/AlbumChart.tsx`).
- Chart inputs are optional and receive deterministic seeded fallback data via `generate…Series`; rendering uses `useMemo` for derived series, maxima, and SVG paths (`src/charts/AccountChart.tsx`).
- Chart mode values are restricted to `"bar" | "line"` and local interaction state uses `useState`; mode controls must use `type="button"` (`src/charts/AccountChart.tsx`).
- Shared UI controls should be reused from `src/ui` rather than introducing another package or stylesheet. Controlled components keep state in the consuming widget, as documented in `src/ui/README.md`.
- Form submission buttons must explicitly set `type="submit"`; shared `Button` defaults to `type="button"` (`src/ui/README.md`).
- Select options and definition-list items require unique values/IDs, and status badges must include wording rather than relying on color alone (`src/ui/README.md`).
- Code must satisfy strict TypeScript, including `noUnusedLocals` and `noUnusedParameters`; `npm run build` runs `tsc --noEmit` before `vite build` (`tsconfig.json`, `package.json`).

## Intentional non-standard choices

- Significant repetition across domain charts is intentional: each chart has its own point type, generator seed, CSS class, and exported component (`src/charts/AccountChart.tsx`, `src/charts/AlbumChart.tsx`).
- The very large import/registration surface in `src/App.tsx` is intentional for the application gallery; do not flag the number of imports alone.
- The project deliberately uses native SVG, HTML controls, and shared local UI components without charting libraries or extra stylesheets (`src/ui/README.md`, `package.json`).

## Watch out for

- Flag missing or incorrect hook dependencies when changing chart dimensions or derived SVG geometry; `path` depends on geometry such as `height` through `y`, not only on `series`, `step`, and `max` (`src/charts/AccountChart.tsx`).
- Preserve the empty-series safeguards and avoid indexing `series[hover]` without proving the hover index is valid.
- Flag non-unique React keys when adding chart points; existing charts use `p.label` as the key (`src/charts/AccountChart.tsx`).
- Flag buttons added to forms without an explicit `type`, especially when bypassing the shared `Button` convention.
- Flag new unused imports, locals, or parameters because strict compiler settings make these build failures (`tsconfig.json`).