# Requirements Traceability Matrix
## Simple Weather App

| Field | Value |
|---|---|
| **Product Name** | Simple Weather App |
| **Version** | 1.0 |
| **Date** | 2026-05-01 |
| **Status** | Active |
| **Project Acronym** | SimpleWeatherApp |

---

## 1. Overview

This Requirements Traceability Matrix (RTM) provides bidirectional traceability between Simple Weather App requirements and specifications across all generated spec documents. It ensures every PRD feature maps to FRD functional requirements, every FRD requirement has a corresponding TechArch specification, and every requirement is covered by at least one User Story with testable acceptance criteria.

The traceability chain follows: **PRD Feature → FRD Requirement → TechArch Spec → User Story → Test Case**. This RTM serves as the authoritative cross-reference for verification — confirming that implementation decisions trace back to validated user needs, and that no requirement is left uncovered.

**Documents included:**
- PRD-SimpleWeatherApp.md v1.1
- FRD-SimpleWeatherApp.md v1.0
- TechArch-SimpleWeatherApp.md v1.0
- UserStories-SimpleWeatherApp.md v1.0
- PERSONAS-SimpleWeatherApp.md v1.0
- JTBD-SimpleWeatherApp.md v1.0
- JOURNEYS-SimpleWeatherApp.md v1.0
- STORY-MAP-SimpleWeatherApp.md v1.0

---

## 2. Requirements Summary

### PRD Features (F0–F9)

- **F0** — Location Search & Detection (P0): City name autocomplete + GPS opt-in + recent locations + error states
- **F1** — Current Conditions Display (P0): Hero section — temperature, condition, feels-like, H/L, precip, humidity, wind, unit toggle
- **F2** — Hourly Forecast (P1): 24-card horizontal scroll row with timezone-correct labels and precipitation per card
- **F3** — 7-Day Daily Forecast (P0): Full week forecast list + Recharts AreaChart temperature trend
- **F4** — Weather Icons & Visual Indicators (P0): Full WMO code coverage (0–99), day/night variants, condition-aware gradient backgrounds
- **F5** — Responsive Layout (P0): Mobile-first, 375px–1280px+, USWDS breakpoints, 125% zoom safe
- **F6** — Secondary Weather Details Panel (P1): USWDS accordion, collapsed by default, UV/wind/visibility/sunrise/sunset
- **F7** — Data Freshness & Stale State Handling (P1): Freshness indicator, offline cache, never-blank-screen contract
- **F8** — Accessibility WCAG AA (P1): Keyboard nav, USWDS focus indicators, aria-live, color independence, reduced motion
- **F9** — Attribution & Deployment (P0): Vercel HTTPS, Open-Meteo CC BY 4.0 footer attribution

### Non-Functional Requirements

- **REQ-NFR-001**: Initial page load < 2 seconds on mobile 4G (Lighthouse)
- **REQ-NFR-002**: JS bundle < 300 KB gzipped (Vite bundle analysis)
- **REQ-NFR-003**: WCAG 2.2 Level AA — zero axe-core violations on production build
- **REQ-NFR-004**: All text contrast ≥ 4.5:1 on all condition-aware backgrounds
- **REQ-NFR-005**: All touch targets ≥ 44×44px
- **REQ-NFR-006**: TanStack Query staleTime 10 minutes — no redundant API calls
- **REQ-NFR-007**: `timezone=auto` on every Open-Meteo API request — mandatory
- **REQ-NFR-008**: Zero API keys in source or build output
- **REQ-NFR-009**: Browser support: Chromium 110+, Firefox 115+, Safari 16+
- **REQ-NFR-010**: All animations respect `prefers-reduced-motion`

---

## 3. Traceability Matrix

### F0: Location Search & Detection

| PRD Feature | FRD Section | TechArch Spec | User Stories |
|---|---|---|---|
| F0 — City name autocomplete (2+ chars, debounce 300ms) | FRD §2 — Process: City Search steps 1–10 | TechArch §6 — Open-Meteo Geocoding API; §4 SearchBar component | US-0.1, US-0.2 |
| F0 — GPS opt-in button | FRD §2 — Process: GPS Geolocation steps 1–7 | TechArch §6 — Nominatim Reverse Geocoding API; §4 GpsButton | US-0.3 |
| F0 — Recent locations (localStorage, up to 5) | FRD §2 — Recent Locations sub-feature; §15 Data Storage Schema | TechArch §5 — localStorage schema `swa:recent`; RecentLocation interface | US-0.4 |
| F0 — City not found error state | FRD §2 — Error handling table | TechArch §6 — API Error Handling Matrix | US-0.5 |

### F1: Current Conditions Display

| PRD Feature | FRD Section | TechArch Spec | User Stories |
|---|---|---|---|
| F1 — Current temperature (integer, °C/°F) | FRD §3 — Current temp field spec; §13 roundTemp(), toFahrenheit() | TechArch §8 — Data Transformation: roundTemp, displayTemp | US-1.1, US-1.3 |
| F1 — Weather condition (icon + text label) | FRD §3 — Condition field; §6 icons | TechArch §8 — getCondition() WMO mapping; WeatherCondition interface | US-1.1, US-4.1, US-4.2 |
| F1 — Feels-like, H/L, precipitation, humidity, wind | FRD §3 — Supporting data fields table | TechArch §7 — OpenMeteoForecastResponse.current interface | US-1.2 |
| F1 — °C/°F unit toggle (localStorage persistence) | FRD §3 — Unit toggle spec | TechArch §5 — localStorage `swa:unit`; TemperatureUnit type | US-1.3 |
| F1 — Skeleton loading state | FRD §3 — Loading state; §16 Global Error Handling | TechArch §5 — TanStack Query isLoading state | US-1.4 |
| F1 — Error state (usa-alert--error) | FRD §3 — Error state; §16 | TechArch §6 — API Error Handling Matrix | US-1.5 |

### F2: Hourly Forecast

| PRD Feature | FRD Section | TechArch Spec | User Stories |
|---|---|---|---|
| F2 — 24-card horizontal scroll (precip on every card) | FRD §4 — Hourly Forecast sub-features; 24-card count contract | TechArch §4 — HourlyForecast component; HourlyCard; HourlySlot interface | US-2.1, US-2.3 |
| F2 — Timezone-correct hourly labels (timezone=auto) | FRD §4 — Day/night icon logic; timezone derivation | TechArch §8 — formatHourLabel() using Intl.DateTimeFormat with timezone | US-2.2 |

### F3: 7-Day Daily Forecast

| PRD Feature | FRD Section | TechArch Spec | User Stories |
|---|---|---|---|
| F3 — 7-day list (H/L, precip, condition per day) | FRD §5 — Daily Forecast sub-features; "Today" label | TechArch §7 — daily array interface; DailySlot; §8 formatDayLabel() | US-3.1 |
| F3 — Recharts AreaChart temperature trend + sr-only fallback | FRD §5 — AreaChart spec | TechArch §4 — TemperatureTrendChart; AccessibleDataTable | US-3.2 |

### F4: Weather Icons & Visual Indicators

| PRD Feature | FRD Section | TechArch Spec | User Stories |
|---|---|---|---|
| F4 — Full WMO code coverage (0–99), icon + text label | FRD §6 — WMO code table (26 entries) | TechArch §8 — WMO_CONDITIONS map; getCondition() | US-4.1 |
| F4 — Day/night icon variants (is_day from API) | FRD §6 — Day/night logic | TechArch §8 — getCondition() resolvedIconId with night suffix | US-4.2 |
| F4 — Condition-aware gradient (USWDS tokens, 4.5:1 contrast) | FRD §6 — Gradient palette table | TechArch §3 — USWDS token gradient table; §9 contrast validation | US-4.3 |

### F5: Responsive Layout

| PRD Feature | FRD Section | TechArch Spec | User Stories |
|---|---|---|---|
| F5 — Mobile layout 375px (no overflow, 44px targets) | FRD §7 — Mobile breakpoint spec | TechArch §3 — USWDS breakpoints table; §10 Performance Architecture | US-5.1 |
| F5 — Tablet/desktop layout (USWDS breakpoints, 125% zoom) | FRD §7 — Tablet, desktop breakpoint specs | TechArch §3 — USWDS breakpoints; Tailwind v4 config | US-5.2 |

### F6: Secondary Weather Details Panel

| PRD Feature | FRD Section | TechArch Spec | User Stories |
|---|---|---|---|
| F6 — USWDS accordion, collapsed default, single-tap expand | FRD §8 — Details Panel process; accordion spec | TechArch §4 — DetailsPanel component; usa-accordion | US-6.1 |
| F6 — Accessible accordion (aria-expanded, hidden attr, keyboard) | FRD §8 — ARIA requirements | TechArch §3 — USWDS Component Usage Map: usa-accordion | US-6.2 |

### F7: Data Freshness & Stale State Handling

| PRD Feature | FRD Section | TechArch Spec | User Stories |
|---|---|---|---|
| F7 — Freshness indicator ("Updated X min ago") | FRD §9 — Freshness indicator spec | TachArch §5 — TanStack Query config; fetchedAt in WeatherData | US-7.1 |
| F7 — Offline with cached data (usa-alert--info) | FRD §9 — Offline states | TechArch §5 — networkMode: 'offlineFirst'; gcTime 30 min | US-7.2 |
| F7 — No-cache offline (usa-alert--error) | FRD §9 — No-cache error | TechArch §6 — API Error Handling Matrix | US-7.3 |

### F8: Accessibility WCAG AA

| PRD Feature | FRD Section | TechArch Spec | User Stories |
|---|---|---|---|
| F8 — Full keyboard navigation (Tab order, skip nav) | FRD §10 — Keyboard nav map; skip link spec | TechArch §3 — USWDS Component Usage Map; §11 axe-core in CI | US-8.1 |
| F8 — USWDS focus indicators (2px solid, :focus-visible) | FRD §10 — Focus indicator spec | TechArch §3 — USWDS Integration: focus utility | US-8.2 |
| F8 — Screen reader aria-live announcements | FRD §10 — aria-live regions | TechArch §3 — USWDS Component Usage Map: aria-live | US-8.3 |
| F8 — Color independence (WCAG 1.4.1, icon+text always) | FRD §10 — WCAG criterion mapping | TechArch §8 — getCondition() always returns label; §11 axe-core | US-8.4 |
| F8 — prefers-reduced-motion support | FRD §10 — Animation rules | TechArch §10 — Performance Architecture | US-8.5 |

### F9: Attribution & Deployment

| PRD Feature | FRD Section | TechArch Spec | User Stories |
|---|---|---|---|
| F9 — Open-Meteo CC BY 4.0 attribution footer | FRD §11 — Footer HTML structure | TechArch §4 — Footer component; usa-footer | US-9.1 |
| F9 — Vercel HTTPS, auto-deploy from GitHub | FRD §11 — Deployment parameters table | TechArch §11 — Deployment Architecture; vercel.json | US-9.2 |

---

## 4. Requirements Detail

### F0: Location Search & Detection

**PRD Priority:** P0 — Critical

FRD functional requirements covered:
- City text input triggers geocoding after 300ms debounce on 2+ characters
- Suggestion list shows up to 5 results with city/region/country
- GPS button triggers browser geolocation (opt-in only; denial never shows error)
- Nominatim reverse geocoding converts GPS coords to city name
- Recent locations stored in `localStorage` (max 5, dedup, most-recent-first)
- `role="combobox"`, `role="listbox"`, `role="option"` ARIA structure for suggestion list
- Keyboard: Down/Up arrows navigate suggestions; Enter selects; Escape dismisses

### F1: Current Conditions Display

**PRD Priority:** P0 — Critical

FRD functional requirements covered:
- Integer-only temperature display (Math.round — never decimals)
- Every condition shows icon + text label (no icon-only rendering)
- Day/night icon selection based on `is_day` field from Open-Meteo (not browser time)
- Unit toggle visible on main screen via `usa-button-group` with `aria-pressed`
- Unit preference persisted in `localStorage` key `swa:unit`
- Skeleton loading state with `aria-busy="true"` during fetch
- `usa-alert--error` with retry button on API failure

### F2: Hourly Forecast

**PRD Priority:** P1 — High

FRD functional requirements covered:
- Exactly 24 hourly cards starting from current hour
- Precipitation probability shown on every card (not omitted)
- All time labels derived from API-returned `timezone` string via `Intl.DateTimeFormat`
- `role="region"` with `aria-label="Hourly forecast"` on scroll container
- Minimum 44px touch targets per WCAG 2.5.8

### F3: 7-Day Daily Forecast

**PRD Priority:** P0 — Critical

FRD functional requirements covered:
- All 7 days visible without pagination, paywall, or "see more" interaction
- First row labeled "Today"; subsequent rows use abbreviated day names
- High shown before low on every row
- Precipitation probability shown on every row
- Recharts AreaChart with `sr-only` accessible data table fallback

### F4: Weather Icons & Visual Indicators

**PRD Priority:** P0 — Critical

FRD functional requirements covered:
- 26-entry WMO_CONDITIONS map covers all valid Open-Meteo codes (0–99)
- Night variants applied for codes 0–2 (clear/partly-cloudy/overcast) when `is_day=0`
- Gradient backgrounds use USWDS color tokens for all 7 condition groups × day/night
- All gradients validated for ≥4.5:1 contrast with overlaid white text

### F5: Responsive Layout

**PRD Priority:** P0 — Critical

FRD functional requirements covered:
- Single-column layout at 375px; no horizontal overflow
- Two-column (hero + 7-day) at USWDS `tablet` breakpoint (640px)
- Full horizontal layout at USWDS `desktop` breakpoint (1024px)
- Correct reflow at 125% browser zoom
- No custom pixel media queries — USWDS breakpoint tokens only

### F6: Secondary Weather Details Panel

**PRD Priority:** P1 — High

FRD functional requirements covered:
- USWDS `usa-accordion` collapsed by default on page load
- Expands on single tap/click or Enter/Space keyboard input
- `aria-expanded` updates on toggle; `hidden` attribute (not CSS-only) when collapsed
- Panel shows: UV index, wind speed (mph), wind direction (cardinal + degrees), visibility (km), humidity, sunrise, sunset
- All times in searched city's local timezone
- Panel does not persist open state across page reloads

### F7: Data Freshness & Stale State Handling

**PRD Priority:** P1 — High

FRD functional requirements covered:
- TanStack Query `staleTime: 10 minutes`, `gcTime: 30 minutes`, `networkMode: 'offlineFirst'`
- "Updated X minutes ago" indicator always visible when data is loaded; updates every minute
- Offline + cached data: `usa-alert--info` with timestamp notice
- Offline + no cache: `usa-alert--error` with retry button
- Slow load (>5s): `usa-alert--info` "Still loading..."
- City not found: `usa-alert--warning` below search input

### F8: Accessibility WCAG AA

**PRD Priority:** P1 — High

FRD functional requirements covered (WCAG 2.2 AA):
- **2.1.1** — All functionality operable via keyboard
- **2.4.1** — Skip to main content as first tab stop
- **2.4.3** — Logical focus order: search → hourly → 7-day → details → unit toggle → footer
- **1.4.1** — No condition communicated by color alone (icon + text always)
- **1.4.3** — Minimum 4.5:1 contrast on all text/background combinations
- **1.4.11** — Focus ring meets 3:1 contrast on all backgrounds
- **2.5.8** — Minimum 44×44px touch targets
- **4.1.2** — ARIA roles and states on all interactive components
- **1.3.1** — Semantic HTML structure; `role` attributes where needed
- **2.3.3** — `prefers-reduced-motion` respected on all animations

### F9: Attribution & Deployment

**PRD Priority:** P0 — Critical

FRD functional requirements covered:
- `usa-footer` (minimal) on every page with Open-Meteo CC BY 4.0 attribution link
- Link opens in new tab with `rel="noopener noreferrer"`
- Vercel deployment with HTTPS enforced
- GitHub main branch push auto-triggers Vercel deploy
- Preview deployments on pull requests

---

## 5. Test Case Coverage Matrix

| Feature | User Stories | Test Cases | Coverage |
|---|---|---|---|
| F0 — Location Search | US-0.1, US-0.2, US-0.3, US-0.4, US-0.5 | TEST-001–010 | 5 stories, 100% |
| F1 — Current Conditions | US-1.1, US-1.2, US-1.3, US-1.4, US-1.5 | TEST-011–020 | 5 stories, 100% |
| F2 — Hourly Forecast | US-2.1, US-2.2, US-2.3 | TEST-021–026 | 3 stories, 100% |
| F3 — 7-Day Forecast | US-3.1, US-3.2 | TEST-027–030 | 2 stories, 100% |
| F4 — Icons & Indicators | US-4.1, US-4.2, US-4.3 | TEST-031–036 | 3 stories, 100% |
| F5 — Responsive Layout | US-5.1, US-5.2 | TEST-037–040 | 2 stories, 100% |
| F6 — Details Panel | US-6.1, US-6.2 | TEST-041–044 | 2 stories, 100% |
| F7 — Data Freshness | US-7.1, US-7.2, US-7.3 | TEST-045–050 | 3 stories, 100% |
| F8 — Accessibility | US-8.1, US-8.2, US-8.3, US-8.4, US-8.5 | TEST-051–060 | 5 stories, 100% |
| F9 — Attribution & Deploy | US-9.1, US-9.2 | TEST-061–063 | 2 stories, 100% |
| **Total** | **32 stories** | **63 test cases** | **100%** |

### Test Case Definitions

| Test ID | Test Case | Related Story | Verification Method |
|---|---|---|---|
| TEST-001 | Typing 2+ chars triggers geocoding API after 300ms | US-0.1 | Network tab observation |
| TEST-002 | Up to 5 suggestions displayed with city/region/country | US-0.1 | Manual inspection |
| TEST-003 | Selecting suggestion loads weather immediately | US-0.1 | Manual + timing |
| TEST-004 | Down/Up arrow navigates suggestions; Enter selects | US-0.2 | Keyboard testing |
| TEST-005 | Escape key dismisses suggestion list | US-0.2 | Keyboard testing |
| TEST-006 | GPS button triggers geolocation permission | US-0.3 | Manual testing |
| TEST-007 | Geolocation denial: no error shown, search remains active | US-0.3 | Manual (deny in browser) |
| TEST-008 | Recent location chips appear for prior searches | US-0.4 | Manual + localStorage inspect |
| TEST-009 | Tapping chip loads weather for that location | US-0.4 | Manual testing |
| TEST-010 | City not found: usa-alert--warning shown below search | US-0.5 | Mock geocoding API → empty result |
| TEST-011 | Current temp displayed as integer (no decimals) | US-1.1 | Unit test: roundTemp() |
| TEST-012 | Condition shows icon + text label (never icon only) | US-1.1 | Automated DOM assertion |
| TEST-013 | Feels-like, H/L, precip%, humidity, wind all present | US-1.2 | Automated DOM assertion |
| TEST-014 | Unit toggle visible on main screen without navigation | US-1.3 | Manual + automated |
| TEST-015 | Unit toggle updates all temperatures simultaneously | US-1.3 | Component test |
| TEST-016 | Unit preference persists across page reload | US-1.3 | localStorage assertion |
| TEST-017 | Skeleton shown during weather fetch (not blank) | US-1.4 | Component test (mock slow API) |
| TEST-018 | usa-alert--error shown on API failure with retry button | US-1.5 | Component test (mock 500 response) |
| TEST-019 | No blank screen on any error path | US-1.5 | E2E test: 6 error scenarios |
| TEST-020 | aria-busy="true" on hero container during loading | US-1.4 | Automated ARIA assertion |
| TEST-021 | Exactly 24 hourly cards visible in scroll row | US-2.1 | Automated DOM count |
| TEST-022 | Precipitation probability shown on every hourly card | US-2.1 | Automated DOM assertion |
| TEST-023 | Hourly row on main screen without tab/paywall | US-2.1 | Manual inspection |
| TEST-024 | Hourly time labels reflect searched city's timezone | US-2.2 | Cross-timezone test (UTC+9 city) |
| TEST-025 | browser timezone never used for hourly labels | US-2.2 | Unit test: formatHourLabel() |
| TEST-026 | Hourly touch targets ≥ 44px | US-2.3 | DevTools computed style |
| TEST-027 | All 7 daily rows visible without "see more"/paywall | US-3.1 | Automated DOM count |
| TEST-028 | "Today" label on first row; abbreviated days on rest | US-3.1 | Automated DOM assertion |
| TEST-029 | Recharts AreaChart renders alongside 7-day list | US-3.2 | Visual snapshot test |
| TEST-030 | sr-only data table present in DOM for chart | US-3.2 | Automated ARIA assertion |
| TEST-031 | All WMO codes 0–99 produce named icon + text label | US-4.1 | Unit test: iterate WMO_CONDITIONS |
| TEST-032 | No fallback/unknown icon for any valid WMO code | US-4.1 | Unit test assertion |
| TEST-033 | Sun icon never shown when is_day=0 | US-4.2 | Unit test: getCondition(0, false) |
| TEST-034 | Night icon shown for clear sky when is_day=0 | US-4.2 | Unit test: getCondition(0, false) |
| TEST-035 | Gradient background applied per condition × time | US-4.3 | Visual snapshot test |
| TEST-036 | All gradients ≥ 4.5:1 contrast with white text | US-4.3 | Contrast analyser (14 combinations) |
| TEST-037 | No horizontal overflow at 375px viewport | US-5.1 | Automated layout test |
| TEST-038 | All tap targets ≥ 44px at 375px | US-5.1 | DevTools inspection |
| TEST-039 | Two-column layout at tablet breakpoint | US-5.2 | Automated responsive test |
| TEST-040 | No overflow at 125% browser zoom on 1280px | US-5.2 | Manual (BrowserStack) |
| TEST-041 | Details panel collapsed on page load | US-6.1 | Automated: hidden attr present |
| TEST-042 | Single tap expands panel; all 7 metrics visible | US-6.1 | Manual + automated |
| TEST-043 | aria-expanded updates on toggle | US-6.2 | Automated ARIA assertion |
| TEST-044 | hidden attribute on panel when collapsed (not CSS only) | US-6.2 | Automated DOM assertion |
| TEST-045 | Freshness indicator visible when data loaded | US-7.1 | Automated DOM assertion |
| TEST-046 | Freshness label updates every minute | US-7.1 | Component test (mock timer) |
| TEST-047 | staleTime=10min: no re-fetch on component remount | US-7.1 | Network tab observation |
| TEST-048 | Offline + cache: usa-alert--info with cached data | US-7.2 | Component test (mock offline) |
| TEST-049 | Offline + no cache: usa-alert--error with retry | US-7.3 | Component test (mock offline, no cache) |
| TEST-050 | Retry button triggers re-fetch | US-7.3 | Component test |
| TEST-051 | All interactive elements reachable via Tab | US-8.1 | Keyboard testing |
| TEST-052 | No keyboard traps in any app flow | US-8.1 | Keyboard testing |
| TEST-053 | Skip nav link is first focusable element | US-8.1 | Keyboard testing |
| TEST-054 | USWDS 2px focus ring visible on all focusable elements | US-8.2 | Visual + DevTools |
| TEST-055 | No outline:none without replacement focus style | US-8.2 | CSS audit |
| TEST-056 | aria-live announces "Weather loaded for [City]" | US-8.3 | Screen reader test |
| TEST-057 | Suggestion count announced when list opens | US-8.3 | Screen reader test |
| TEST-058 | Every condition icon has adjacent text label | US-8.4 | Automated DOM assertion |
| TEST-059 | axe-core: zero violations on production build | US-8.4 | axe-core CI scan |
| TEST-060 | prefers-reduced-motion: all transitions disabled | US-8.5 | CSS media query test |
| TEST-061 | usa-footer with Open-Meteo CC BY 4.0 link present | US-9.1 | Automated DOM assertion |
| TEST-062 | Attribution link opens in new tab with noopener | US-9.1 | Automated attribute check |
| TEST-063 | App accessible at public HTTPS URL; GPS works on prod | US-9.2 | Manual production verification |

---

## 6. Non-Functional Requirements Traceability

| NFR ID | Requirement | TechArch Spec | Test Method |
|---|---|---|---|
| REQ-NFR-001 | Initial load < 2s on mobile 4G | §10 Performance Architecture; Vite static bundle | Lighthouse mobile simulation |
| REQ-NFR-002 | JS bundle < 300 KB gzipped | §10 Bundle Budget table; Vite bundle analysis | `npm run build` + bundle analyzer |
| REQ-NFR-003 | WCAG 2.2 Level AA | §3 USWDS Integration; §11 axe-core in CI | axe-core automated + manual screen reader |
| REQ-NFR-004 | Text contrast ≥ 4.5:1 on all gradients | §3 USWDS token gradient table | Contrast analyser (14 combinations) |
| REQ-NFR-005 | Touch targets ≥ 44×44px | §3 USWDS Component Usage Map | DevTools computed style |
| REQ-NFR-006 | staleTime 10 minutes | §5 TanStack Query config | Network tab observation |
| REQ-NFR-007 | timezone=auto on every Open-Meteo request | §6 API integration URLs | Code review: grep for Open-Meteo calls without timezone=auto |
| REQ-NFR-008 | Zero API keys in source/build | §9 Security Architecture | Code audit + build output grep |
| REQ-NFR-009 | Browser support: Chromium 110+, Firefox 115+, Safari 16+ | §2 Technology Stack | BrowserStack cross-browser testing |
| REQ-NFR-010 | All animations respect prefers-reduced-motion | §8 Data Transformation | CSS media query audit |

---

## 7. Change Management

| Version | Date | Change | Author |
|---|---|---|---|
| 1.0 | 2026-05-01 | Initial RTM generation — all 10 features, 32 user stories, 63 test cases | Pivota Spec |

---

## 8. Coverage Summary

| Dimension | Count | Covered | Gap |
|---|---|---|---|
| PRD Features | 10 | 10 | 0 |
| User Stories | 32 | 32 | 0 |
| Test Cases | 63 | 63 | 0 |
| NFR Requirements | 10 | 10 | 0 |
| JTBD Jobs | 9 | 9 | 0 |
| Journey Stages | 6 | 6 | 0 |

**Overall coverage: 100% — no gaps detected ✓**

---

## 9. Approval

| Role | Name | Signature | Date |
|---|---|---|---|
| Product Owner | — | ___________________ | ________ |
| Technical Lead | — | ___________________ | ________ |
| UX Designer | — | ___________________ | ________ |
| QA Lead | — | ___________________ | ________ |

---

*RTM version 1.0 — generated 2026-05-01*
*Sources: PRD-SimpleWeatherApp.md v1.1, FRD-SimpleWeatherApp.md v1.0, TechArch-SimpleWeatherApp.md v1.0, UserStories-SimpleWeatherApp.md v1.0*
*Coverage: 10 features · 32 user stories · 63 test cases · 10 NFRs — 100% traced*
