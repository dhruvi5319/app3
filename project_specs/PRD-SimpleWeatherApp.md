# Product Requirements Document
## Simple Weather App

**Version:** 1.1
**Date:** 2026-05-01
**Status:** Active
**Confidence:** HIGH (based on PROJECT.md, research/SUMMARY.md, ref_docs/PRD.md)

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Problem Statement](#2-problem-statement)
3. [Product Vision](#3-product-vision)
4. [Target Users & Personas](#4-target-users--personas)
5. [Technical Architecture](#5-technical-architecture)
6. [Feature Requirements](#6-feature-requirements)
7. [Non-Functional Requirements](#7-non-functional-requirements)
8. [Success Metrics](#8-success-metrics)
9. [Out of Scope](#9-out-of-scope)
10. [Risks & Mitigations](#10-risks--mitigations)
11. [Feature Index](#11-feature-index)

---

## 1. Executive Summary

The Simple Weather App is a frontend-only single-page web application that answers one question instantly: "What's the weather right now, and what should I expect?" Users type any city name and see current conditions plus a multi-day forecast in under 2 seconds — no account required, no ads, no bloat. Built on USWDS (U.S. Web Design System) design tokens and components, the app delivers government-grade accessibility and visual consistency while targeting a proven market gap: over 100 million daily queries go to a command-line ASCII tool (`wttr.in`) precisely because mainstream weather apps are slow, invasive, and cluttered. This product delivers mainstream-quality weather information with the speed and simplicity of a developer tool — wrapped in a clean, accessible USWDS interface.

---

## 2. Problem Statement

Weather information is universally needed but poorly delivered. The dominant consumer apps (Weather.com, AccuWeather) have become advertising platforms first and weather tools second — they load slowly, require accounts for basic features, autoplay video, and bury the data users actually want under layers of promoted content. The core user need is simple: **answer "do I need an umbrella?" in under 3 seconds**.

The developer-tool alternatives (wttr.in, command-line tools) prove massive latent demand for fast, no-account weather access — but they lack the visual clarity and usability that mainstream audiences require. The gap between "fast but ugly" and "polished but bloated" is wide open, and USWDS provides the design foundation to bridge it accessibly and consistently.

Key pain points this product addresses:

- **Speed**: Mainstream apps take 4–8 seconds to load useful data on mobile due to ads and tracking scripts
- **Friction**: Account creation prompts, notification permission demands, and location auto-detection paywalls gate basic features
- **Noise**: Sponsored content, news feeds, and video autoplay compete for attention in apps where users just want temperature
- **Precision theatre**: Showing "18.47°C" instead of "18°C" destroys trust without adding value
- **Mobile frustration**: Touch targets too small, forecast cards overflow, unit toggle buried in settings menus
- **Inconsistent design**: Arbitrary custom UI that lacks accessibility guarantees — USWDS components carry proven WCAG compliance out of the box

---

## 3. Product Vision

**Vision statement:** A weather app so fast and clean that checking the weather feels like reading a clock — instant, effortless, always correct.

**Strategic goals:**

- Deliver the core weather answer (current temp + conditions) within 2 seconds of search on a mobile connection
- Require zero user accounts, zero API keys exposed to users, and zero permissions except optional geolocation
- Achieve feature parity with major apps on the data users actually use while eliminating everything they do not
- Build to WCAG 2.2 Level AA accessibility standards from day one — not as a retrofit — using USWDS components as the accessibility foundation
- Deploy as a fully static frontend with no backend infrastructure or recurring server cost
- Apply USWDS design tokens, components, and patterns as the governing design system for all UI decisions
- Establish the correct data-layer patterns (timezone handling, caching, error states) before adding visual polish — the #1 failure mode in the 26,000+ portfolio weather apps surveyed

---

## 4. Target Users & Personas

### Persona A: The Casual Checker
**Who they are:** Anyone deciding what to wear or whether to bring an umbrella. They open weather apps 1–3 times per day, most often on mobile, most often in the morning.

**What they need:** Current temperature (large and dominant), feels-like temperature, today's high/low, and a precipitation probability — all visible without scrolling.

**What frustrates them:** Slow load times, ads, notification prompts, account walls, and needing to hunt for the °C/°F toggle.

**How this app serves them:** Hero section displays the core data pair (temperature + condition) above the fold with a visible unit toggle on the main screen. USWDS typography scale ensures legibility at all sizes. No account. No ads.

---

### Persona B: The Commuter
**Who they are:** Daily commuters planning travel around weather windows. They check weather 2–4 times daily, often quickly between tasks, and care about hourly changes during their commute window.

**What they need:** Hourly forecast for the next 24 hours, precipitation probability per hour, and a quick scan of whether conditions change during their commute.

**What frustrates them:** Having to navigate multiple screens to get hourly data, or discovering the hourly view is paywalled.

**How this app serves them:** Horizontally scrollable hourly forecast row is a first-class component — visible on the main screen, not behind a tab or paywall. USWDS card components provide consistent, touch-friendly forecast cards with guaranteed 44px targets. Precipitation probability is shown on every hourly card.

---

### Persona C: The Outdoor Enthusiast
**Who they are:** Hikers, cyclists, gardeners, and weekend athletes who need to plan activities around weather windows. They check 3–7 day forecasts regularly and care about secondary metrics (wind speed, UV index, sunrise/sunset).

**What they need:** 7-day daily forecast, secondary metrics (UV, wind, visibility, sunrise/sunset), and enough forecast depth to plan a weekend activity.

**What frustrates them:** Apps that hide the 7-day view behind an account, show only 3 days on mobile, or bury wind/UV data in hard-to-find settings.

**How this app serves them:** 7-day daily forecast is a primary component. Secondary metrics (UV, wind direction, visibility, sunrise/sunset) are accessible via a USWDS accordion-style progressive-disclosure "Details" panel — present but not cluttering the primary view.

---

## 5. Technical Architecture

The app is a single-page application with no backend. All state lives in component-local state, TanStack Query's cache, and `localStorage`. There are no servers to operate, no API keys to manage at runtime, and no user data stored beyond browser-local preferences. The UI layer is governed by USWDS design tokens and components, implemented via Tailwind CSS v4 custom configuration aligned to the USWDS token set.

| Layer | Technology | Rationale |
|---|---|---|
| Framework | React 19 + TypeScript | Best ecosystem for data fetching, charting, and state management; TanStack Query integration |
| Build Tool | Vite 8 | Fast HMR, `react-ts` template, static `dist/` output for zero-config Vercel deploy |
| Styling | Tailwind CSS v4 + USWDS Design Tokens | Vite plugin; utility-first layout configured with USWDS color, spacing, and typography tokens |
| Design System | USWDS (U.S. Web Design System) | Government-grade accessibility; design tokens, components, and patterns govern all UI decisions |
| Weather Data | Open-Meteo API | No API key required (eliminates #1 portfolio app pitfall); 14-day forecasts; `timezone=auto`; CC BY 4.0 |
| Data Fetching | TanStack Query v5 | `staleTime: 10min` prevents redundant calls; `isLoading`/`isError` states drive UI feedback |
| Charting | Recharts | React-native `AreaChart` for temperature trend; `BarChart` for precipitation probability |
| Geocoding | Open-Meteo Geocoding API | City name → lat/lon with autocomplete; no key required |
| Reverse Geocoding | Nominatim | GPS lat/lon → city name when geolocation is used; no key required |
| Deployment | Vercel | Zero-config HTTPS deploy from GitHub; HTTPS required for Geolocation API |

**USWDS Integration Notes:**

- USWDS design tokens (color, spacing, typography, border-radius) are mapped to Tailwind CSS v4 custom properties to enable utility-class-driven layouts that remain token-compliant
- USWDS component patterns (search input, cards, accordion, banner/footer) are used directly or adapted as React components that preserve ARIA roles and keyboard behavior
- All color choices for condition-aware backgrounds are validated against USWDS color token pairs with known contrast ratios before implementation
- USWDS `usa-` class naming convention is preserved on adapted components to maintain semantic clarity

**Key architecture decisions:**

- **Geocoding is separate from weather fetching.** City search (Open-Meteo Geocoding → lat/lon) and weather data (lat/lon → Open-Meteo Forecast) are distinct API calls cached independently. This avoids coupling and enables clean cache invalidation.
- **`timezone=auto` is set on every Open-Meteo request from day one.** This is non-negotiable — without it, sunrise/sunset, hourly labels, and all time-dependent displays break for locations outside the user's local timezone.
- **Geolocation is opt-in, never a gate.** The GPS button is an enhancement. City search is always the primary path. Denying geolocation never produces a blank screen.
- **No backend, ever (v1).** All caching is TanStack Query + `localStorage`. This eliminates infrastructure cost and attack surface for a v1 product.
- **Integer temperature display only.** "18°C" not "18.47°C" — precision theatre destroys trust without adding value; enforced in the API response transformation layer.

---

## 6. Feature Requirements

### F0: Location Search & Detection (REQ-01)

**Description:** Users find weather for any location via city name search with autocomplete suggestions, or via optional GPS geolocation. The location bar is always visible and is the primary entry point into the app. The search input follows the USWDS search component pattern with appropriate ARIA labeling, focus management, and keyboard navigation.

**Capabilities:**
- USWDS-styled search input (`usa-search` pattern) triggers autocomplete suggestions from the Open-Meteo Geocoding API after 2+ characters are typed
- Autocomplete suggestion list follows USWDS combo-box or list-box pattern with keyboard-navigable options (arrow keys, Enter to select, Escape to dismiss)
- Selecting a suggestion loads weather data for that location immediately
- GPS opt-in button appears alongside the search input (USWDS `usa-button--outline` style); tapping it requests browser geolocation permission
- If geolocation permission is denied or unavailable, the search input remains fully functional with no error state or blank screen
- Reverse geocoding via Nominatim translates GPS coordinates into a human-readable city name for display
- Recent location searches persist in `localStorage` and appear as quick-select chips (USWDS tag pattern) below the search input

**Priority:** P0 — Critical (MVP requirement; all other features depend on location resolution)

---

### F1: Current Conditions Display (REQ-02)

**Description:** The hero section of the app shows the current weather snapshot for the selected location. This is the primary answer to the core user question and must be visible above the fold on both mobile and desktop. Typography uses USWDS type scale; spacing uses USWDS spacing tokens to ensure visual hierarchy without custom overrides.

**Capabilities:**
- Current temperature displayed large and dominant using USWDS `font-size-3xl` or equivalent token; integer values only (e.g., "18°C", never "18.47°C")
- Feels-like temperature displayed as a secondary data point adjacent to current temp using USWDS `font-size-lg`
- Weather condition shown with both an icon and a text label (e.g., "Partly Cloudy") — never icon-only (WCAG 1.4.1)
- Icons use day/night variants — a sun icon never appears at 9pm
- Today's high and low temperatures displayed as a paired value
- Precipitation probability for the current day displayed as a percentage
- Humidity and wind speed displayed as supporting data points
- °C/°F unit toggle (USWDS button group or segmented control pattern) visible on the main screen — not buried in settings; preference persisted in `localStorage`
- Skeleton loading state (USWDS placeholder pattern) displayed while data is fetching; USWDS `usa-alert--error` component shown on API failure; never a blank screen

**Priority:** P0 — Critical (MVP requirement)

---

### F2: Hourly Forecast (REQ-03 partial, REQ-04)

**Description:** A horizontally scrollable row of forecast cards showing weather conditions for each of the next 24 hours. Cards follow the USWDS card component pattern to ensure consistent padding, border treatment, and focus styles. This serves commuters and anyone planning activities within the current day.

**Capabilities:**
- Horizontal scroll row of USWDS-styled cards, each showing: hour label, condition icon (day/night variant), temperature, and precipitation probability percentage
- Minimum 44px touch targets on all interactive card elements (per WCAG 2.5.8 and USWDS touch target guidance)
- Precipitation probability shown on every card — never omitted
- Cards derived from the same Open-Meteo API response as current conditions (no additional API call required)
- Day/night icon variant logic applied correctly per the location's local timezone
- Scroll container uses `role="region"` with an `aria-label` of "Hourly forecast" for screen reader landmark navigation

**Priority:** P1 — High (differentiator; commuter persona primary feature)

---

### F3: 7-Day Daily Forecast (REQ-03)

**Description:** A vertical list of daily forecast rows covering the next 7 days. This is the planning view for outdoor enthusiasts and anyone looking beyond today. Rows use USWDS table or list patterns for semantic structure and consistent spacing.

**Capabilities:**
- 7 rows showing: abbreviated day name, condition icon (representative daytime icon), high temperature, low temperature, and precipitation probability percentage
- Recharts `AreaChart` temperature trend visualization rendered alongside or below the daily list, showing the temperature curve across the week at a glance
- Precipitation probability shown on every daily row — never omitted
- High/low pair displayed consistently (high first, then low)
- Data sourced from the same Open-Meteo API response as current conditions
- List region uses `role="region"` with `aria-label="7-day forecast"` for landmark navigation

**Priority:** P0 — Critical (MVP requirement; directly satisfies REQ-03)

---

### F4: Weather Icons & Visual Indicators (REQ-04)

**Description:** All weather data is accompanied by recognizable condition icons that communicate weather state at a glance. Icons adapt to time of day and cover all WMO weather code states returned by the Open-Meteo API. Condition-aware background gradients use USWDS color tokens where feasible to maintain contrast guarantees.

**Capabilities:**
- Icon set covers the full WMO weather interpretation code range (0–99): clear, cloudy, foggy, drizzle, rain, freezing rain, snow, shower, thunderstorm variants
- Day and night variants for all clear/partly-cloudy/overcast states — sun icon never shows after sunset, moon/stars icon used for night conditions
- Icons used consistently across current conditions, hourly forecast, and daily forecast components
- Condition-aware background gradient in the current conditions hero shifts to reflect weather state and time of day (e.g., deep navy at night, sky blue on clear day, muted grey on overcast); color palette derived from or mapped to USWDS color tokens where applicable
- All condition + time-of-day background combinations achieve WCAG 1.4.3 minimum 4.5:1 contrast ratio with overlaid text — validated against USWDS contrast guidelines before implementation

**Priority:** P0 — Critical (MVP requirement; REQ-04)

---

### F5: Responsive Layout — Desktop & Mobile (REQ-05)

**Description:** The app is built mobile-first and displays correctly at all standard viewport widths from 375px (mobile) to 1280px+ (desktop) without horizontal overflow, truncated labels, or unusable tap targets. Breakpoints align with USWDS responsive grid breakpoints (`mobile-lg`, `tablet`, `desktop`) to ensure design system consistency.

**Capabilities:**
- Mobile layout (375px–767px / USWDS `mobile-lg` and below): single-column stack; all data visible without horizontal overflow; all tap targets ≥ 44px
- Tablet layout (768px–1023px / USWDS `tablet`): optional two-column layout for current conditions + details panel
- Desktop layout (1024px+ / USWDS `desktop`): wider layout with forecast and details panels arranged horizontally; USWDS grid system used for column management
- Tailwind CSS v4 responsive breakpoints configured to match USWDS breakpoint values manage all layout transitions
- No separate mobile codebase — one responsive implementation

**Priority:** P0 — Critical (MVP requirement; REQ-05)

---

### F6: Secondary Weather Details Panel

**Description:** A collapsible "Details" panel provides secondary weather metrics for users who need more than temperature and precipitation. Collapsed by default to avoid cluttering the primary view for casual users. Implemented using the USWDS accordion component to inherit correct ARIA expanded/collapsed states and keyboard interaction.

**Capabilities:**
- Collapsed by default; user taps/clicks to expand (USWDS accordion pattern: `aria-expanded`, `aria-controls` wired correctly)
- When expanded, displays: UV index, wind speed and direction (cardinal + degrees), visibility (km/mi), humidity (if not shown in hero), sunrise time, sunset time
- All values use the location's local timezone for sunrise/sunset display
- Panel state does not persist across page reloads (returns to collapsed on next visit)
- Accordion trigger meets 44px minimum touch target and is keyboard-operable (Enter/Space to toggle)

**Priority:** P1 — High (outdoor enthusiast persona; differentiator over basic weather apps)

---

### F7: Data Freshness & Stale State Handling

**Description:** Users always know how current their weather data is, and the app behaves gracefully when the network is unavailable or slow. Status messages use USWDS alert components to ensure consistent visual treatment and screen reader announcements.

**Capabilities:**
- "Updated X minutes ago" freshness indicator (USWDS `usa-tag` or inline text) visible on the main screen at all times when data is loaded
- TanStack Query `staleTime` set to 10 minutes — data is not re-fetched on every component mount
- If the network is offline, the last cached data is displayed with a visible USWDS `usa-alert--warning` banner: "Showing cached data from X minutes ago"
- If no cached data exists and the network fails, a USWDS `usa-alert--error` message: "Unable to load weather — check your connection" (never a blank screen)
- "City not found — try a different spelling" displayed as USWDS `usa-alert--info` when the geocoding API returns no results
- All alert messages use `aria-live="polite"` regions to announce status changes to screen readers

**Priority:** P1 — High (trust and reliability; critical for mobile users on intermittent connections)

---

### F8: Accessibility (WCAG AA Compliance)

**Description:** The app meets WCAG 2.2 Level AA across all components and states, verified by both automated tooling and manual screen reader testing. USWDS components serve as the accessibility baseline — all custom components are built to match or exceed USWDS ARIA patterns.

**Capabilities:**
- All interactive elements (search input, GPS button, forecast cards, unit toggle, details accordion) reachable and operable via keyboard, following USWDS focus management patterns
- `aria-live` regions announce weather data updates to screen readers when location changes or data refreshes
- WCAG 1.4.1: Weather condition never conveyed by color alone — icon + text label always paired
- WCAG 1.4.3: 4.5:1 contrast ratio on all text across all condition-aware background states (validated against USWDS color token contrast matrix)
- WCAG 2.5.8: Minimum 44px touch targets on all interactive elements (enforced via USWDS component defaults)
- Recharts chart components include accessible text fallback (data table or `aria-label` descriptions) for screen readers
- All animations and transitions respect `prefers-reduced-motion` media query
- Focus indicators meet USWDS focus style standards (2px solid offset outline on all interactive elements)

**Priority:** P1 — High (Phase 4 delivery; non-negotiable for production quality)

---

### F9: Attribution & Deployment

**Description:** The app is publicly deployed on HTTPS and includes required attribution for the Open-Meteo API. The footer uses the USWDS `usa-footer` component for semantic structure and consistent visual treatment.

**Capabilities:**
- Deployed to Vercel with HTTPS by default (required for Browser Geolocation API to function)
- Open-Meteo CC BY 4.0 attribution link visible in the USWDS `usa-footer` component on every page load
- Deployment auto-triggers from GitHub main branch push (zero manual deploy steps)
- Footer also includes USWDS `usa-identifier` or equivalent for design system acknowledgment if appropriate

**Priority:** P0 — Critical (geolocation requires HTTPS; Open-Meteo license requires attribution)

---

## 7. Non-Functional Requirements

| Category | Requirement | Target | Verification |
|---|---|---|---|
| Performance | Initial page load (mobile 4G) | < 2 seconds to first meaningful content | Core Web Vitals / Lighthouse |
| Performance | Time to weather data visible after city select | < 2 seconds | Manual timing + TanStack Query cache |
| Performance | JavaScript bundle size (gzipped) | < 300 KB | Vite bundle analysis |
| Accessibility | WCAG compliance level | 2.2 Level AA | Automated (axe-core) + manual screen reader |
| Accessibility | Contrast ratio — all condition backgrounds | ≥ 4.5:1 (text) | USWDS colour contrast matrix + analyser |
| Accessibility | Touch target size | ≥ 44 × 44 px | DevTools inspection + USWDS component defaults |
| Accessibility | Focus indicators | USWDS 2px solid offset outline on all interactive elements | Manual keyboard audit |
| Design System | UI compliance | All components follow USWDS design tokens, spacing, and component patterns | Design review against USWDS guidelines |
| Responsiveness | Supported viewport range | 375px – 1280px+ | Manual + BrowserStack |
| Responsiveness | Breakpoints | Aligned to USWDS responsive grid (`mobile-lg`, `tablet`, `desktop`) | Layout audit at each breakpoint |
| Responsiveness | No horizontal overflow | Zero overflow at any breakpoint | Automated layout test |
| Reliability | API cache stale time | 10 minutes (no redundant calls) | Network tab observation |
| Reliability | Offline behaviour | Cached data shown via USWDS alert; never blank screen | TanStack Query cache + manual offline test |
| Security | API key exposure | None — Open-Meteo requires no key | Code audit |
| Browser Support | Target browsers | Chromium 110+, Firefox 115+, Safari 16+ | BrowserStack |
| Motion | Animations | All respect `prefers-reduced-motion` | CSS media query audit |

---

## 8. Success Metrics

**User experience:**
- Time to first meaningful weather data ≤ 2 seconds on a simulated mobile 4G connection (measured via Lighthouse)
- Bounce rate from initial load < 30% (indicating users are finding data without confusion)
- Unit toggle usage visible on main screen — no support requests about "how to change to Fahrenheit"

**Accessibility:**
- Zero WCAG AA violations in automated axe-core scan on production build
- All condition + time-of-day background combinations achieve ≥ 4.5:1 contrast ratio (manual audit passes; validated against USWDS color token matrix)
- All interactive elements keyboard-navigable with logical tab order (manual screen reader test passes)
- All USWDS component ARIA patterns correctly implemented (verified via manual keyboard + VoiceOver/NVDA testing)

**Reliability:**
- API error states render a visible USWDS alert message — zero instances of blank screen on API failure (manual testing across all error paths)
- Geolocation denial produces no blank screen or stuck state — search input remains usable in 100% of test cases

**Design system compliance:**
- All UI components traceable to a USWDS design token or component pattern
- No custom color values outside the USWDS color token set used in the production build

**Code quality:**
- TypeScript strict mode with zero `any` casts at deploy time
- Zero exposed API keys in source or build output
- Open-Meteo CC BY 4.0 attribution footer present on every production page load

**Deployment:**
- App live at a public HTTPS Vercel URL
- Geolocation API functioning correctly in production (requires HTTPS — verified on live URL, not localhost)

---

## 9. Out of Scope

The following features are explicitly excluded from v1.0. They are documented here to prevent scope creep and to clearly communicate product boundaries to stakeholders.

**Excluded from v1.0:**

- **User accounts and saved locations** — Adds authentication infrastructure and state management complexity not justified by the v1 use case. Single active location is sufficient.
- **Severe weather alerts and push notifications** — Requires persistent backend infrastructure, NWS feed integration, and legal review of alert accuracy obligations. Out of scope for a frontend-only app.
- **Historical weather data** — Not part of the core user question ("what is it doing now / soon?"). Adds API complexity without serving primary personas.
- **Radar maps** — MapLibre/Leaflet integration with tile server adds significant complexity; not core to "simple weather."
- **Air quality index (AQI)** — Open-Meteo has an AQI endpoint; deferred to v2 to preserve focus.
- **Animated weather backgrounds** — CSS keyframe animation for weather states is non-trivial to do accessibly and well; condition-aware static gradients ship in v1.
- **Multiple saved locations** — Requires `localStorage` architecture upgrade; deferred to v2.
- **Weather for coordinates (lat/lon direct input)** — City name search is sufficient for v1 personas.
- **Autoplay video, advertising, news/trending content** — Anti-features; never build.

---

## 10. Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Timezone display bugs (UTC vs. location timezone) | HIGH | HIGH | Set `timezone=auto` on every Open-Meteo request from Phase 1; use `Intl.DateTimeFormat` for all time display; never use browser's local timezone for location data |
| Geolocation denial produces blank/stuck screen | HIGH | HIGH | Geolocation is opt-in only; city search is always the primary path; denial path tested explicitly in Phase 1 |
| WCAG contrast failure on dynamic condition backgrounds | HIGH | MEDIUM | Design color palette for all weather × time-of-day combinations using USWDS color tokens with known contrast ratios before implementing Phase 3; validate with contrast analyser before ship |
| No loading/error states (blank screen on slow/failed API) | HIGH | HIGH | TanStack Query `isLoading`/`isError` + USWDS skeleton and alert components built in Phase 1; never deferred to "polish later" |
| USWDS token drift from Tailwind CSS v4 config | MEDIUM | MEDIUM | Map USWDS tokens to Tailwind custom properties at project start; document token mapping in `tailwind.config.ts`; enforce via design review checklist |
| Precision mismatch (decimal temperatures destroy trust) | MEDIUM | MEDIUM | All temperature values rounded to integer display; enforced in API response transformation layer |
| Open-Meteo geocoding quality for ambiguous city names | MEDIUM | LOW | "City not found" error state (USWDS `usa-alert--info`) built in Phase 4; Nominatim available as fallback geocoder |
| Recharts SVG charts inaccessible to screen readers | MEDIUM | MEDIUM | Verify Recharts ARIA support in Phase 2; implement `<table>` or `aria-label` data fallback if library support is insufficient |
| Bundle size exceeds 300 KB gzipped | LOW | MEDIUM | Verify against Core Web Vitals after Phase 2 (Recharts is the largest addition); code-split or replace if target is missed |
| Open-Meteo rate limits exceeded | LOW | LOW | `staleTime: 10min` in TanStack Query prevents redundant calls; Open-Meteo non-commercial free tier is generous for portfolio traffic |

---

## 11. Feature Index

| ID | Feature | REQ Coverage | Priority | Phase | Status |
|---|---|---|---|---|---|
| F0 | Location Search & Detection | REQ-01 | P0 | Phase 1 | Not started |
| F1 | Current Conditions Display | REQ-02 | P0 | Phase 1 | Not started |
| F2 | Hourly Forecast | REQ-03, REQ-04 | P1 | Phase 2 | Not started |
| F3 | 7-Day Daily Forecast | REQ-03 | P0 | Phase 2 | Not started |
| F4 | Weather Icons & Visual Indicators | REQ-04 | P0 | Phase 2–3 | Not started |
| F5 | Responsive Layout | REQ-05 | P0 | Phase 3 | Not started |
| F6 | Secondary Weather Details Panel | — | P1 | Phase 3 | Not started |
| F7 | Data Freshness & Stale State Handling | — | P1 | Phase 3–4 | Not started |
| F8 | Accessibility (WCAG AA) | — | P1 | Phase 4 | Not started |
| F9 | Attribution & Deployment | — | P0 | Phase 4 | Not started |

**Priority summary:**

- **P0 (Critical — ship to call it v1.0):** F0, F1, F3, F4, F5, F9 — 6 features
- **P1 (High — expected by target personas):** F2, F6, F7, F8 — 4 features
- **P2/P3 (Deferred — v2+):** See [Out of Scope](#9-out-of-scope)

---

## Requirement Traceability

| Requirement | Description | Features | Phase |
|---|---|---|---|
| REQ-01 | User can search for weather by city/location name | F0 | Phase 1 |
| REQ-02 | User can view current weather conditions (temperature, description, humidity, wind) | F1 | Phase 1 |
| REQ-03 | User can view a multi-day forecast (3–7 days) | F2, F3 | Phase 2 |
| REQ-04 | User can see weather icons/visual indicators for conditions | F2, F3, F4 | Phase 2–3 |
| REQ-05 | App displays data clearly on both desktop and mobile | F5 | Phase 3 |

**Coverage: 5/5 requirements fully mapped ✓**

---

*PRD version 1.1 — generated 2026-05-01*
*Sources: PROJECT.md, ref_docs/PRD.md, research/SUMMARY.md, ROADMAP.md*
*USWDS integration: All UI components, design tokens, and patterns must conform to USWDS standards*
*Next documents: FRD (functional requirements), TechArch (architecture decisions), UserStories (sprint-ready stories)*
