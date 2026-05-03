# Roadmap: Simple Weather App

## Overview

Four phases deliver a complete, production-quality weather SPA. Phase 1 establishes the working foundation: location search and current conditions — the core "do I need an umbrella?" answer. Phase 2 extends the data surface with hourly/7-day forecasts and the complete icon/visual system. Phase 3 hardens the app with responsive layout, the details panel, and data freshness/error handling. Phase 4 locks in WCAG AA accessibility compliance and ships to Vercel with attribution. Each phase delivers a coherent, verifiable capability; no phase depends on future work to be usable.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: Foundation** - Location search + current conditions; the core "do I need an umbrella?" answer works end-to-end
- [ ] **Phase 2: Forecasts & Visuals** - Hourly + 7-day forecasts, complete WMO icon set, and condition-aware gradients
- [ ] **Phase 3: Layout, Details & Freshness** - Responsive layout at all breakpoints, secondary details panel, offline/freshness handling
- [ ] **Phase 4: Accessibility & Deployment** - Full WCAG AA compliance, skip nav, reduced motion, live regions, and live Vercel deploy

## Phase Details

### Phase 1: Foundation
**Status**: awaiting decision
**Goal**: Users can find any city and see current weather conditions immediately — no blank screens, no stalled states
**Depends on**: Nothing (first phase)
**Requirements**: F0-01, F0-02, F0-03, F0-04, F0-05, F1-01, F1-02, F1-03, F1-04, F1-05, F1-06
**Success Criteria** (what must be TRUE):
  1. User can type a city name (2+ chars), see up to 5 autocomplete suggestions, and select one to load weather
  2. User can navigate autocomplete suggestions with arrow keys and select with Enter — mouse and keyboard both work
  3. User can tap "Use my location" to auto-detect their city via GPS; denying the prompt leaves the search fully functional with no error
  4. Current temperature (integer), feels-like, high/low, precipitation probability, humidity, wind speed, and condition label + icon are all visible above the fold on mobile (375px)
  5. °C/°F toggle on the main screen switches units instantly; preference survives page reload
  6. Skeleton loading state appears during fetch; a clear error message with retry button appears on API failure — the screen is never blank
**Plans**: 5 plans

Plans:
- [ ] 01-01-PLAN.md — Project scaffold: Vite + React 19 + TypeScript strict + Tailwind v4 + USWDS + TanStack Query + Playwright
- [ ] 01-02-PLAN.md — Data layer TDD: TypeScript interfaces, transformation utilities, localStorage helpers
- [ ] 01-03-PLAN.md — Search UI: SearchBar (centered/header), autocomplete, GPS opt-in, recent chips, F0 Playwright tests
- [ ] 01-04-PLAN.md — Current Conditions: weather hook, WeatherHero, skeleton, error state, unit toggle, F1 Playwright tests
- [ ] 01-05-PLAN.md — App integration: App.tsx, AppShell, Footer attribution, AlertBanner, full e2e integration tests

### Phase 2: Forecasts & Visuals
**Goal**: Users can see the full weather picture — 24-hour and 7-day forecast — with accurate icons and condition-aware backgrounds
**Depends on**: Phase 1
**Requirements**: F2-01, F2-02, F2-03, F3-01, F3-02, F3-03, F4-01, F4-02, F4-03
**Success Criteria** (what must be TRUE):
  1. A horizontally scrollable row of 24 hourly cards appears on the main screen — no paywall, no tab — each showing local-timezone hour label, condition icon, temperature, and precipitation probability
  2. All 7 days of daily forecast are visible without pagination; each row shows day name ("Today" for current day), icon + text label, high/low, and precipitation probability
  3. A Recharts AreaChart temperature trend renders alongside the 7-day list with an accessible sr-only table fallback
  4. Every WMO weather code (0–99) resolves to a specific named icon and text label — no generic fallbacks
  5. Icons use day/night variants driven by the API is_day field (not the browser clock); the hero uses a condition-aware gradient background with ≥4.5:1 contrast on all text
**Plans**: TBD

### Phase 3: Layout, Details & Freshness
**Goal**: The app looks and behaves correctly at every viewport size, surfaces secondary weather metrics on demand, and communicates data freshness and offline state clearly
**Depends on**: Phase 2
**Requirements**: F5-01, F5-02, F5-03, F6-01, F6-02, F6-03, F7-01, F7-02, F7-03
**Success Criteria** (what must be TRUE):
  1. App displays without horizontal overflow at 375px (mobile), 640px (tablet), and 1024px+ (desktop) using USWDS breakpoint tokens; all tap targets ≥44px at every width
  2. Layout reflows correctly at 125% browser zoom on desktop — no truncation, no overflow
  3. A collapsible USWDS accordion "Details" panel expands on a single click/tap or Enter/Space, revealing UV index, wind speed/direction, visibility, humidity, and sunrise/sunset in the searched city's local timezone
  4. "Updated X minutes ago" freshness indicator is always visible when data is loaded
  5. When offline with cached data, a warning banner shows "Showing data from X min ago"; when offline with no cache, a friendly error with retry appears — the screen is never blank
**Plans**: TBD

### Phase 4: Accessibility & Deployment
**Goal**: The app meets WCAG 2.2 Level AA across all components and states, and is live at a public HTTPS URL with Open-Meteo attribution
**Depends on**: Phase 3
**Requirements**: F8-01, F8-02, F8-03, F8-04, F8-05, F9-01, F9-02
**Success Criteria** (what must be TRUE):
  1. All interactive elements (search, GPS button, unit toggle, hourly cards, accordion, recent chips, retry button) are keyboard-reachable with logical Tab order; a skip-nav link is the first Tab stop; no keyboard traps exist
  2. USWDS 2px solid focus ring is visible on every focused interactive element via :focus-visible — not overridden anywhere
  3. Screen readers announce weather data loads and freshness updates via aria-live regions; no important state change is silent
  4. All animations and transitions (skeleton pulse, gradient shift, card hover) are disabled or replaced with static equivalents when prefers-reduced-motion is set
  5. App is live at a public HTTPS Vercel URL; Open-Meteo CC BY 4.0 attribution link is visible in the footer on every page load; auto-deploys from GitHub main branch push
**Plans**: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3 → 4

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Foundation | 0/5 | Planned | - |
| 2. Forecasts & Visuals | 0/TBD | Not started | - |
| 3. Layout, Details & Freshness | 0/TBD | Not started | - |
| 4. Accessibility & Deployment | 0/TBD | Not started | - |