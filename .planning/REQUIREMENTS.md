# Requirements: Simple Weather App

**Defined:** 2026-05-01
**Core Value:** Deliver the current temperature and today's forecast for any city in under 2 seconds — no accounts, no ads, no blank screens.

---

## v1 Requirements

### Location (F0)

- [ ] **F0-01**: User can search for weather by city name with autocomplete suggestions (2+ chars, up to 5 results)
- [ ] **F0-02**: User can select a suggested city using mouse, touch, or keyboard (arrow keys + Enter)
- [ ] **F0-03**: User can opt in to GPS geolocation to detect current location; denial never produces an error or blank screen
- [ ] **F0-04**: Recent searches (up to 5) persist in localStorage as quick-select chips
- [ ] **F0-05**: "City not found" message shown when geocoding returns no results; no blank screen

### Current Conditions (F1)

- [ ] **F1-01**: User can view current temperature as an integer (never decimals) in °C or °F
- [ ] **F1-02**: User can view weather condition with both an icon and a text label (never icon-only)
- [ ] **F1-03**: User can view feels-like temperature, today's high/low, precipitation probability, humidity, and wind speed
- [ ] **F1-04**: User can toggle between °C and °F from the main screen; preference persisted in localStorage
- [ ] **F1-05**: Skeleton loading state is shown during data fetch; never a blank screen
- [ ] **F1-06**: Clear error message with retry button shown on API failure; never a blank screen

### Hourly Forecast (F2)

- [ ] **F2-01**: User can view a horizontally scrollable row of 24 hourly forecast cards on the main screen (no paywall, no tab)
- [ ] **F2-02**: Each hourly card shows: hour label (city's local timezone), condition icon, temperature, and precipitation probability
- [ ] **F2-03**: All hourly time labels reflect the searched city's local timezone (not the user's browser timezone)

### 7-Day Forecast (F3)

- [ ] **F3-01**: User can view all 7 days of daily forecast without pagination, paywall, or "see more" interaction
- [ ] **F3-02**: Each daily row shows: day name ("Today" for current day), condition icon + text label, high/low temperatures, precipitation probability
- [ ] **F3-03**: A Recharts AreaChart temperature trend is rendered alongside the 7-day list with an accessible sr-only fallback

### Weather Icons & Visuals (F4)

- [ ] **F4-01**: Every WMO weather code (0–99) returns a specific named condition icon and text label; no generic fallback
- [ ] **F4-02**: Icons use day and night variants based on the API is_day field (not the browser's clock)
- [ ] **F4-03**: The current conditions hero uses a condition-aware gradient background; all text achieves ≥4.5:1 contrast ratio

### Responsive Layout (F5)

- [ ] **F5-01**: App displays without horizontal overflow at 375px viewport; all tap targets ≥44px
- [ ] **F5-02**: Layout adapts correctly at tablet (640px) and desktop (1024px) breakpoints using USWDS tokens
- [ ] **F5-03**: Layout reflows correctly at 125% browser zoom on desktop

### Secondary Details Panel (F6)

- [ ] **F6-01**: A collapsible USWDS accordion Details panel shows UV index, wind speed/direction, visibility, humidity, sunrise, and sunset
- [ ] **F6-02**: Panel is collapsed by default; expands with a single tap/click or Enter/Space keyboard input
- [ ] **F6-03**: All sunrise/sunset times reflect the searched city's local timezone

### Data Freshness & Error Handling (F7)

- [ ] **F7-01**: "Updated X minutes ago" freshness indicator is always visible when data is loaded
- [ ] **F7-02**: When offline with cached data, cached data is shown with a "Showing data from X min ago" notice
- [ ] **F7-03**: When offline with no cache, a friendly error with retry button is shown; never a blank screen

### Accessibility WCAG AA (F8)

- [ ] **F8-01**: All interactive elements are keyboard-reachable with logical Tab order; no keyboard traps; skip nav present
- [ ] **F8-02**: USWDS focus indicators (2px solid ring) visible on all focusable elements via :focus-visible
- [ ] **F8-03**: aria-live regions announce weather data loads and freshness updates to screen readers
- [ ] **F8-04**: Weather conditions are never conveyed by color alone — icon and text label always paired (WCAG 1.4.1)
- [ ] **F8-05**: All animations and transitions respect prefers-reduced-motion media query

### Attribution & Deployment (F9)

- [ ] **F9-01**: Open-Meteo CC BY 4.0 attribution link is visible in the footer on every page load
- [ ] **F9-02**: App is deployed to Vercel with HTTPS; auto-deploys from GitHub main branch push

---

## v2 Requirements

### Enhanced Location

- **LOC-V2-01**: Multiple saved locations — requires localStorage architecture upgrade
- **LOC-V2-02**: Direct lat/lon coordinate input

### Enhanced Forecast

- **FORE-V2-01**: Air quality index (AQI) — Open-Meteo has the endpoint; deferred to preserve v1 focus
- **FORE-V2-02**: Historical weather data

### Enhanced Features

- **ENH-V2-01**: Radar maps (MapLibre/Leaflet) — significant complexity; deferred
- **ENH-V2-02**: Animated weather backgrounds — non-trivial to do accessibly

---

## Out of Scope

| Feature | Reason |
|---|---|
| User accounts and saved locations | Adds auth infrastructure not justified for v1 single-location use case |
| Severe weather alerts and push notifications | Requires persistent backend and legal review; out of scope for frontend-only |
| Historical weather data | Not part of core user question; adds API complexity without serving primary personas |
| Radar maps | MapLibre/Leaflet tile server integration; significant complexity; not core to "simple weather" |
| Air quality index (AQI) | Open-Meteo has endpoint; deferred to v2 to preserve focus |
| Animated weather backgrounds | Non-trivial to do accessibly; static gradients ship in v1 |
| Weather for lat/lon direct input | City name search sufficient for v1 personas |
| Autoplay video, advertising, news | Anti-features; never build |

---

## Traceability

*Populated during roadmap creation — 2026-05-01.*

| Requirement | Phase | Status |
|---|---|---|
| F0-01 | Phase 1 | Pending |
| F0-02 | Phase 1 | Pending |
| F0-03 | Phase 1 | Pending |
| F0-04 | Phase 1 | Pending |
| F0-05 | Phase 1 | Pending |
| F1-01 | Phase 1 | Pending |
| F1-02 | Phase 1 | Pending |
| F1-03 | Phase 1 | Pending |
| F1-04 | Phase 1 | Pending |
| F1-05 | Phase 1 | Pending |
| F1-06 | Phase 1 | Pending |
| F2-01 | Phase 2 | Pending |
| F2-02 | Phase 2 | Pending |
| F2-03 | Phase 2 | Pending |
| F3-01 | Phase 2 | Pending |
| F3-02 | Phase 2 | Pending |
| F3-03 | Phase 2 | Pending |
| F4-01 | Phase 2 | Pending |
| F4-02 | Phase 2 | Pending |
| F4-03 | Phase 2 | Pending |
| F5-01 | Phase 3 | Pending |
| F5-02 | Phase 3 | Pending |
| F5-03 | Phase 3 | Pending |
| F6-01 | Phase 3 | Pending |
| F6-02 | Phase 3 | Pending |
| F6-03 | Phase 3 | Pending |
| F7-01 | Phase 3 | Pending |
| F7-02 | Phase 3 | Pending |
| F7-03 | Phase 3 | Pending |
| F8-01 | Phase 4 | Pending |
| F8-02 | Phase 4 | Pending |
| F8-03 | Phase 4 | Pending |
| F8-04 | Phase 4 | Pending |
| F8-05 | Phase 4 | Pending |
| F9-01 | Phase 4 | Pending |
| F9-02 | Phase 4 | Pending |

**Coverage:**
- v1 requirements: 36 total
- Mapped to phases: 36 ✓
- Unmapped: 0 ✓

---
*Requirements defined: 2026-05-01*
*Last updated: 2026-05-01 after initial definition*
