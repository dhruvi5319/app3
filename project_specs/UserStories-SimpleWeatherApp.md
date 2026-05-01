# User Stories Document
## Simple Weather App

| Field | Value |
|---|---|
| **Product Name** | Simple Weather App |
| **Version** | 1.0 |
| **Date** | 2026-05-01 |
| **Status** | Active |
| **Related PRD** | PRD-SimpleWeatherApp.md v1.1 |
| **Related FRD** | FRD-SimpleWeatherApp.md v1.0 |
| **Related Personas** | PERSONAS-SimpleWeatherApp.md |

---

## Table of Contents

1. [Epic 0: Location Search & Detection (F0)](#epic-0-location-search--detection-f0)
2. [Epic 1: Current Conditions Display (F1)](#epic-1-current-conditions-display-f1)
3. [Epic 2: Hourly Forecast (F2)](#epic-2-hourly-forecast-f2)
4. [Epic 3: 7-Day Daily Forecast (F3)](#epic-3-7-day-daily-forecast-f3)
5. [Epic 4: Weather Icons & Visual Indicators (F4)](#epic-4-weather-icons--visual-indicators-f4)
6. [Epic 5: Responsive Layout (F5)](#epic-5-responsive-layout-f5)
7. [Epic 6: Secondary Weather Details Panel (F6)](#epic-6-secondary-weather-details-panel-f6)
8. [Epic 7: Data Freshness & Stale State Handling (F7)](#epic-7-data-freshness--stale-state-handling-f7)
9. [Epic 8: Accessibility — WCAG AA Compliance (F8)](#epic-8-accessibility--wcag-aa-compliance-f8)
10. [Epic 9: Attribution & Deployment (F9)](#epic-9-attribution--deployment-f9)
11. [Story Index](#story-index)

---

## Priority Definitions

| Priority | Meaning |
|---|---|
| P0 | Critical — MVP; app cannot ship without this |
| P1 | High — Expected by target personas; ships in v1 |
| P2 | Medium — Enhances v1 but can slip to v1.1 |
| P3 | Low — Nice-to-have; deferred to v2 |

---

## Epic 0: Location Search & Detection (F0)

*Entry point for all weather data. All other features depend on a resolved location. Uses USWDS `usa-search` and combo-box pattern.*

### US-0.1: City Name Search with Autocomplete

**As a** Maya Torres (Casual Checker), **I want to** type a city name and see autocomplete suggestions appear, **so that** I can find my city quickly without needing to know exact spelling or country.

**Acceptance Criteria:**
- [ ] Typing 2 or more characters in the search input triggers a request to the Open-Meteo Geocoding API after a 300ms debounce
- [ ] Up to 5 suggestions are displayed in a dropdown list, each showing city name, region, and country
- [ ] Selecting a suggestion loads weather data for that location immediately
- [ ] The search input is implemented as a USWDS `usa-search` component with correct `role="combobox"` on the input

**Priority:** P0 | **Feature Ref:** F0

---

### US-0.2: Keyboard Navigation in Autocomplete

**As a** David Park (Commuter), **I want to** navigate autocomplete suggestions using the keyboard (arrow keys to move, Enter to select, Escape to dismiss), **so that** I can complete a city search entirely by keyboard without reaching for the mouse.

**Acceptance Criteria:**
- [ ] Down arrow key moves focus to the first suggestion; subsequent presses move to the next
- [ ] Up arrow key moves focus up through suggestions; wrapping from first to last
- [ ] Enter key selects the currently highlighted suggestion and loads weather data
- [ ] Escape key dismisses the suggestion list and returns focus to the search input
- [ ] The suggestion list uses `role="listbox"` with each item as `role="option"`
- [ ] Screen readers announce the number of available suggestions when the list opens

**Priority:** P0 | **Feature Ref:** F0

---

### US-0.3: GPS Geolocation (Opt-In)

**As a** Maya Torres (Casual Checker), **I want to** tap a GPS button to detect my current location automatically, **so that** I can get the weather for where I am without typing anything.

**Acceptance Criteria:**
- [ ] A GPS opt-in button (USWDS `usa-button--outline`) appears alongside the search input
- [ ] Tapping the button requests browser geolocation permission
- [ ] If permission is granted, GPS coordinates are reverse-geocoded via Nominatim to a human-readable city name
- [ ] If geolocation permission is denied, the search input remains fully functional with no error state shown
- [ ] If geolocation is unavailable (non-HTTPS context or unsupported browser), the GPS button is not shown

**Priority:** P0 | **Feature Ref:** F0

---

### US-0.4: Recent Locations Quick-Select

**As a** Maya Torres (Casual Checker), **I want to** see my recent city searches displayed as quick-select chips below the search input, **so that** I can re-load a previously searched location with a single tap instead of retyping.

**Acceptance Criteria:**
- [ ] Up to 5 most recent location selections are shown as USWDS `usa-tag` chips below the search input
- [ ] Tapping a chip loads weather data for that location immediately
- [ ] Recent locations persist across browser sessions via `localStorage`
- [ ] Duplicate selections are not added; the existing entry is moved to the top of the list
- [ ] Each chip shows the city name and country code

**Priority:** P1 | **Feature Ref:** F0

---

### US-0.5: City Not Found Error State

**As a** Maya Torres (Casual Checker), **I want to** see a clear "city not found" message when my search returns no results, **so that** I know to try a different spelling rather than waiting for a response that will never come.

**Acceptance Criteria:**
- [ ] When the Geocoding API returns zero results, a USWDS `usa-alert--warning` message displays: "City not found — try a different spelling"
- [ ] The search input remains active and editable; the user is not locked out
- [ ] No blank screen or spinner is shown in the weather data area when search returns no results
- [ ] The error message is announced to screen readers via `aria-live="polite"`

**Priority:** P0 | **Feature Ref:** F0

---

## Epic 1: Current Conditions Display (F1)

*Hero section of the app. Primary answer to the core user question. Must be above the fold on mobile.*

### US-1.1: View Current Temperature and Condition

**As a** Maya Torres (Casual Checker), **I want to** see the current temperature displayed large and prominently along with a weather condition label, **so that** I can answer "what should I wear?" at a glance without reading further.

**Acceptance Criteria:**
- [ ] Current temperature is displayed as an integer (e.g., "18°C", never "18.47°C") in large, dominant type
- [ ] Weather condition is shown with both an icon and a text label (e.g., "Partly Cloudy") — never icon-only
- [ ] Icons use day/night variants based on the `is_day` field from the API — a sun icon never appears at night
- [ ] The temperature and condition are visible above the fold on a 375px mobile screen without scrolling

**Priority:** P0 | **Feature Ref:** F1

---

### US-1.2: View Supporting Current Data (Feels-Like, High/Low, Precipitation, Humidity, Wind)

**As a** Maya Torres (Casual Checker), **I want to** see feels-like temperature, today's high/low, precipitation probability, humidity, and wind speed alongside the current temperature, **so that** I have everything I need to make my outfit and umbrella decision in a single glance.

**Acceptance Criteria:**
- [ ] Feels-like temperature displayed adjacent to the current temperature as a secondary data point
- [ ] Today's high and low temperatures displayed as a paired value (high first, then low)
- [ ] Precipitation probability for the current day displayed as a percentage
- [ ] Humidity displayed as a percentage
- [ ] Wind speed displayed in mph (from API wind_speed_unit=mph parameter)
- [ ] All values are integer or one-decimal maximum — no excessive precision

**Priority:** P0 | **Feature Ref:** F1

---

### US-1.3: Toggle Temperature Units (°C / °F)

**As a** Maya Torres (Casual Checker), **I want to** switch between Celsius and Fahrenheit directly from the main screen, **so that** I can change units without digging through settings menus.

**Acceptance Criteria:**
- [ ] A °C/°F unit toggle (USWDS `usa-button-group`) is visible on the main screen without any navigation
- [ ] Toggling updates all temperature values in the current conditions, hourly forecast, and 7-day forecast simultaneously
- [ ] The active unit has `aria-pressed="true"` on its button
- [ ] The user's unit preference is persisted in `localStorage` key `swa:unit` and restored on the next visit
- [ ] Toggle is operable by keyboard (Tab to reach, Enter/Space to activate)

**Priority:** P0 | **Feature Ref:** F1

---

### US-1.4: Loading State for Current Conditions

**As a** Maya Torres (Casual Checker), **I want to** see a skeleton placeholder while weather data is loading, **so that** I always have visual feedback that the app is working and I never stare at a blank screen.

**Acceptance Criteria:**
- [ ] A skeleton loading state is shown in the hero section while data is fetching — no blank screen during load
- [ ] The skeleton matches the approximate layout of the loaded state (prevents layout shift)
- [ ] `aria-busy="true"` is set on the hero container during loading
- [ ] The skeleton is dismissed and replaced with real data as soon as the API response is received

**Priority:** P0 | **Feature Ref:** F1

---

### US-1.5: Error State for Current Conditions

**As a** Maya Torres (Casual Checker), **I want to** see a clear error message if the weather data fails to load, **so that** I know the app is not broken and I have guidance on what to do next.

**Acceptance Criteria:**
- [ ] A USWDS `usa-alert--error` message is shown when the forecast API returns a 4xx or 5xx response
- [ ] Error message text: "Weather data unavailable. Please try again shortly."
- [ ] A retry button is visible within the error alert
- [ ] The weather data area never shows a blank white space — either data, skeleton, or error is always present
- [ ] Error is announced to screen readers via `aria-live="assertive"`

**Priority:** P0 | **Feature Ref:** F1

---

## Epic 2: Hourly Forecast (F2)

*First-class feature for commuters. Must be visible on the main screen without navigation.*

### US-2.1: View 24-Hour Forecast in Scrollable Row

**As a** David Park (Commuter), **I want to** see a horizontally scrollable row of hourly forecast cards for the next 24 hours directly on the main screen, **so that** I can scan precipitation and temperature changes during my commute window without navigating away.

**Acceptance Criteria:**
- [ ] A horizontal scroll row of exactly 24 hourly cards is visible on the main screen as a first-class section
- [ ] The row is not behind a tab, paywall, or "see more" interaction
- [ ] Each card shows: hour label (local time), condition icon (day/night variant), temperature, and precipitation probability percentage
- [ ] Precipitation probability is shown on every card — never omitted
- [ ] The row is wrapped in `role="region"` with `aria-label="Hourly forecast"` for screen readers

**Priority:** P1 | **Feature Ref:** F2

---

### US-2.2: Correct Timezone in Hourly Labels

**As a** David Park (Commuter), **I want to** see hourly time labels that reflect the searched city's local time (not my browser's timezone), **so that** when I search for a city in a different timezone, the hourly cards make sense relative to activities happening there.

**Acceptance Criteria:**
- [ ] All hourly time labels are derived from the API-returned `timezone` field using `Intl.DateTimeFormat`
- [ ] The user's browser timezone is never used for any time display
- [ ] Searching for a city 5 hours ahead of the user's browser timezone produces hourly labels that are 5 hours ahead
- [ ] Day/night icon variants in hourly cards are determined using the city's local sunrise/sunset times

**Priority:** P0 | **Feature Ref:** F2

---

### US-2.3: Accessible Touch Targets on Hourly Cards

**As a** David Park (Commuter), **I want to** be able to tap hourly cards accurately on a phone while commuting on a moving train, **so that** I don't mis-tap and accidentally open the wrong card or trigger an unwanted action.

**Acceptance Criteria:**
- [ ] All interactive elements within hourly cards have a minimum touch target of 44×44px (WCAG 2.5.8)
- [ ] Cards do not have interactive on-tap behavior in v1 — they are read-only display cards; tappability requirement applies to any future interactive elements
- [ ] Card text does not truncate or overflow at any font size within the card bounds

**Priority:** P1 | **Feature Ref:** F2

---

## Epic 3: 7-Day Daily Forecast (F3)

*Planning view for outdoor enthusiasts. All 7 days visible, no gating.*

### US-3.1: View Full 7-Day Daily Forecast

**As a** Priya Nair (Outdoor Enthusiast), **I want to** see a vertical list of all 7 forecast days with condition icons, high/low temperatures, and precipitation probability for each day, **so that** I can compare the full week and choose the best day for outdoor activities without hitting a paywall or "see more" prompt.

**Acceptance Criteria:**
- [ ] All 7 forecast days are visible in a single vertical list — no pagination, paywall, or "see more" interaction
- [ ] Each row shows: abbreviated day name, condition icon (daytime representative variant), high temperature, low temperature, precipitation probability percentage
- [ ] Today's row is labeled "Today" instead of the day name
- [ ] Precipitation probability is shown on every daily row — never omitted
- [ ] High temperature is always shown before low temperature
- [ ] Data is sourced from the same Open-Meteo API response as current conditions (no extra API call)

**Priority:** P0 | **Feature Ref:** F3

---

### US-3.2: View Temperature Trend Chart

**As a** Priya Nair (Outdoor Enthusiast), **I want to** see a temperature trend chart for the week alongside the daily forecast list, **so that** I can understand at a glance whether temperatures are rising, falling, or stable across the planning horizon.

**Acceptance Criteria:**
- [ ] A Recharts `AreaChart` temperature trend is rendered adjacent to or below the 7-day daily list
- [ ] The chart shows the daily high temperature curve across all 7 days
- [ ] The chart renders correctly at all viewport widths from 375px to 1280px+ without overflow
- [ ] A screen-reader-accessible `sr-only` data table or `aria-label` description is provided as a fallback for the chart

**Priority:** P0 | **Feature Ref:** F3

---

## Epic 4: Weather Icons & Visual Indicators (F4)

*Accessibility-critical. Icons must never appear without text labels.*

### US-4.1: Full WMO Code Icon Coverage

**As a** Priya Nair (Outdoor Enthusiast), **I want to** see a specific, recognizable icon for every weather condition the API can return, **so that** I never see a generic fallback icon that gives me no information about the actual condition.

**Acceptance Criteria:**
- [ ] The icon set covers the full WMO weather interpretation code range (0–99) as defined in the WMO_CONDITIONS mapping
- [ ] Every WMO code produces a specific named icon and text label — no generic fallback for any valid code
- [ ] Icons are rendered as SVGs for crisp display at all screen densities

**Priority:** P0 | **Feature Ref:** F4

---

### US-4.2: Day/Night Icon Variants

**As a** Maya Torres (Casual Checker), **I want to** see icons that match the actual time of day (sun during the day, moon/stars at night), **so that** the visual immediately tells me whether it's currently daytime or nighttime at the searched location.

**Acceptance Criteria:**
- [ ] All clear, partly-cloudy, and overcast condition codes have separate day and night icon variants
- [ ] The `is_day` value from the API (per the city's local timezone via `timezone=auto`) determines which variant is shown
- [ ] A sun icon never appears when `is_day = 0`; a moon/stars icon never appears when `is_day = 1`
- [ ] Day/night variants are applied consistently in: current conditions, hourly forecast cards, and daily forecast (daily rows always use daytime icons as representative)

**Priority:** P0 | **Feature Ref:** F4

---

### US-4.3: Condition-Aware Background Gradient

**As a** Maya Torres (Casual Checker), **I want to** see the app's hero background color shift to reflect the current weather state and time of day, **so that** the visual immediately communicates weather context before I read any data.

**Acceptance Criteria:**
- [ ] The current conditions hero section uses a CSS gradient background that changes based on weather condition and time of day
- [ ] Each condition × time-of-day combination uses a specific gradient defined using USWDS color tokens
- [ ] All text overlaid on any gradient background achieves a minimum 4.5:1 contrast ratio (WCAG 1.4.3)
- [ ] Gradients do not animate (static only in v1; animated backgrounds are out of scope)
- [ ] Contrast ratio for all condition × time combinations is validated via contrast analyser before ship

**Priority:** P0 | **Feature Ref:** F4

---

## Epic 5: Responsive Layout (F5)

*One codebase, all viewports. Mobile-first.*

### US-5.1: Usable Layout on Mobile (375px)

**As a** Maya Torres (Casual Checker), **I want to** use the app on my phone (375px viewport) with all data visible and all touch targets usable, **so that** I don't have to scroll horizontally or struggle to tap small buttons.

**Acceptance Criteria:**
- [ ] At 375px viewport width, the layout is a single column with all data visible without horizontal overflow
- [ ] All tap targets meet the 44×44px minimum (WCAG 2.5.8)
- [ ] No horizontal scrollbar appears at 375px except within the intentional hourly forecast scroll container
- [ ] Text does not truncate or overlap at 375px

**Priority:** P0 | **Feature Ref:** F5

---

### US-5.2: Usable Layout on Tablet and Desktop

**As a** David Park (Commuter), **I want to** use the app on my laptop browser with a layout that takes advantage of the wider screen, **so that** I can see more information at once without excessive white space or awkward single-column stretching.

**Acceptance Criteria:**
- [ ] At 768px+ (USWDS `tablet` breakpoint), a two-column layout is applied where appropriate
- [ ] At 1024px+ (USWDS `desktop` breakpoint), the forecast and details panels are arranged horizontally
- [ ] USWDS responsive breakpoints (`mobile-lg`, `tablet`, `desktop`) are used for all layout transitions — no custom pixel-specific media queries
- [ ] No single component stretches beyond a readable max-width at desktop viewports
- [ ] Layout is correct at 125% browser zoom (supports Priya Nair's use pattern)

**Priority:** P0 | **Feature Ref:** F5

---

## Epic 6: Secondary Weather Details Panel (F6)

*Progressive disclosure. Collapsed by default to protect the casual experience.*

### US-6.1: Expand Details Panel to View Secondary Metrics

**As a** Priya Nair (Outdoor Enthusiast), **I want to** tap/click a "Details" panel trigger to expand a section showing UV index, wind direction, visibility, and sunrise/sunset, **so that** I can access secondary metrics when planning outdoor activities without those details cluttering the main view during quick checks.

**Acceptance Criteria:**
- [ ] A "Details" section implemented as a USWDS `usa-accordion` is present on the main screen
- [ ] The panel is collapsed by default on page load — secondary metrics are not visible until the user expands it
- [ ] A single tap/click (or Enter/Space keyboard input) on the accordion trigger expands the panel
- [ ] The expanded panel shows: UV index, wind speed (mph), wind direction (cardinal + degrees), visibility (km), humidity (if not shown in hero), sunrise time, sunset time
- [ ] All times use the searched city's local timezone
- [ ] Panel state returns to collapsed on page reload — does not persist open

**Priority:** P1 | **Feature Ref:** F6

---

### US-6.2: Accessible Accordion Behavior for Details Panel

**As a** David Park (Commuter), **I want to** operate the Details panel accordion using the keyboard with correct ARIA state announcements, **so that** I can expand and collapse the panel as part of a keyboard-only workflow without losing focus or getting unexpected behavior.

**Acceptance Criteria:**
- [ ] The accordion trigger has `aria-expanded="false"` when collapsed and `aria-expanded="true"` when expanded
- [ ] The trigger has `aria-controls` pointing to the panel content element's `id`
- [ ] The panel content has `hidden` attribute when collapsed (not just `display: none` via CSS)
- [ ] Focus is not moved unexpectedly when the panel expands or collapses
- [ ] The accordion trigger has a minimum 44px height touch target

**Priority:** P1 | **Feature Ref:** F6

---

## Epic 7: Data Freshness & Stale State Handling (F7)

*Trust and reliability. Every error path covered — no blank screens.*

### US-7.1: View Data Freshness Indicator

**As a** David Park (Commuter), **I want to** always see a "Updated X minutes ago" indicator on the main screen when data is loaded, **so that** I can decide whether the data is current enough to trust for my commute timing decision.

**Acceptance Criteria:**
- [ ] A freshness timestamp is visible on the main screen at all times when weather data is loaded
- [ ] The timestamp shows relative time: "Updated just now", "Updated 5 minutes ago", etc.
- [ ] The timestamp updates every minute without a page reload
- [ ] TanStack Query `staleTime` is set to 10 minutes — data is not re-fetched on every component mount

**Priority:** P1 | **Feature Ref:** F7

---

### US-7.2: Graceful Offline Handling with Cached Data

**As a** David Park (Commuter), **I want to** see cached weather data with a clear notice when I lose network connectivity, **so that** I still have the last known forecast available rather than a blank screen.

**Acceptance Criteria:**
- [ ] When the network is offline and cached data exists, the cached data is displayed with a USWDS `usa-alert--info` notice: "Showing cached data from X minutes ago"
- [ ] The alert clearly communicates that data may be outdated
- [ ] When connectivity is restored, TanStack Query automatically re-fetches and updates the display
- [ ] `networkMode: 'offlineFirst'` is configured in TanStack Query to serve cache when offline

**Priority:** P1 | **Feature Ref:** F7

---

### US-7.3: No-Cache Offline Error State

**As a** Maya Torres (Casual Checker), **I want to** see a helpful error message if the app cannot load data and has no cached data, **so that** I know to check my connection rather than waiting indefinitely.

**Acceptance Criteria:**
- [ ] When offline with no cached data, a USWDS `usa-alert--error` is shown: "Unable to load weather — check your connection"
- [ ] No blank screen is ever shown — the error message fills the weather data area
- [ ] A "Try again" button is visible within the error alert to trigger a retry once connectivity returns
- [ ] The search input remains functional so the user can try a different city when connectivity returns

**Priority:** P1 | **Feature Ref:** F7

---

## Epic 8: Accessibility — WCAG AA Compliance (F8)

*Non-negotiable for production quality. Verified by automated tooling and manual testing.*

### US-8.1: Full Keyboard Navigation

**As a** David Park (Commuter), **I want to** navigate the entire app using only a keyboard with a logical tab order, **so that** I can operate the app fluently from my laptop without switching between keyboard and mouse.

**Acceptance Criteria:**
- [ ] All interactive elements (search input, GPS button, suggestion list, unit toggle, hourly scroll, details accordion, footer link) are reachable via Tab key
- [ ] Tab order is logical: header search → hourly forecast → 7-day forecast → details panel → unit toggle → footer
- [ ] No keyboard traps — Tab always moves forward, Shift+Tab always moves backward through the focus order
- [ ] Skip navigation link ("Skip to main content") is the first focusable element on the page

**Priority:** P1 | **Feature Ref:** F8

---

### US-8.2: Visible USWDS Focus Indicators

**As a** David Park (Commuter), **I want to** always see a clearly visible focus ring on whatever element is currently focused, **so that** I always know where I am in the page during keyboard navigation.

**Acceptance Criteria:**
- [ ] USWDS-standard focus indicator (2px solid outline with 2px offset) is applied to all focusable elements via `:focus-visible`
- [ ] Focus indicator is visible on all condition-aware background gradients (contrast ratio of focus ring meets 3:1 against the background per WCAG 1.4.11)
- [ ] No element uses `outline: none` without providing an equivalent visible focus indicator

**Priority:** P1 | **Feature Ref:** F8

---

### US-8.3: Screen Reader Announcements for Weather Updates

**As a** screen reader user, **I want to** hear an announcement when weather data loads or updates after a location change, **so that** I know new data is available without having to navigate through the entire page to find it.

**Acceptance Criteria:**
- [ ] An `aria-live="polite"` region announces weather data updates when a new location is selected: "Weather loaded for [City Name]"
- [ ] `aria-live="polite"` also announces when the freshness indicator updates
- [ ] The autocomplete suggestion count is announced when the suggestion list opens: "N results available"
- [ ] Dynamic content changes (data freshness, loading state transitions) are announced appropriately

**Priority:** P1 | **Feature Ref:** F8

---

### US-8.4: Color Independence for Weather Conditions

**As a** Priya Nair (Outdoor Enthusiast), **I want to** distinguish all weather conditions using icon shapes and text labels rather than color, **so that** I can reliably read the forecast even with my color vision deficiency (deuteranomaly).

**Acceptance Criteria:**
- [ ] Every weather condition icon is always accompanied by a text label — no icon-only display anywhere in the app
- [ ] No weather condition is communicated by color alone (WCAG 1.4.1)
- [ ] All condition backgrounds achieve ≥4.5:1 contrast ratio with overlaid text (WCAG 1.4.3) — validated across all condition × time-of-day combinations
- [ ] Precipitation probability values (percentages) are shown as text, not color bars only
- [ ] axe-core automated audit passes with zero color-contrast violations on all tested pages

**Priority:** P1 | **Feature Ref:** F8

---

### US-8.5: Reduced Motion Support

**As a** user with vestibular disorders, **I want to** use the app without triggering motion sickness from animations, **so that** I can access weather information comfortably.

**Acceptance Criteria:**
- [ ] All CSS transitions and animations check `prefers-reduced-motion: reduce` media query
- [ ] When `prefers-reduced-motion` is active, transitions are set to `0ms` or replaced with instant state changes
- [ ] Recharts chart animations are disabled when `prefers-reduced-motion` is active

**Priority:** P1 | **Feature Ref:** F8

---

## Epic 9: Attribution & Deployment (F9)

*Legal requirement (Open-Meteo license) and deployment prerequisites.*

### US-9.1: Open-Meteo Attribution in Footer

**As a** product owner, **I want to** display the required Open-Meteo CC BY 4.0 attribution link in the app footer on every page load, **so that** the app complies with the Open-Meteo API license terms.

**Acceptance Criteria:**
- [ ] A USWDS `usa-footer` (minimal variant) is present on every page of the app
- [ ] The footer contains a visible link to Open-Meteo's website with text that satisfies CC BY 4.0 attribution requirements
- [ ] The link opens in a new tab (`target="_blank"`) with `rel="noopener noreferrer"`
- [ ] The footer attribution is visible on all viewport widths (375px–1280px+)
- [ ] The footer link is keyboard-focusable and has a visible focus indicator

**Priority:** P0 | **Feature Ref:** F9

---

### US-9.2: Vercel HTTPS Deployment with Auto-Deploy from GitHub

**As a** developer, **I want to** deploy the app to a public HTTPS URL on Vercel that automatically updates when I push to the main branch, **so that** the live app always reflects the latest code without manual deployment steps.

**Acceptance Criteria:**
- [ ] The app is deployed to Vercel and served over HTTPS
- [ ] HTTPS is required for the Browser Geolocation API to function — the GPS button works correctly on the production URL
- [ ] A push to the `main` branch on GitHub automatically triggers a Vercel deployment (zero manual deploy steps)
- [ ] Preview deployments are created for pull requests, enabling pre-merge testing
- [ ] The production URL is publicly accessible without authentication

**Priority:** P0 | **Feature Ref:** F9

---

## Story Index

| Story ID | Title | Persona | Priority | Feature |
|---|---|---|---|---|
| US-0.1 | City Name Search with Autocomplete | Maya Torres | P0 | F0 |
| US-0.2 | Keyboard Navigation in Autocomplete | David Park | P0 | F0 |
| US-0.3 | GPS Geolocation (Opt-In) | Maya Torres | P0 | F0 |
| US-0.4 | Recent Locations Quick-Select | Maya Torres | P1 | F0 |
| US-0.5 | City Not Found Error State | Maya Torres | P0 | F0 |
| US-1.1 | View Current Temperature and Condition | Maya Torres | P0 | F1 |
| US-1.2 | View Supporting Current Data | Maya Torres | P0 | F1 |
| US-1.3 | Toggle Temperature Units (°C / °F) | Maya Torres | P0 | F1 |
| US-1.4 | Loading State for Current Conditions | Maya Torres | P0 | F1 |
| US-1.5 | Error State for Current Conditions | Maya Torres | P0 | F1 |
| US-2.1 | View 24-Hour Forecast in Scrollable Row | David Park | P1 | F2 |
| US-2.2 | Correct Timezone in Hourly Labels | David Park | P0 | F2 |
| US-2.3 | Accessible Touch Targets on Hourly Cards | David Park | P1 | F2 |
| US-3.1 | View Full 7-Day Daily Forecast | Priya Nair | P0 | F3 |
| US-3.2 | View Temperature Trend Chart | Priya Nair | P0 | F3 |
| US-4.1 | Full WMO Code Icon Coverage | Priya Nair | P0 | F4 |
| US-4.2 | Day/Night Icon Variants | Maya Torres | P0 | F4 |
| US-4.3 | Condition-Aware Background Gradient | Maya Torres | P0 | F4 |
| US-5.1 | Usable Layout on Mobile (375px) | Maya Torres | P0 | F5 |
| US-5.2 | Usable Layout on Tablet and Desktop | David Park | P0 | F5 |
| US-6.1 | Expand Details Panel to View Secondary Metrics | Priya Nair | P1 | F6 |
| US-6.2 | Accessible Accordion Behavior for Details Panel | David Park | P1 | F6 |
| US-7.1 | View Data Freshness Indicator | David Park | P1 | F7 |
| US-7.2 | Graceful Offline Handling with Cached Data | David Park | P1 | F7 |
| US-7.3 | No-Cache Offline Error State | Maya Torres | P1 | F7 |
| US-8.1 | Full Keyboard Navigation | David Park | P1 | F8 |
| US-8.2 | Visible USWDS Focus Indicators | David Park | P1 | F8 |
| US-8.3 | Screen Reader Announcements for Weather Updates | Screen reader user | P1 | F8 |
| US-8.4 | Color Independence for Weather Conditions | Priya Nair | P1 | F8 |
| US-8.5 | Reduced Motion Support | Motion-sensitive user | P1 | F8 |
| US-9.1 | Open-Meteo Attribution in Footer | Product owner | P0 | F9 |
| US-9.2 | Vercel HTTPS Deployment with Auto-Deploy | Developer | P0 | F9 |

**Total stories:** 32
**P0 (Critical):** 18 stories
**P1 (High):** 14 stories

---

*UserStories version 1.0 — generated 2026-05-01*
*Sources: PRD-SimpleWeatherApp.md v1.1, FRD-SimpleWeatherApp.md v1.0, PERSONAS-SimpleWeatherApp.md*
*Next documents: JOURNEYS, STORY-MAP, UX-Mockup*
