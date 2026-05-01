# Simple Weather App

## What This Is

A frontend-only single-page web application that answers one question instantly: "What's the weather right now, and what should I expect?" Users type any city name and see current conditions plus a multi-day forecast in under 2 seconds — no account required, no ads, no bloat. The app targets a proven market gap: users who need fast, clean, accessible weather data without the clutter, tracking, and advertising that plague mainstream weather apps.

## Core Value

Deliver the current temperature and today's forecast for any city in under 2 seconds, with zero accounts, zero ads, and zero friction — the answer to "do I need an umbrella?" before the user's coffee gets cold.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] User can search for weather by city name with autocomplete suggestions (F0)
- [ ] User can detect location via optional GPS geolocation (F0)
- [ ] User can view current weather conditions: temperature, feels-like, condition, high/low, precipitation probability, humidity, wind speed (F1)
- [ ] User can toggle temperature units between °C and °F, persisted in localStorage (F1)
- [ ] User can view hourly forecast for the next 24 hours in a horizontally scrollable row (F2)
- [ ] User can view 7-day daily forecast with temperature trend chart (F3)
- [ ] Weather condition icons with day/night variants cover full WMO code range (F4)
- [ ] Condition-aware background gradient shifts by weather state and time of day (F4)
- [ ] App displays correctly on all viewport widths from 375px to 1280px+ (F5)
- [ ] Secondary weather details panel (UV, wind direction, visibility, sunrise/sunset) accessible via progressive disclosure (F6)
- [ ] Data freshness indicator and graceful offline/cached-data handling (F7)
- [ ] App meets WCAG 2.2 Level AA across all components and states (F8)
- [ ] App deployed to Vercel with HTTPS and Open-Meteo CC BY 4.0 attribution (F9)

### Out of Scope

- User accounts and saved locations — adds auth infrastructure not justified for v1 single-location use case
- Severe weather alerts and push notifications — requires persistent backend and legal review; out of scope for frontend-only app
- Historical weather data — not part of the core user question; adds API complexity without serving primary personas
- Radar maps — MapLibre/Leaflet tile server integration adds significant complexity; not core to "simple weather"
- Air quality index (AQI) — deferred to v2 to preserve focus
- Animated weather backgrounds — non-trivial to do accessibly; static condition-aware gradients ship in v1
- Multiple saved locations — requires localStorage architecture upgrade; deferred to v2
- Autoplay video, advertising, news/trending content — anti-features; never build

## Context

- **Domain research:** Over 100 million daily queries go to wttr.in (a command-line ASCII weather tool) proving latent demand for fast, no-account weather access. The gap between "fast but ugly" developer tools and "polished but bloated" consumer apps is the product opportunity.
- **Primary pain points addressed:** Slow load (4–8s on mobile), friction (account walls, permission demands), noise (ads, video autoplay), precision theatre (18.47°C vs 18°C), and poor mobile touch targets.
- **No-backend architecture:** All state in component-local state, TanStack Query cache, and localStorage. No server costs, no API key management at runtime.
- **Critical pattern:** `timezone=auto` on every Open-Meteo request is non-negotiable — without it, sunrise/sunset and hourly labels break for non-local timezones. This is the #1 failure mode in 26,000+ portfolio weather apps surveyed.
- **UI standard:** USWDS (U.S. Web Design System) — apply USWDS design tokens, components, and patterns throughout the interface for accessible, consistent, government-grade design.

## Constraints

- **Tech Stack**: React 19 + TypeScript + Vite 8 + Tailwind CSS v4 + TanStack Query v5 + Recharts — established in PRD; no backend
- **Weather API**: Open-Meteo API only (no API key required; CC BY 4.0 license; `timezone=auto` mandatory on all requests)
- **Geocoding**: Open-Meteo Geocoding API for city search; Nominatim for reverse geocoding (GPS → city name)
- **Deployment**: Vercel with HTTPS (required for Browser Geolocation API)
- **Performance**: Initial load < 2 seconds on mobile 4G; JS bundle < 300 KB gzipped
- **Accessibility**: WCAG 2.2 Level AA minimum; all interactive elements keyboard-navigable; 44px minimum touch targets
- **UI Design System**: USWDS — components, design tokens, and patterns must follow USWDS standards
- **Browser Support**: Chromium 110+, Firefox 115+, Safari 16+
- **No API key exposure**: Open-Meteo requires no key; zero keys in source or build output

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Frontend-only (no backend) | Eliminates infrastructure cost and attack surface for v1; Open-Meteo requires no API key | — Pending |
| Open-Meteo as weather data source | No API key required (eliminates #1 portfolio app pitfall); 14-day forecasts; timezone=auto support; CC BY 4.0 | — Pending |
| TanStack Query with staleTime: 10min | Prevents redundant API calls; isLoading/isError states drive UI feedback; clean cache invalidation | — Pending |
| Geocoding separate from weather fetching | Avoids coupling; enables clean cache invalidation per data type | — Pending |
| Geolocation opt-in only | Denying geolocation never produces blank screen; city search is always the primary path | — Pending |
| USWDS design system | Government-grade accessibility and consistency; aligns with project UI requirements | — Pending |
| Integer temperature display only | "18°C" not "18.47°C" — precision theatre destroys trust without adding value | — Pending |

---
*Last updated: 2026-05-01 after initialization*
