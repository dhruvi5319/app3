# Functional Requirements Document
## Simple Weather App

**Version:** 1.0
**Date:** 2026-05-01
**Status:** Active
**Derived from:** PRD-SimpleWeatherApp.md v1.1
**Confidence:** HIGH

---

## Table of Contents

1. [Document Overview](#1-document-overview)
2. [F0 — Location Search & Detection](#2-f0--location-search--detection)
3. [F1 — Current Conditions Display](#3-f1--current-conditions-display)
4. [F2 — Hourly Forecast](#4-f2--hourly-forecast)
5. [F3 — 7-Day Daily Forecast](#5-f3--7-day-daily-forecast)
6. [F4 — Weather Icons & Visual Indicators](#6-f4--weather-icons--visual-indicators)
7. [F5 — Responsive Layout](#7-f5--responsive-layout)
8. [F6 — Secondary Weather Details Panel](#8-f6--secondary-weather-details-panel)
9. [F7 — Data Freshness & Stale State Handling](#9-f7--data-freshness--stale-state-handling)
10. [F8 — Accessibility (WCAG AA Compliance)](#10-f8--accessibility-wcag-aa-compliance)
11. [F9 — Attribution & Deployment](#11-f9--attribution--deployment)
12. [API Integration Specifications](#12-api-integration-specifications)
13. [Data Transformation Requirements](#13-data-transformation-requirements)
14. [Component Architecture](#14-component-architecture)
15. [Data Storage Schema](#15-data-storage-schema)
16. [Global Error Handling](#16-global-error-handling)

---

## 1. Document Overview

This Functional Requirements Document (FRD) defines the exact behavior of each feature in the Simple Weather App. It is derived from PRD-SimpleWeatherApp.md v1.1 and provides developer-ready specifications including inputs, outputs, validation rules, error states, API details, and USWDS component mappings. Every feature listed in the PRD Feature Index (F0–F9) is covered with enough specificity that implementation requires no further design decisions.

**USWDS Note:** All UI components must conform to U.S. Web Design System (USWDS) standards. Where a USWDS component or pattern is prescribed, it must be implemented using the USWDS `usa-` naming convention and ARIA roles, preserving keyboard behavior and focus management as documented in the USWDS component library.

**Accessibility baseline:** WCAG 2.2 Level AA is the minimum for every feature. USWDS components serve as the accessibility foundation. All custom components must match or exceed USWDS ARIA patterns.

---

## 2. F0 — Location Search & Detection

**Description:** F0 is the app's primary entry point and a prerequisite for all other features. It enables users to find a location either by typing a city name (with autocomplete) or by using the device's GPS. The search bar is always visible and always functional — GPS is a secondary enhancement that must never gate access to search. The search input is implemented as a USWDS `usa-search` component and the autocomplete suggestion list follows the USWDS combo-box pattern with full keyboard navigation.

**USWDS Components:**
- `usa-search` — wraps the city text input and submit button
- Combo-box / list-box pattern — autocomplete suggestion dropdown with `role="listbox"` and `role="option"` children
- `usa-button--outline` — GPS opt-in button
- `usa-tag` — recent location chips displayed below the search input
- `aria-live="polite"` region — announces suggestion count and selection feedback to screen readers

**Terminology:**
- **Geocoding:** Converting a city name string into geographic coordinates (latitude, longitude) via the Open-Meteo Geocoding API
- **Reverse Geocoding:** Converting GPS coordinates (latitude, longitude) into a human-readable city/locality name via the Nominatim API
- **Autocomplete Suggestion:** A candidate location result returned by the Open-Meteo Geocoding API, displayed as a selectable option in the suggestion list
- **Active Location:** The currently selected location whose coordinates are used to fetch all weather data
- **Recent Locations:** Up to 5 previous selections persisted in `localStorage` as quick-select entries
- **Geolocation Permission:** Browser permission for accessing the device's GPS sensor, requested only on explicit user action

**Sub-features:**
- City name text input with autocomplete suggestion list (Open-Meteo Geocoding API)
- GPS opt-in button triggering browser geolocation
- Reverse geocoding of GPS coordinates to display a human-readable city name (Nominatim)
- Recent location chip strip persisted in `localStorage`
- Keyboard navigation within the suggestion list (arrow keys, Enter to select, Escape to dismiss)
- Clear/reset button to return to empty search state

**Process — City Search:**
1. User focuses the `usa-search` text input
2. User types 2 or more characters
3. App debounces input for 300 ms, then issues a GET request to the Open-Meteo Geocoding API with the typed string
4. App displays up to 5 suggestion items in the dropdown list (`role="listbox"`); each item shows city name, admin region, and country
5. If the API returns zero results, the suggestion list is not shown; no error state is triggered at this step (error handling is per F7)
6. User selects a suggestion (mouse click, Enter key, or touch tap)
7. App sets the selected result's `latitude`, `longitude`, and `display_name` as the Active Location
8. App immediately triggers weather data fetch (see F1, F2, F3)
9. App adds the selected location to `localStorage` recent list (see Data Storage Schema §15)
10. Suggestion list closes; search input displays the selected city name

**Process — GPS Geolocation:**
1. User clicks/taps the `usa-button--outline` GPS icon button labeled "Use my location"
2. App calls `navigator.geolocation.getCurrentPosition()` with a 10-second timeout
3. If browser prompt is shown, user can approve or deny
4. **Approved:** App receives `{ latitude, longitude }` coordinates
5. App issues a GET request to the Nominatim Reverse Geocoding API with the coordinates
6. Nominatim returns a locality name; app sets this as the Active Location display name, with the GPS coordinates as Active Location coordinates
7. App triggers weather data fetch (same as step 8 of City Search process)
8. **Denied / timed out / unavailable:** App displays no error state; the search input remains focused and functional; GPS button reverts to its default state; no blank screen

**Inputs:**
- `query` (string, required): City name typed by user; minimum 2 characters to trigger API call
- `coordinates` (object, optional): `{ latitude: number, longitude: number }` from browser Geolocation API; only populated on GPS flow

**Outputs:**
- `activeLocation` (object): `{ name: string, latitude: number, longitude: number, country: string, admin1: string }`
- Suggestion list (array of up to 5 candidate locations) rendered in the dropdown
- Updated recent locations strip (up to 5 `usa-tag` chips)

**Validation:**
- Query must be at least 2 characters before any API call is made
- Query is trimmed of leading/trailing whitespace before sending to the API
- Coordinates from GPS must be valid finite numbers; `Infinity`, `NaN`, or out-of-range values (-90 > lat > 90, -180 > lon > 180) are rejected silently and treated as geolocation failure
- Only the first 5 suggestions returned by the API are rendered; additional results are discarded
- Recent locations list is capped at 5 entries; oldest entry is dropped when a 6th is added

**Error States:**

| Scenario | Trigger | USWDS Component | Message | Behavior |
|---|---|---|---|---|
| Geocoding API returns 0 results | `results.length === 0` | `usa-alert--info` below input | "City not found — try a different spelling" | Alert shown below input; input remains editable |
| Geocoding API network error | `fetch` throws or returns non-2xx | `usa-alert--error` | "Search unavailable — check your connection" | Alert shown; input remains editable |
| Geolocation denied | `PositionError.code === 1` | None (silent) | No message shown | GPS button reverts; search input remains primary |
| Geolocation timeout | `PositionError.code === 3` / 10s elapsed | `usa-alert--warning` | "Location detection timed out — search by city name" | Alert shown briefly; auto-dismisses after 5s |
| Nominatim reverse geocoding failure | Non-2xx or network error | None (silent fallback) | No message; coordinates used directly | Active Location `name` set to `"${lat}, ${lon}"` as fallback |

---

## 3. F1 — Current Conditions Display

**Description:** The hero section of the app presents the most important weather snapshot immediately on load for the active location. Temperature must be the visually dominant element, displayed as an integer using USWDS type scale tokens. All supporting data (feels-like, high/low, precipitation probability, humidity, wind) must fit above the fold on a 375px mobile viewport without scrolling. The °C/°F unit toggle must be on-screen at all times — never inside a settings panel. Loading and error states are handled with USWDS skeleton and alert components.

**USWDS Components:**
- `font-size-3xl` (or `usa-display`) — current temperature (dominant)
- `font-size-lg` — feels-like, high/low pair
- `usa-button-group` or segmented control — °C / °F unit toggle
- `usa-tag` — precipitation probability badge
- Skeleton loading pattern (`usa-placeholder`) — shown while data is fetching
- `usa-alert--error` — shown on API fetch failure
- `aria-live="polite"` region — announces new weather data when location changes

**Terminology:**
- **WMO Code:** World Meteorological Organization weather interpretation code (integer 0–99) returned by Open-Meteo in the `weathercode` field; mapped to condition label and icon (see §13 Data Transformation)
- **Feels-Like Temperature:** Apparent temperature accounting for wind chill and humidity (`apparent_temperature` from Open-Meteo)
- **Day Index 0:** The current calendar day in the location's local timezone
- **Current Hour:** The hour index in the hourly array matching the current local time at the location

**Sub-features:**
- Current temperature (integer, °C or °F) — primary display element
- Feels-like temperature — secondary display
- Condition label + icon (day/night variant) — from WMO code mapping
- Today's high / low temperatures — from daily array index 0
- Today's precipitation probability — from daily `precipitation_probability_max[0]`
- Humidity — from current hourly `relative_humidity_2m`
- Wind speed — from current `windspeed_10m`
- °C / °F unit toggle with `localStorage` persistence
- Skeleton loading state
- Error state (USWDS `usa-alert--error`)

**Process:**
1. Active Location is set (from F0)
2. TanStack Query issues GET request to Open-Meteo Forecast API with Active Location coordinates; `timezone=auto` param is mandatory
3. While loading, render USWDS skeleton placeholders for all data fields in the hero section
4. On success, extract values from API response (see Data Transformation §13)
5. Apply temperature unit conversion if user preference is °F
6. Map `current_weather.weathercode` to condition label and icon identifier using WMO code table (see §13)
7. Determine day/night variant: compare `current_weather.time` against `daily.sunrise[0]` and `daily.sunset[0]` in location local timezone
8. Render hero section with all fields populated
9. Announce new weather data via `aria-live="polite"` region: `"Weather updated for [City Name]: [temp]°[unit], [condition]"`
10. On API failure, replace skeleton with `usa-alert--error` (see Error States)

**Inputs:**
- `activeLocation` (object): `{ latitude, longitude, name }` — set by F0
- `unitPreference` (string): `"celsius"` or `"fahrenheit"` — read from `localStorage` key `weather_unit`; defaults to `"celsius"`

**Outputs:**
- Rendered hero section displaying: current temperature (integer), feels-like temperature (integer), condition label (string), condition icon (day/night variant), today's high temperature (integer), today's low temperature (integer), precipitation probability (integer, %), humidity (integer, %), wind speed (integer, km/h or mph depending on unit)
- `aria-live` announcement string

**Open-Meteo Fields Used:**

| Display Field | API Source | Array Index | Transformation |
|---|---|---|---|
| Current temperature | `current_weather.temperature` | — | `Math.round()` |
| Feels-like temperature | `hourly.apparent_temperature[currentHourIndex]` | Current hour in location timezone | `Math.round()` |
| Condition label | `current_weather.weathercode` | — | WMO code map lookup |
| Day/night variant | `current_weather.time` vs `daily.sunrise[0]`, `daily.sunset[0]` | 0 | Compare ISO timestamps |
| Today high | `daily.temperature_2m_max[0]` | 0 | `Math.round()` |
| Today low | `daily.temperature_2m_min[0]` | 0 | `Math.round()` |
| Precipitation probability | `daily.precipitation_probability_max[0]` | 0 | Display as-is (integer %) |
| Humidity | `hourly.relative_humidity_2m[currentHourIndex]` | Current hour | Display as-is (integer %) |
| Wind speed | `current_weather.windspeed` | — | `Math.round()`; convert if °F unit selected |

**Unit Conversion:**
- Temperature: `°F = Math.round((°C × 9/5) + 32)` — conversion applied after rounding the Celsius value
- Wind speed: when unit is `"fahrenheit"`, display in mph: `mph = Math.round(km/h × 0.621371)`; when `"celsius"`, display in km/h

**Validation:**
- Unit preference must be exactly `"celsius"` or `"fahrenheit"`; any other `localStorage` value defaults to `"celsius"`
- Temperature values must be finite numbers; `NaN` or `null` values render as `"—"` (em dash placeholder)
- Precipitation probability must be in range 0–100; values outside this range render as `"—"`
- WMO code must be in range 0–99; unknown codes fall back to `"Unknown"` label and a generic cloud icon

**Error States:**

| Scenario | HTTP Status | USWDS Component | Message | Behavior |
|---|---|---|---|---|
| Weather API fetch failure | Non-2xx or network error | `usa-alert--error` | "Unable to load weather — check your connection" | Alert replaces skeleton; retry button offered |
| API returns malformed JSON | Parse error | `usa-alert--error` | "Weather data unavailable — try again" | Same as above |
| `activeLocation` not set | — | Hero section hidden | No message; search prompt shown | F0 search input is the visible CTA |

---

## 4. F2 — Hourly Forecast

**Description:** A horizontally scrollable strip of 24 forecast cards shows weather conditions hour-by-hour for the next 24 hours from the current local time at the active location. This component serves commuters and anyone planning within the current day. All cards follow the USWDS card component pattern for consistent padding, border, focus styles, and 44px touch targets. The component reuses data from the same TanStack Query cache entry as F1 — no additional API call is made.

**USWDS Components:**
- `usa-card` — each hourly card (hour label, icon, temperature, precipitation probability)
- `role="region"` with `aria-label="Hourly forecast"` — scroll container landmark
- Keyboard-accessible horizontal scroll (arrow keys move focus between cards)
- USWDS focus indicator (2px solid offset outline) on each card when focused

**Terminology:**
- **Forecast Window:** The 24 hourly slots starting from `currentHourIndex + 1` in the Open-Meteo `hourly` arrays
- **Current Hour Index:** Derived by finding the hourly timestamp in the API response that matches the current local hour at the location (using `timezone` offset in the API response)
- **Day/Night Boundary:** The transition times (`daily.sunrise[0]`, `daily.sunset[0]`) used to assign day or night icon variant per hour

**Sub-features:**
- 24 horizontally scrollable USWDS card tiles (one per hour)
- Per-card: hour label (e.g., "2 PM"), condition icon (day/night variant), temperature (integer), precipitation probability (integer %)
- Keyboard navigation: arrow keys scroll through cards; Tab moves focus
- Cards are non-interactive (display only); no click/tap action required
- Scroll indicator (shadow gradient at right edge) hints at additional cards off-screen

**Process:**
1. F1 weather data is loaded in TanStack Query cache
2. F2 component reads the same cache entry — no new API call
3. Derive `currentHourIndex` from the location's local hour matching the `hourly.time` array
4. Slice `hourly.time`, `hourly.temperature_2m`, `hourly.weathercode`, `hourly.precipitation_probability` starting at `currentHourIndex + 1` for 24 elements
5. For each slot, determine day/night variant by comparing the slot's timestamp against `daily.sunrise` and `daily.sunset` values (crossing day boundaries requires checking `daily.sunrise[1]`, `daily.sunset[1]` where applicable)
6. Apply temperature unit conversion if user preference is °F
7. Render 24 USWDS card components in a horizontally scrollable container
8. Set `aria-label` on the scroll container: `"Hourly forecast for [City Name]"`
9. Render skeleton cards if data is loading; render nothing (section hidden) if no Active Location

**Inputs:**
- `hourlyData` (object): sliced arrays from TanStack Query cache — no separate fetch
- `unitPreference` (string): from `localStorage`
- `sunrise` / `sunset` arrays: from `daily` block in same API response

**Outputs:**
- 24 USWDS card elements rendered in a horizontal scroll container
- Each card: `{ hourLabel: string, iconId: string, temperature: number (integer), precipitationProbability: number (integer) }`

**Open-Meteo Fields Used:**

| Card Field | API Source | Index Range | Transformation |
|---|---|---|---|
| Hour label | `hourly.time[i]` | currentHour+1 to currentHour+24 | `Intl.DateTimeFormat` with `{ hour: "numeric" }` in location timezone |
| Temperature | `hourly.temperature_2m[i]` | Same | `Math.round()`; unit convert if °F |
| Condition icon | `hourly.weathercode[i]` | Same | WMO code map lookup + day/night variant |
| Precipitation % | `hourly.precipitation_probability[i]` | Same | Display as-is (integer) |

**Validation:**
- Exactly 24 cards must render; if fewer than 24 future hourly slots exist in the response, remaining cards render with `"—"` placeholders
- Precipitation probability must be 0–100; clamp out-of-range values
- Hour labels must use `Intl.DateTimeFormat` with the location's IANA timezone string from the API response — never the browser's local timezone
- Day/night boundary must be evaluated per-slot, not globally for the whole strip

**Error States:**

| Scenario | USWDS Component | Message | Behavior |
|---|---|---|---|
| Parent data fetch failed | Inherits F1 error state | — | F2 section is hidden; F1 error alert is sufficient |
| `hourly` array missing from response | `usa-alert--warning` inline | "Hourly forecast unavailable" | Section renders message; rest of app unaffected |

---

## 5. F3 — 7-Day Daily Forecast

**Description:** A vertical list of 7 daily forecast rows gives users a planning view for the upcoming week. Each row shows the day name, a representative daytime condition icon, the day's high and low temperatures, and precipitation probability. Below the row list, a Recharts `AreaChart` visualizes the temperature trend curve across all 7 days. This component reuses the same TanStack Query cache entry as F1 and F2. The region is marked with `role="region"` and `aria-label="7-day forecast"` for screen reader landmark navigation.

**USWDS Components:**
- `role="region"` with `aria-label="7-day forecast"` — section landmark
- USWDS list pattern — 7 forecast rows with consistent vertical spacing using USWDS spacing tokens
- USWDS `font-size-sm` — low temperature label (visually subordinate to high)
- Recharts `AreaChart` — temperature trend (see accessibility fallback in F8)
- `usa-tag` — precipitation probability badge per row

**Terminology:**
- **Day 0:** The current calendar day in the location's local timezone (not necessarily UTC today)
- **Daily Array:** The `daily` object in the Open-Meteo response containing arrays indexed 0–6 for the 7-day window
- **High/Low Pair:** `temperature_2m_max` / `temperature_2m_min` shown as `High / Low` in that order

**Sub-features:**
- 7 daily forecast rows
- Per-row: abbreviated day name, representative condition icon (always daytime variant for daily rows), integer high, integer low, precipitation probability percentage
- Recharts `AreaChart` temperature trend showing high/low range across 7 days
- Today's row uses "Today" label instead of day-of-week name
- Data sourced from the same TanStack Query cache entry as F1

**Process:**
1. F1 weather data is loaded in TanStack Query cache
2. F3 reads the same cache entry — no new API call
3. For each of the 7 `daily` array indices (0–6):
   a. Derive day label: index 0 → "Today"; indices 1–6 → abbreviated weekday using `Intl.DateTimeFormat` with `{ weekday: "short" }` and location timezone
   b. Map `daily.weathercode[i]` to condition label and daytime icon identifier
   c. Apply `Math.round()` to `daily.temperature_2m_max[i]` and `daily.temperature_2m_min[i]`; apply unit conversion if °F
   d. Read `daily.precipitation_probability_max[i]` as-is
4. Render 7 rows in the list container
5. Prepare `AreaChart` data array: `[{ day, high, low }]` for indices 0–6
6. Render Recharts `AreaChart` with two `Area` data series (high temp and low temp); accessible fallback data table rendered for screen readers (see F8)

**Inputs:**
- `dailyData` (object): `daily` block from TanStack Query cache
- `unitPreference` (string): from `localStorage`
- `timezone` (string): IANA timezone string from API response

**Outputs:**
- 7 rendered daily rows
- `AreaChart` rendered with 7 data points per temperature series
- Accessible data table (hidden visually, available to screen readers): 7 rows × columns (day, high, low, precipitation %)

**Open-Meteo Fields Used:**

| Row Field | API Source | Index | Transformation |
|---|---|---|---|
| Day label | `daily.time[i]` | 0–6 | `Intl.DateTimeFormat` weekday short + "Today" override for index 0 |
| Condition icon | `daily.weathercode[i]` | 0–6 | WMO code map; always daytime variant |
| High temperature | `daily.temperature_2m_max[i]` | 0–6 | `Math.round()`; unit convert if °F |
| Low temperature | `daily.temperature_2m_min[i]` | 0–6 | `Math.round()`; unit convert if °F |
| Precipitation % | `daily.precipitation_probability_max[i]` | 0–6 | Display as-is (integer) |

**Validation:**
- Exactly 7 rows must render; if API returns fewer than 7 daily entries, remaining rows render with `"—"` placeholders
- High temperature must be ≥ low temperature; if data violates this (API anomaly), render both values as-is with no reordering
- Day labels must use location timezone — never browser-local timezone
- `AreaChart` data must use the same rounded integer values displayed in rows (no separate floating-point series)

**Error States:**

| Scenario | USWDS Component | Message | Behavior |
|---|---|---|---|
| Parent data fetch failed | Inherits F1 error state | — | F3 section hidden; F1 error alert sufficient |
| `daily` array missing | `usa-alert--warning` inline | "Daily forecast unavailable" | Section renders message; rest of app unaffected |
| Recharts render error | React Error Boundary | Accessible data table shown | Chart replaced by `<table>` fallback; no visible error message to user |

---

## 6. F4 — Weather Icons & Visual Indicators

**Description:** Every weather condition display in the app (current conditions, hourly cards, daily rows) uses a consistent icon set mapped from WMO weather interpretation codes. Icons have day and night variants for all time-sensitive conditions. The current conditions hero section also features a condition-aware background gradient that reflects weather state and time of day, with all text-on-background color combinations validated against WCAG 1.4.3 (4.5:1 minimum contrast) using USWDS color tokens.

**USWDS Components / Design Tokens:**
- USWDS color token pairs — condition-aware gradient palette must use or map to USWDS `color-primary-*`, `color-base-*`, or `color-secondary-*` tokens; no arbitrary hex values in production
- WCAG contrast validation: each background × foreground text color pair verified against the USWDS color contrast matrix
- All icons must have non-empty `alt` text conveying the condition name (WCAG 1.4.1; `aria-label` on SVG or `alt` on `<img>`)

**Terminology:**
- **WMO Code:** Integer 0–99; the `weathercode` field in Open-Meteo responses
- **Day Variant:** Icon showing sun / sunlight / daylight visual cues; used when current time is between `sunrise` and `sunset` at the location
- **Night Variant:** Icon showing moon / stars / darkness; used when current time is outside `sunrise`–`sunset` at the location
- **Condition Group:** WMO codes are grouped into logical buckets (clear, cloudy, foggy, drizzle, rain, freezing rain, snow, shower, thunderstorm) for icon mapping

**Sub-features:**
- Complete WMO code-to-icon mapping (codes 0–99)
- Day/night variant selection per icon
- Condition-aware hero background gradient system
- Consistent icon usage across F1, F2, and F3 components
- Icon alt text for every icon instance

**WMO Code to Condition Mapping:**

| WMO Code(s) | Condition Group | Condition Label | Has Day/Night Variant |
|---|---|---|---|
| 0 | Clear | "Clear Sky" | Yes |
| 1 | Mostly Clear | "Mostly Clear" | Yes |
| 2 | Partly Cloudy | "Partly Cloudy" | Yes |
| 3 | Overcast | "Overcast" | No (same icon day/night) |
| 45 | Foggy | "Foggy" | No |
| 48 | Icy Fog | "Icy Fog" | No |
| 51 | Light Drizzle | "Light Drizzle" | No |
| 53 | Moderate Drizzle | "Drizzle" | No |
| 55 | Dense Drizzle | "Heavy Drizzle" | No |
| 56 | Light Freezing Drizzle | "Freezing Drizzle" | No |
| 57 | Heavy Freezing Drizzle | "Heavy Freezing Drizzle" | No |
| 61 | Slight Rain | "Light Rain" | No |
| 63 | Moderate Rain | "Rain" | No |
| 65 | Heavy Rain | "Heavy Rain" | No |
| 66 | Slight Freezing Rain | "Freezing Rain" | No |
| 67 | Heavy Freezing Rain | "Heavy Freezing Rain" | No |
| 71 | Slight Snow | "Light Snow" | No |
| 73 | Moderate Snow | "Snow" | No |
| 75 | Heavy Snow | "Heavy Snow" | No |
| 77 | Snow Grains | "Snow Grains" | No |
| 80 | Slight Rain Shower | "Light Showers" | No |
| 81 | Moderate Rain Shower | "Showers" | No |
| 82 | Violent Rain Shower | "Heavy Showers" | No |
| 85 | Slight Snow Shower | "Snow Showers" | No |
| 86 | Heavy Snow Shower | "Heavy Snow Showers" | No |
| 95 | Thunderstorm | "Thunderstorm" | No |
| 96 | Thunderstorm w/ Slight Hail | "Thunderstorm with Hail" | No |
| 99 | Thunderstorm w/ Heavy Hail | "Severe Thunderstorm" | No |
| Any unlisted code | Unknown | "Unknown Conditions" | No (generic cloud icon) |

**Day/Night Logic:**
- Input: `currentTime` (ISO string from API), `sunrise` (ISO string from `daily.sunrise[0]`), `sunset` (ISO string from `daily.sunset[0]`), both in location timezone
- Rule: If `currentTime >= sunrise AND currentTime < sunset` → use day variant; otherwise → use night variant
- For hourly slots crossing a day boundary, check against the appropriate day's sunrise/sunset values (`daily.sunrise[1]`, `daily.sunset[1]`, etc.)
- For daily forecast rows (F3): always use daytime variant regardless of current time

**Condition-Aware Background Gradient Palette:**

| Condition Group | Time | Gradient Style | USWDS Token Anchor | Text Color |
|---|---|---|---|---|
| Clear | Day | Sky blue → warm white | `color-cyan-20` → `color-white` | `color-base-darkest` |
| Clear | Night | Deep navy → dark blue | `color-indigo-80` → `color-blue-90` | `color-white` |
| Mostly Clear / Partly Cloudy | Day | Soft blue → light grey | `color-cyan-10` → `color-base-lightest` | `color-base-darkest` |
| Mostly Clear / Partly Cloudy | Night | Dark blue-grey → navy | `color-blue-80` → `color-indigo-90` | `color-white` |
| Overcast | Day | Medium grey → light grey | `color-base-light` → `color-base-lighter` | `color-base-darkest` |
| Overcast | Night | Dark grey → charcoal | `color-base-dark` → `color-base-darker` | `color-white` |
| Rain / Drizzle / Shower | Day/Night | Slate grey → blue-grey | `color-blue-70` → `color-base-dark` | `color-white` |
| Snow | Day/Night | Light grey-blue → white | `color-blue-10` → `color-white` | `color-base-darkest` |
| Thunderstorm | Day/Night | Deep charcoal → dark purple | `color-violet-90` → `color-base-darkest` | `color-white` |
| Fog | Day/Night | Muted warm grey | `color-base-light` | `color-base-darkest` |

**Contrast Validation Rule:** Every background × text color pair in the table above must achieve ≥ 4.5:1 contrast ratio before implementation, verified with a USWDS color token contrast calculator. If a token pairing fails, a darker/lighter variant must be substituted until the ratio passes.

**Validation:**
- Every rendered icon must have a non-empty `alt` attribute (for `<img>`) or `aria-label` (for inline SVG) matching the condition label string
- Icons must never be the sole means of conveying condition (WCAG 1.4.1) — the text label must always be rendered alongside the icon
- Day variant must never appear after sunset; night variant must never appear before sunrise (enforced in `getIconVariant()` utility function)
- Unknown WMO codes must not throw errors; generic cloud icon + "Unknown Conditions" label is the fallback

---

## 7. F5 — Responsive Layout

**Description:** The app renders correctly at all viewport widths from 375px to 1280px+ without horizontal overflow, truncated text, or unusable touch targets. Layout transitions use Tailwind CSS v4 responsive breakpoints configured to match USWDS grid breakpoints exactly. There is one codebase — no separate mobile or desktop implementation. All layout changes are achieved through CSS responsive variants; no JavaScript breakpoint detection is used.

**USWDS Breakpoints (mapped to Tailwind CSS v4):**

| USWDS Name | USWDS Width | Tailwind v4 Key | Layout Behavior |
|---|---|---|---|
| `mobile-lg` | ≥ 480px | `sm` | Slight horizontal padding increase |
| `tablet` | ≥ 640px (USWDS 640) | `md` | Two-column option for hero + side panel |
| `desktop` | ≥ 1024px | `lg` | Full-width multi-column layout |
| (max tested) | 1280px | — | Max content width capped with `mx-auto` |

**USWDS Components:**
- USWDS grid system (`usa-grid`) — column management on tablet and desktop
- `usa-layout-docs` / grid container — max-width wrapper
- All USWDS card, accordion, and button components inherit responsive sizing from USWDS tokens

**Sub-features:**
- Mobile-first single-column layout (primary)
- Tablet optional two-column layout (current conditions + details)
- Desktop multi-column layout (forecast panels arranged horizontally)
- No horizontal overflow at any breakpoint
- All tap targets ≥ 44 × 44 px at all breakpoints

**Layout Specifications:**

**Mobile (< 640px / below USWDS `tablet`):**
- Single column, full-width
- Component render order (top to bottom): search bar → current conditions hero → hourly forecast strip (horizontal scroll) → 7-day daily list + temperature chart → secondary details accordion
- Hero section: temperature stacked vertically above condition label
- Unit toggle: full-width button group below temperature block
- Hourly strip: 100vw container, cards scroll horizontally; overflow hidden on page container (no full-page horizontal scroll)
- Daily rows: full-width list with adequate vertical spacing (USWDS `spacing-3` minimum between rows)

**Tablet (640px – 1023px / USWDS `tablet` to below `desktop`):**
- Two-column grid option: left column (hero + unit toggle) / right column (details panel, if expanded)
- Hourly strip: same horizontal scroll behavior, cards may be slightly wider
- Daily list: same single-column, full-width

**Desktop (≥ 1024px / USWDS `desktop`):**
- Three-region layout: search bar (full width) → main content row (current conditions | forecast panels) → footer
- Current conditions: left panel (~40% width)
- Forecast panels (hourly + daily): right panel (~60% width), stacked vertically or tabbed
- Secondary details accordion: below current conditions in left panel
- Max content width: 1280px, centered with `mx-auto`

**Validation:**
- No element may cause `overflow-x` at any tested viewport width (375px, 480px, 640px, 768px, 1024px, 1280px)
- All interactive elements must meet 44px minimum touch target at all breakpoints — enforced via USWDS component defaults; custom elements must add explicit `min-height: 44px; min-width: 44px` via Tailwind token
- Forecast cards in horizontal scroll must not clip at left/right edges; `padding-inline` must provide visible partial-card hint at right edge on mobile to indicate scrollability
- Text must not truncate with ellipsis on any screen except where explicitly designed (e.g., very long city names may truncate with visible ellipsis in the search bar label)

---

## 8. F6 — Secondary Weather Details Panel

**Description:** A collapsible accordion panel provides advanced weather metrics (UV index, wind direction, visibility, humidity, sunrise, sunset) for users who need more than the hero section provides. The panel is collapsed by default on every page load to keep the primary view clean for casual users. It is implemented using the USWDS accordion component to inherit correct ARIA `expanded`/`collapsed` state management and keyboard interaction without custom implementation.

**USWDS Components:**
- `usa-accordion` — full accordion pattern with `aria-expanded`, `aria-controls`, and `id` wiring
- `usa-accordion__heading` / `usa-accordion__button` — trigger button; must meet 44px touch target
- `usa-accordion__content` — expandable content region
- USWDS spacing tokens for content layout inside the panel

**Terminology:**
- **Collapsed State:** The default state; `aria-expanded="false"` on the accordion button; content region hidden via `hidden` attribute
- **Expanded State:** User-triggered; `aria-expanded="true"`; content region visible
- **Cardinal Direction:** Wind direction as N, NE, E, SE, S, SW, W, NW (derived from degrees)
- **UV Index:** Integer 0–11+ scale; Open-Meteo `hourly.uv_index` field

**Sub-features:**
- Collapsed by default on every page load (no `localStorage` persistence of panel state)
- Expand/collapse toggle via click, tap, Enter key, or Space key
- When expanded: UV index, wind speed, wind direction (cardinal + degrees), visibility, humidity, sunrise time, sunset time
- All time values in location's local timezone
- Panel state does not persist; always collapsed on reload

**Process:**
1. On page load, accordion renders in collapsed state (`aria-expanded="false"`, content hidden)
2. User clicks/taps accordion trigger button (labeled "Weather Details" or equivalent)
3. App toggles `aria-expanded` to `"true"` and removes `hidden` from content region
4. Expanded content reads from same TanStack Query cache as F1 — no new API call
5. Derive values at current hour index for: UV index, wind direction cardinal
6. Display all values with appropriate units
7. User clicks/taps again (or presses Escape if focus is within panel) → collapse: `aria-expanded="false"`, content hidden again

**Inputs:**
- Same TanStack Query cache entry as F1 (no separate API call)
- `currentHourIndex` (integer): derived in same manner as F1/F2

**Outputs (when expanded):**

| Field | Source | Transformation | Unit |
|---|---|---|---|
| UV Index | `hourly.uv_index[currentHourIndex]` | `Math.round()` | Unitless (0–11+) |
| Wind Speed | `current_weather.windspeed` | `Math.round()`; mph if °F unit | km/h or mph |
| Wind Direction | `current_weather.winddirection` | Degrees → cardinal direction (see below) | Cardinal + °e.g. "NW (315°)" |
| Visibility | `hourly.visibility[currentHourIndex]` | `Math.round()` ÷ 1000 (API returns meters) | km or mi if °F unit |
| Humidity | `hourly.relative_humidity_2m[currentHourIndex]` | Display as-is | % |
| Sunrise | `daily.sunrise[0]` | `Intl.DateTimeFormat` `{ hour: "numeric", minute: "2-digit" }` in location timezone | Local time string |
| Sunset | `daily.sunset[0]` | Same | Local time string |

**Wind Direction Conversion:**

| Degrees Range | Cardinal |
|---|---|
| 337.5–360 or 0–22.5 | N |
| 22.5–67.5 | NE |
| 67.5–112.5 | E |
| 112.5–157.5 | SE |
| 157.5–202.5 | S |
| 202.5–247.5 | SW |
| 247.5–292.5 | W |
| 292.5–337.5 | NW |

**Visibility Unit Conversion:** When unit preference is `"fahrenheit"`, convert visibility from km to miles: `mi = Math.round(km × 0.621371)`.

**Validation:**
- Accordion trigger must have a visible text label (not icon-only) — WCAG 1.1.1
- `aria-controls` must reference the correct panel content `id`
- Panel content must use `hidden` HTML attribute (not `display:none` via CSS only) for correct screen reader behavior with USWDS accordion
- UV index must display `"N/A"` if the value is `null` or missing from the API response
- Sunrise/sunset must display in location timezone — not browser timezone

**Error States:**

| Scenario | Behavior |
|---|---|
| Parent data fetch failed | Accordion section hidden; F1 error state handles messaging |
| `uv_index` not in response | Render "N/A" for that field; all other fields render normally |
| Visibility field missing | Render "N/A"; no error thrown |

---

## 9. F7 — Data Freshness & Stale State Handling

**Description:** Users must always know whether the displayed weather data is current or cached. The app communicates data age with an inline freshness timestamp and escalates to USWDS alert banners when data is stale, the network is offline, or a fetch has failed. All status messages use `aria-live` regions to ensure screen reader announcements. The app must never render a completely blank screen — a visible message always appears in place of missing data.

**USWDS Components:**
- `usa-tag` or inline text — "Updated X min ago" freshness indicator; always visible when data is loaded
- `usa-alert--warning` — shown when offline with cached data available
- `usa-alert--error` — shown when offline with no cached data (or API hard failure)
- `usa-alert--info` — shown when city search returns no results (geocoding failure)
- `aria-live="polite"` — wraps all alert and freshness regions

**Terminology:**
- **Stale Time:** TanStack Query `staleTime` value = 10 minutes (600,000 ms); data is considered fresh for 10 minutes after last successful fetch
- **Cache Entry:** TanStack Query cache keyed by `["weather", latitude, longitude]`; persists for the session
- **Stale State:** Data older than `staleTime` but still available in cache; a background refetch is triggered on next component mount
- **Offline State:** `navigator.onLine === false` or network request fails due to connectivity

**Sub-features:**
- Freshness timestamp: "Updated X minutes ago" — always visible when data is loaded
- Offline warning banner (USWDS `usa-alert--warning`) when cached data is shown
- Error banner (USWDS `usa-alert--error`) when no data is available
- Info banner (USWDS `usa-alert--info`) for geocoding no-results
- `aria-live="polite"` on all status regions
- Background refetch after `staleTime` (managed by TanStack Query)

**Process — Freshness Indicator:**
1. On successful data fetch, record `fetchTimestamp = Date.now()` in component state
2. Render freshness label: calculate `Math.floor((Date.now() - fetchTimestamp) / 60000)` minutes
3. Update label every 60 seconds via `setInterval`; clear interval on component unmount
4. When `minutes === 0`, display "Updated just now"; when `minutes === 1`, display "Updated 1 minute ago"; otherwise "Updated X minutes ago"

**Process — Offline State Detection:**
1. Listen to `window` events: `"online"` and `"offline"`
2. If `"offline"` fires and cached data exists: display `usa-alert--warning`: `"You're offline — showing weather data from X minutes ago"`
3. If `"offline"` fires and no cached data exists: display `usa-alert--error`: `"Unable to load weather — check your connection"`
4. If `"online"` fires: dismiss offline alert; trigger TanStack Query `refetch()`

**TanStack Query Configuration:**

| Parameter | Value | Rationale |
|---|---|---|
| `staleTime` | 600,000 ms (10 min) | Prevents redundant API calls on component remounts |
| `cacheTime` / `gcTime` | 1,800,000 ms (30 min) | Keeps data in memory for offline fallback |
| `retry` | 2 | Retry failed requests twice before entering error state |
| `retryDelay` | 1,000 ms (linear) | Brief delay between retries |
| `refetchOnWindowFocus` | `false` | Prevents refetch on every tab switch |

**Alert Content Specifications:**

| Alert Type | USWDS Variant | Heading | Body | Dismissible |
|---|---|---|---|---|
| Offline with cache | `usa-alert--warning` | "Offline Mode" | "Showing weather from [X] minutes ago. Connect to refresh." | No |
| Offline, no cache | `usa-alert--error` | "No Connection" | "Unable to load weather — check your connection." | No |
| API error | `usa-alert--error` | "Weather Unavailable" | "Unable to load weather data. Tap to retry." | Yes (with retry CTA) |
| City not found | `usa-alert--info` | "Location Not Found" | "City not found — try a different spelling." | Yes |
| Data refreshed | `aria-live` only | — | Screen-reader-only: "Weather updated for [City]." | N/A |

**Validation:**
- The freshness indicator must never show a negative number of minutes; clamp to 0 if `fetchTimestamp > Date.now()` (clock skew edge case)
- Offline alert must not appear if data fetch is still in-flight (loading state takes precedence)
- `aria-live` region must be in the DOM from initial render (not conditionally rendered) to ensure screen readers register it as a live region before any announcements

---

## 10. F8 — Accessibility (WCAG AA Compliance)

**Description:** Every component and state in the app must meet WCAG 2.2 Level AA. USWDS components serve as the accessibility baseline; any custom component must match or exceed USWDS ARIA patterns. Accessibility is verified by automated axe-core scanning and manual screen reader testing (VoiceOver on macOS/iOS; NVDA on Windows).

**USWDS Accessibility Foundations:**
- USWDS focus styles: `2px solid` offset outline on all interactive elements — not removed or overridden
- USWDS ARIA patterns: preserved exactly on all adapted components (search, accordion, card, alert, button group)
- USWDS touch target guidance: 44 × 44 px minimum enforced via component defaults

**Terminology:**
- **Focus Ring:** The 2px solid offset outline indicating keyboard focus; must be visible on all interactive elements
- **Live Region:** `aria-live="polite"` HTML attribute on a container that causes screen readers to announce dynamic content changes
- **Skip Link:** A visually-hidden-until-focused link at page top allowing keyboard users to jump to main content
- **Reduced Motion:** `@media (prefers-reduced-motion: reduce)` — CSS query disabling animations for users with vestibular disorders

**Sub-features:**
- Skip to main content link (visible on focus)
- Logical tab order across all interactive elements
- `aria-live` regions for weather data updates and alert messages
- Keyboard operability for all components (search, GPS button, unit toggle, hourly cards, accordion, recent location chips)
- Icon + text label pairing on all condition indicators
- WCAG 1.4.3 contrast compliance on all condition-aware backgrounds
- Screen reader accessible charts (Recharts fallback)
- `prefers-reduced-motion` compliance for all animations

**WCAG Criterion–to–Implementation Mapping:**

| WCAG Criterion | Requirement | Implementation |
|---|---|---|
| 1.1.1 Non-text Content | All icons have text alternative | `alt` on `<img>` icons; `aria-label` on SVG icons; matches condition label string |
| 1.3.1 Info and Relationships | Semantic HTML structure | `<main>`, `<header>`, `<footer>`, `<nav>`, `<section>` used correctly; USWDS landmark roles |
| 1.4.1 Use of Color | Condition not conveyed by color alone | Icon + text label always paired; gradient background is supplemental |
| 1.4.3 Contrast (Minimum) | 4.5:1 for normal text; 3:1 for large text | Validated per gradient palette (§F4 table); USWDS token pairs only |
| 1.4.4 Resize Text | Content reflows at 200% zoom | Single-column flow maintained at 200% browser zoom |
| 1.4.10 Reflow | No horizontal scroll at 320px wide | Tested at 320px viewport; all content reflows to single column |
| 2.1.1 Keyboard | All functionality keyboard-accessible | Tab, Enter, Space, arrow keys; USWDS component keyboard patterns |
| 2.4.3 Focus Order | Logical reading order | DOM order matches visual order; no `tabindex > 0` |
| 2.4.7 Focus Visible | Focus indicator always visible | USWDS 2px solid offset outline; not overridden by any CSS |
| 2.5.8 Target Size | 44 × 44 px minimum | USWDS defaults; explicit `min-h-[44px] min-w-[44px]` on custom elements |
| 3.1.1 Language of Page | `lang` attribute on `<html>` | `<html lang="en">` |
| 4.1.2 Name, Role, Value | All interactive elements have accessible names | USWDS component patterns; explicit `aria-label` where label is icon-only |
| 4.1.3 Status Messages | Status changes announced without focus change | `aria-live="polite"` on freshness indicator, alert regions, and data update announcements |

**Recharts Accessibility:**
- The `AreaChart` rendered in F3 must have a visually hidden accessible fallback
- Implementation: render a `<table>` with `className="sr-only"` (visually hidden but screen-reader readable) containing the same 7-day data (columns: Day, High, Low, Precipitation %)
- The `<AreaChart>` SVG container must have `aria-hidden="true"` to prevent screen readers from attempting to parse the SVG nodes
- The `<table>` must be adjacent to the chart in DOM order

**`prefers-reduced-motion` Rules:**
- All CSS transitions and animations (gradient background shift, card hover, skeleton pulse) must be wrapped in `@media (prefers-reduced-motion: no-preference)`
- Skeleton loading animation must be a static color instead of pulsing animation when `prefers-reduced-motion: reduce` is set
- No JavaScript-driven animations may run when `window.matchMedia("(prefers-reduced-motion: reduce)").matches` is true

**Skip Link Specification:**
- Element: `<a href="#main-content">Skip to main content</a>` as first child of `<body>`
- Visually hidden by default: USWDS `usa-skipnav` class
- Becomes visible on `:focus` — must be the first tab stop on the page
- Target: `<main id="main-content">` — the primary content container

**Keyboard Navigation Map:**

| Component | Keys | Behavior |
|---|---|---|
| Search input | Type, Tab, Escape | Type to trigger autocomplete; Escape clears suggestion list |
| Autocomplete suggestions | Arrow Up/Down, Enter, Escape | Navigate options; Enter selects; Escape closes list |
| GPS button | Enter, Space | Triggers geolocation flow |
| Unit toggle (°C / °F) | Arrow Left/Right (within button group) | Switches unit; focus stays in group |
| Recent location chips | Tab, Enter, Space | Chips are focusable; Enter/Space selects location |
| Hourly forecast strip | Arrow Left/Right | Move focus between hour cards |
| Details accordion | Enter, Space | Toggle expand/collapse |
| Retry button (error state) | Enter, Space | Retriggers data fetch |

---

## 11. F9 — Attribution & Deployment

**Description:** The app is deployed to Vercel with HTTPS (a technical requirement for the Browser Geolocation API) and includes the Open-Meteo CC BY 4.0 attribution on every page load. Deployment is automated via GitHub main branch integration. The footer uses the USWDS `usa-footer` component for semantic structure and design system consistency.

**USWDS Components:**
- `usa-footer` (slim variant) — page footer containing attribution and any secondary links
- `usa-identifier` (optional) — USWDS design system acknowledgment if appropriate for project context

**Terminology:**
- **CC BY 4.0:** Creative Commons Attribution 4.0 International license — Open-Meteo's data license requiring attribution with a link
- **Vercel Project:** The deployment target; linked to the GitHub repository; builds from the `main` branch

**Sub-features:**
- HTTPS deployment on Vercel
- Attribution link in `usa-footer` on every page
- Auto-deploy from GitHub `main` branch push (no manual deploy step)

**Attribution Requirements:**

| Element | Required Content | Implementation |
|---|---|---|
| Attribution text | "Weather data by Open-Meteo" | Visible text in `usa-footer` |
| Attribution link | `href="https://open-meteo.com/"` | Anchor wrapping or alongside attribution text |
| License link | CC BY 4.0 license URL | `href="https://creativecommons.org/licenses/by/4.0/"` |
| Visibility | Must be visible on every page load | In `usa-footer`; not hidden, not conditionally rendered |

**Footer HTML Structure (required):**
```html
<footer class="usa-footer usa-footer--slim">
  <div class="usa-footer__secondary-section">
    <p>
      Weather data by <a href="https://open-meteo.com/" target="_blank" rel="noopener noreferrer">Open-Meteo</a>
      — licensed under <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank" rel="noopener noreferrer">CC BY 4.0</a>
    </p>
  </div>
</footer>
```

**Deployment Specifications:**

| Parameter | Value |
|---|---|
| Platform | Vercel |
| Protocol | HTTPS (enforced by Vercel default) |
| Build command | `vite build` |
| Output directory | `dist/` |
| Auto-deploy trigger | Push to `main` branch |
| Environment variables | None (Open-Meteo requires no API key) |
| Domain | Vercel default (`*.vercel.app`) or custom domain |

**Validation:**
- Geolocation API (`navigator.geolocation`) must be tested on the deployed HTTPS URL — it will not work on `http://localhost` in production browsers
- Attribution link must open in a new tab (`target="_blank"`) with `rel="noopener noreferrer"` for security
- Footer must render on initial load before any location is searched (it is not dependent on weather data)
- Build must produce zero TypeScript errors and zero `any` casts — enforced via `tsc --noEmit` in CI

**Error States:**

| Scenario | Impact | Resolution |
|---|---|---|
| Vercel build fails | Deployment blocked | Fix TypeScript/build errors; re-push to `main` |
| HTTPS not available | Geolocation API fails silently | Vercel enforces HTTPS by default; not applicable |
| Attribution absent from footer | License violation | Attribution is in `usa-footer`; not conditionally rendered |

---

## 12. API Integration Specifications

### 12.1 Open-Meteo Weather Forecast API

**Base URL:** `https://api.open-meteo.com/v1/forecast`

**Authentication:** None required.

**Mandatory Parameters (always included):**

| Parameter | Value | Note |
|---|---|---|
| `latitude` | Active Location latitude | Float; e.g. `51.5085` |
| `longitude` | Active Location longitude | Float; e.g. `-0.1257` |
| `timezone` | `"auto"` | **Non-negotiable** — required for correct local time display |
| `current_weather` | `true` | Returns current conditions block |

**Required `hourly` Parameters:**
```
hourly=temperature_2m,apparent_temperature,weathercode,precipitation_probability,relative_humidity_2m,uv_index,visibility
```

**Required `daily` Parameters:**
```
daily=temperature_2m_max,temperature_2m_min,weathercode,precipitation_probability_max,sunrise,sunset
```

**Full Constructed URL Example:**
```
https://api.open-meteo.com/v1/forecast
  ?latitude=51.5085
  &longitude=-0.1257
  &timezone=auto
  &current_weather=true
  &hourly=temperature_2m,apparent_temperature,weathercode,precipitation_probability,relative_humidity_2m,uv_index,visibility
  &daily=temperature_2m_max,temperature_2m_min,weathercode,precipitation_probability_max,sunrise,sunset
```

**Response Shape (key fields):**

```typescript
interface OpenMeteoForecastResponse {
  timezone: string;           // IANA timezone string, e.g. "Europe/London"
  timezone_abbreviation: string;
  current_weather: {
    temperature: number;      // °C float
    windspeed: number;        // km/h float
    winddirection: number;    // degrees 0–360
    weathercode: number;      // WMO code integer
    time: string;             // ISO 8601 local time string
    is_day: number;           // 0 or 1 (additional day/night helper)
  };
  hourly: {
    time: string[];           // 168 ISO time strings (7 days × 24 hours)
    temperature_2m: number[];
    apparent_temperature: number[];
    weathercode: number[];
    precipitation_probability: number[];
    relative_humidity_2m: number[];
    uv_index: number[];
    visibility: number[];     // meters
  };
  daily: {
    time: string[];           // 7 date strings (YYYY-MM-DD)
    temperature_2m_max: number[];
    temperature_2m_min: number[];
    weathercode: number[];
    precipitation_probability_max: number[];
    sunrise: string[];        // ISO datetime strings in location timezone
    sunset: string[];         // ISO datetime strings in location timezone
  };
}
```

**TanStack Query Key:** `["weather", latitude, longitude]`

**TanStack Query Config:**
```typescript
{
  queryKey: ["weather", latitude, longitude],
  queryFn: () => fetchWeather(latitude, longitude),
  staleTime: 600_000,     // 10 minutes
  gcTime: 1_800_000,      // 30 minutes
  retry: 2,
  retryDelay: 1_000,
  refetchOnWindowFocus: false,
  enabled: latitude !== null && longitude !== null,
}
```

---

### 12.2 Open-Meteo Geocoding API

**Base URL:** `https://geocoding-api.open-meteo.com/v1/search`

**Authentication:** None required.

**Parameters:**

| Parameter | Value | Note |
|---|---|---|
| `name` | User-typed query string | URL-encoded |
| `count` | `5` | Return max 5 results |
| `language` | `"en"` | English result labels |
| `format` | `"json"` | Response format |

**Full Constructed URL Example:**
```
https://geocoding-api.open-meteo.com/v1/search?name=London&count=5&language=en&format=json
```

**Response Shape:**
```typescript
interface GeocodingResponse {
  results?: Array<{
    id: number;
    name: string;             // City name
    latitude: number;
    longitude: number;
    country_code: string;
    country: string;
    admin1: string;           // State/province
    timezone: string;
  }>;
}
```

**Notes:**
- `results` may be absent (not just empty array) when no matches found; defensive check required: `response.results ?? []`
- Debounce query 300 ms before issuing the API call to avoid flooding on fast typing
- Do not cache geocoding results in TanStack Query — they are single-use suggestions; use component-local state

---

### 12.3 Nominatim Reverse Geocoding API

**Base URL:** `https://nominatim.openstreetmap.org/reverse`

**Authentication:** None required. **Usage Policy:** Must include a `User-Agent` header identifying the application; respect rate limits (1 request/second maximum).

**Parameters:**

| Parameter | Value | Note |
|---|---|---|
| `lat` | GPS latitude float | |
| `lon` | GPS longitude float | |
| `format` | `"json"` | |

**Required Headers:**
```
User-Agent: SimpleWeatherApp/1.0 (https://github.com/[username]/simple-weather-app)
```

**Full Constructed URL Example:**
```
https://nominatim.openstreetmap.org/reverse?lat=51.5085&lon=-0.1257&format=json
```

**Response Shape (key fields):**
```typescript
interface NominatimReverseResponse {
  address: {
    city?: string;
    town?: string;
    village?: string;
    county?: string;
    country: string;
    country_code: string;
  };
  display_name: string;     // Full human-readable address
}
```

**City Name Extraction Logic:**
```typescript
function extractCityName(response: NominatimReverseResponse): string {
  return response.address.city
    ?? response.address.town
    ?? response.address.village
    ?? response.address.county
    ?? `${lat}, ${lon}`;  // Final fallback
}
```

**Error Handling:** Network failure or non-2xx response → use coordinate string `"${lat}, ${lon}"` as the Active Location display name. No error message shown to user.

---

## 13. Data Transformation Requirements

All transformations are applied in a dedicated `transformWeatherData()` function in the data layer — never in component render logic. Components receive pre-transformed, display-ready values.

### 13.1 Temperature Rounding

**Rule:** All temperatures are rounded to the nearest integer using `Math.round()`. This is applied to every temperature value before it reaches any component.

```typescript
function roundTemp(value: number | null | undefined): number | null {
  if (value == null || !Number.isFinite(value)) return null;
  return Math.round(value);
}
```

**Celsius → Fahrenheit Conversion (applied after rounding):**
```typescript
function toFahrenheit(celsius: number): number {
  return Math.round((celsius * 9) / 5 + 32);
}
```

**Convention:** Convert, then display. Never display the raw API float. The display contract is: an integer string followed by the degree symbol and unit letter, e.g., `"18°C"` or `"64°F"`.

---

### 13.2 Timezone Handling

**Non-negotiable rules:**
1. `timezone=auto` is included on every Open-Meteo Forecast API call
2. The IANA timezone string from `response.timezone` is stored alongside weather data and passed to all date/time formatting functions
3. `Intl.DateTimeFormat` with the location's `timeZone` option is used for all time display
4. The browser's `Intl.DateTimeFormat()` default (browser-local timezone) is **never** used for location weather times

**Current Hour Index Derivation:**
```typescript
function getCurrentHourIndex(
  hourlyTimes: string[],
  locationTimezone: string
): number {
  const now = new Date();
  const localHour = new Intl.DateTimeFormat("en-US", {
    timeZone: locationTimezone,
    hour: "numeric",
    hour12: false,
  }).format(now);
  // Find the index in hourlyTimes where the hour matches
  return hourlyTimes.findIndex((t) => {
    const slotHour = new Intl.DateTimeFormat("en-US", {
      timeZone: locationTimezone,
      hour: "numeric",
      hour12: false,
    }).format(new Date(t));
    return slotHour === localHour;
  });
}
```

**Hourly Label Formatting:**
```typescript
function formatHourLabel(isoTime: string, timezone: string): string {
  return new Intl.DateTimeFormat("en-US", {
    timeZone: timezone,
    hour: "numeric",
    hour12: true,
  }).format(new Date(isoTime));
  // Output examples: "2 PM", "12 AM"
}
```

**Day Label Formatting:**
```typescript
function formatDayLabel(dateString: string, timezone: string, index: number): string {
  if (index === 0) return "Today";
  return new Intl.DateTimeFormat("en-US", {
    timeZone: timezone,
    weekday: "short",
  }).format(new Date(dateString));
  // Output examples: "Today", "Tue", "Wed", "Thu"
}
```

---

### 13.3 WMO Code Mapping

The `getCondition()` function is the single source of truth for WMO code to label/icon mappings. All components call this function; no component contains its own inline WMO switch.

```typescript
interface WeatherCondition {
  label: string;
  iconId: string;        // e.g. "clear-day", "rain", "thunderstorm"
  hasDayNightVariant: boolean;
}

function getCondition(wmoCode: number, isDay: boolean): WeatherCondition {
  const conditions: Record<number, WeatherCondition> = {
    0:  { label: "Clear Sky",               iconId: isDay ? "clear-day" : "clear-night",                 hasDayNightVariant: true },
    1:  { label: "Mostly Clear",            iconId: isDay ? "mostly-clear-day" : "mostly-clear-night",   hasDayNightVariant: true },
    2:  { label: "Partly Cloudy",           iconId: isDay ? "partly-cloudy-day" : "partly-cloudy-night", hasDayNightVariant: true },
    3:  { label: "Overcast",                iconId: "overcast",                                           hasDayNightVariant: false },
    45: { label: "Foggy",                   iconId: "fog",                                                hasDayNightVariant: false },
    48: { label: "Icy Fog",                 iconId: "fog-ice",                                            hasDayNightVariant: false },
    51: { label: "Light Drizzle",           iconId: "drizzle-light",                                      hasDayNightVariant: false },
    53: { label: "Drizzle",                 iconId: "drizzle",                                            hasDayNightVariant: false },
    55: { label: "Heavy Drizzle",           iconId: "drizzle-heavy",                                      hasDayNightVariant: false },
    56: { label: "Freezing Drizzle",        iconId: "freezing-drizzle",                                   hasDayNightVariant: false },
    57: { label: "Heavy Freezing Drizzle",  iconId: "freezing-drizzle-heavy",                             hasDayNightVariant: false },
    61: { label: "Light Rain",              iconId: "rain-light",                                         hasDayNightVariant: false },
    63: { label: "Rain",                    iconId: "rain",                                               hasDayNightVariant: false },
    65: { label: "Heavy Rain",              iconId: "rain-heavy",                                         hasDayNightVariant: false },
    66: { label: "Freezing Rain",           iconId: "freezing-rain",                                      hasDayNightVariant: false },
    67: { label: "Heavy Freezing Rain",     iconId: "freezing-rain-heavy",                                hasDayNightVariant: false },
    71: { label: "Light Snow",              iconId: "snow-light",                                         hasDayNightVariant: false },
    73: { label: "Snow",                    iconId: "snow",                                               hasDayNightVariant: false },
    75: { label: "Heavy Snow",              iconId: "snow-heavy",                                         hasDayNightVariant: false },
    77: { label: "Snow Grains",             iconId: "snow-grains",                                        hasDayNightVariant: false },
    80: { label: "Light Showers",           iconId: "showers-light",                                      hasDayNightVariant: false },
    81: { label: "Showers",                 iconId: "showers",                                            hasDayNightVariant: false },
    82: { label: "Heavy Showers",           iconId: "showers-heavy",                                      hasDayNightVariant: false },
    85: { label: "Snow Showers",            iconId: "snow-showers",                                       hasDayNightVariant: false },
    86: { label: "Heavy Snow Showers",      iconId: "snow-showers-heavy",                                 hasDayNightVariant: false },
    95: { label: "Thunderstorm",            iconId: "thunderstorm",                                       hasDayNightVariant: false },
    96: { label: "Thunderstorm with Hail",  iconId: "thunderstorm-hail",                                  hasDayNightVariant: false },
    99: { label: "Severe Thunderstorm",     iconId: "thunderstorm-hail-heavy",                            hasDayNightVariant: false },
  };
  return conditions[wmoCode] ?? {
    label: "Unknown Conditions",
    iconId: "unknown",
    hasDayNightVariant: false,
  };
}
```

---

## 14. Component Architecture

The following lists the primary React components, their responsibilities, and USWDS pattern mappings.

### Component Tree (top-level):

```
App
├── SkipNav                     (WCAG skip link; usa-skipnav)
├── Header
│   └── LocationSearch          (F0; usa-search + combo-box)
│       ├── SearchInput
│       ├── GpsButton           (usa-button--outline)
│       ├── SuggestionList      (role="listbox")
│       └── RecentLocations     (usa-tag chips)
├── Main (id="main-content")
│   ├── CurrentConditions       (F1; aria-live region)
│   │   ├── WeatherHero         (temperature, condition, icon, gradient)
│   │   ├── UnitToggle          (usa-button-group; F1)
│   │   ├── HighLowBar          (high/low temperatures)
│   │   └── SupportingData      (humidity, wind, precipitation probability)
│   ├── HourlyForecast          (F2; role="region" aria-label="Hourly forecast")
│   │   └── HourlyCard × 24     (usa-card)
│   ├── DailyForecast           (F3; role="region" aria-label="7-day forecast")
│   │   ├── DailyRow × 7        (USWDS list pattern)
│   │   ├── TemperatureTrendChart (Recharts AreaChart)
│   │   └── ChartDataTable      (sr-only accessible fallback)
│   └── DetailsAccordion        (F6; usa-accordion)
│       └── DetailFields        (UV, wind dir, visibility, humidity, sunrise, sunset)
├── FreshnessIndicator          (F7; usa-tag or inline; aria-live)
├── AlertBanner                 (F7; usa-alert variants; aria-live)
└── Footer                      (F9; usa-footer--slim)
```

### Key Component Specifications:

**`LocationSearch`**
- USWDS pattern: `usa-search` with combo-box extension
- Props: `onLocationSelect: (location: ActiveLocation) => void`
- State: `query` (string), `suggestions` (array), `isLoadingSuggestions` (boolean)
- Debounce: 300 ms on query change before API call

**`CurrentConditions`**
- Reads from TanStack Query cache: `useWeatherQuery(latitude, longitude)`
- Renders skeleton (USWDS placeholder) when `isLoading === true`
- Renders `usa-alert--error` when `isError === true`
- `aria-live="polite"` wrapper announces data changes

**`WeatherHero`**
- Receives transformed weather data as props (no direct API calls)
- Background gradient class derived from `conditionGroup` + `isDay` via `getHeroGradientClass()` utility
- Temperature rendered as `{Math.round(temp)}°{unit}` — no decimals ever

**`HourlyCard`**
- `role="listitem"` within a `role="list"` container
- No click handler required (display only)
- Min 44px height via USWDS card defaults

**`DetailsAccordion`**
- Wraps USWDS `usa-accordion` markup exactly
- State: `isExpanded` (boolean, default `false`); NOT persisted to `localStorage`
- On toggle: updates `aria-expanded` attribute; toggles `hidden` on content region

**`AlertBanner`**
- Renders conditionally based on app state
- Always in DOM as `aria-live="polite"` region (empty when no alert)
- `variant` prop: `"error"` | `"warning"` | `"info"` maps to `usa-alert--error` | `usa-alert--warning` | `usa-alert--info`

---

## 15. Data Storage Schema

All persistence is client-side only (`localStorage`). No server-side storage exists.

### `localStorage` Keys:

| Key | Type | Default | Contents | Expiry |
|---|---|---|---|---|
| `weather_unit` | string | `"celsius"` | `"celsius"` or `"fahrenheit"` | Never (user preference) |
| `recent_locations` | JSON string | `"[]"` | Array of up to 5 `RecentLocation` objects | Never; capped at 5 entries |

### `RecentLocation` Object Schema:
```typescript
interface RecentLocation {
  name: string;       // Display name (city, admin1, country)
  latitude: number;
  longitude: number;
  timestamp: number;  // Unix ms; used for recency sorting
}
```

### `recent_locations` Management Rules:
1. On new location selection, prepend to array
2. Deduplicate: if an entry with the same `latitude` and `longitude` exists, remove the old entry before prepending the new one
3. Cap array at 5 entries: if length would exceed 5 after prepend, remove the last element
4. Serialize with `JSON.stringify`; parse with `JSON.parse`; wrap in try/catch (malformed JSON → reset to `[]`)

### TanStack Query Cache (in-memory, session-scoped):

| Cache Key | TTL (gcTime) | Populated by |
|---|---|---|
| `["weather", lat, lon]` | 30 minutes | Open-Meteo Forecast API call |

---

## 16. Global Error Handling

### Error Hierarchy:

| Level | Trigger | USWDS Component | Recovery |
|---|---|---|---|
| Critical (no data, no connection) | Network offline + no cache | `usa-alert--error` full-width banner | Automatic retry when `"online"` event fires |
| Degraded (stale cached data) | Network offline + cache available | `usa-alert--warning` banner | Auto-refresh on reconnect |
| Feature-level (section failure) | Individual API field missing | Inline `usa-alert--warning` per section | Section shows `"—"` or "Unavailable" |
| Input (no results) | Geocoding 0 results | `usa-alert--info` below search input | User edits query |
| Silent (GPS failure) | Geolocation denied/timeout | None for denial; `usa-alert--warning` brief toast for timeout | Search input remains primary |

### Never-Blank-Screen Contract:
The app must always render one of the following states:
1. **Empty state** (no location selected): Search prompt with `usa-search` input as the CTA
2. **Loading state**: USWDS skeleton placeholders in all data regions
3. **Success state**: All weather data rendered
4. **Error state**: USWDS `usa-alert--error` with retry action; never just a white screen

### TypeScript Error Handling Contract:
- All API response types are typed with TypeScript interfaces (see §12)
- All optional fields use `?` and are defensively accessed with nullish coalescing (`??`)
- No `any` casts permitted; TypeScript strict mode is enforced at build time (`"strict": true` in `tsconfig.json`)
- All `JSON.parse` calls are wrapped in try/catch with typed fallbacks

---

*FRD version 1.0 — generated 2026-05-01*
*Derived from PRD-SimpleWeatherApp.md v1.1*
*Sources: PRD-SimpleWeatherApp.md, .planning/PROJECT.md, project_specs/ref_docs/PRD.md*
*USWDS integration: All UI components, design tokens, and patterns conform to USWDS standards*
*Accessibility: WCAG 2.2 Level AA across all features and states*
*Next documents: TechArch (architecture decisions), UserStories (sprint-ready stories)*
