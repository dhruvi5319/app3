# Technical Architecture Document
## Simple Weather App

| Field | Value |
|---|---|
| **Product Name** | Simple Weather App |
| **Version** | 1.0 |
| **Date** | 2026-05-01 |
| **Status** | Active |
| **Related PRD** | PRD-SimpleWeatherApp.md v1.1 |
| **Related FRD** | FRD-SimpleWeatherApp.md v1.0 |

---

## Table of Contents

1. [Architectural Overview](#1-architectural-overview)
2. [Technology Stack](#2-technology-stack)
3. [USWDS Integration Architecture](#3-uswds-integration-architecture)
4. [Component Architecture](#4-component-architecture)
5. [Data Model & State Management](#5-data-model--state-management)
6. [API Design & Integration](#6-api-design--integration)
7. [TypeScript Interfaces](#7-typescript-interfaces)
8. [Data Transformation Layer](#8-data-transformation-layer)
9. [Security Architecture](#9-security-architecture)
10. [Performance Architecture](#10-performance-architecture)
11. [Deployment Architecture](#11-deployment-architecture)
12. [Key Architecture Decisions](#12-key-architecture-decisions)

---

## 1. Architectural Overview

### Architecture Pattern

The Simple Weather App is a **Client-Side Single-Page Application (SPA)** with no backend. All computation, state management, and API communication occurs in the user's browser. There is no server, no database, and no authentication system. Deployment is a static file bundle served over HTTPS from a CDN (Vercel Edge Network).

```
┌─────────────────────────────────────────────────────────────────┐
│                        User's Browser                           │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                  React 19 SPA (Vite 8)                  │   │
│  │                                                         │   │
│  │  ┌───────────────────┐   ┌───────────────────────────┐  │   │
│  │  │  USWDS Components │   │   TanStack Query v5 Cache │  │   │
│  │  │  (usa-search,     │   │   staleTime: 10 min       │  │   │
│  │  │  usa-accordion,   │   │   isLoading / isError     │  │   │
│  │  │  usa-alert, etc.) │   └───────────────────────────┘  │   │
│  │  └───────────────────┘                                   │   │
│  │                                                         │   │
│  │  ┌───────────────────┐   ┌───────────────────────────┐  │   │
│  │  │  Recharts         │   │   localStorage             │  │   │
│  │  │  (AreaChart,      │   │   (unit pref, recent locs)│  │   │
│  │  │   BarChart)       │   └───────────────────────────┘  │   │
│  │  └───────────────────┘                                   │   │
│  └─────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
         │                    │                    │
         ▼                    ▼                    ▼
┌──────────────┐   ┌──────────────────┐   ┌──────────────────┐
│ Open-Meteo   │   │  Open-Meteo      │   │  Nominatim       │
│ Forecast API │   │  Geocoding API   │   │  Reverse Geo API │
│ (no key req.)│   │  (no key req.)   │   │  (no key req.)   │
└──────────────┘   └──────────────────┘   └──────────────────┘
```

### Deployment Topology

```
GitHub (main branch push)
        │
        ▼ (auto-deploy via Vercel GitHub integration)
Vercel Edge Network (HTTPS)
        │
        ▼ (serves static files)
dist/ (Vite build output)
  ├── index.html
  ├── assets/
  │   ├── index-[hash].js       (main bundle, < 300 KB gzipped)
  │   ├── index-[hash].css      (Tailwind purged + USWDS tokens)
  │   └── icons/                (WMO weather icon SVGs)
  └── _headers                  (Vercel security headers)
```

---

## 2. Technology Stack

| Layer | Technology | Version | Purpose |
|---|---|---|---|
| Framework | React | 19 | Component model, JSX, Suspense |
| Language | TypeScript | 5.x (strict) | Type safety, zero `any` casts |
| Build Tool | Vite | 8 | HMR, `react-ts` template, static dist output |
| Styling | Tailwind CSS | v4 | Utility-first layout; configured with USWDS tokens |
| Design System | USWDS | 3.x | Design tokens, accessible components, keyboard patterns |
| Data Fetching | TanStack Query | v5 | SWR-style cache, loading/error states, staleTime |
| Charting | Recharts | 2.x | AreaChart (temperature trend), BarChart (precipitation) |
| Weather API | Open-Meteo Forecast | v1 | Current conditions, hourly, 7-day forecast |
| Geocoding | Open-Meteo Geocoding | v1 | City name → lat/lon with autocomplete |
| Reverse Geocoding | Nominatim (OSM) | v1 | GPS lat/lon → human-readable city name |
| Deployment | Vercel | — | Zero-config HTTPS static hosting from GitHub |
| Testing | Vitest + Testing Library | — | Unit and component tests |
| Accessibility Audit | axe-core | — | Automated WCAG AA verification in CI |

### Key Dependency Rationale

- **Open-Meteo** — Only free weather API with no key required, 14-day forecasts, and native `timezone=auto` support. Eliminates the #1 portfolio app failure mode.
- **TanStack Query v5** — Provides `staleTime: 10 minutes` to prevent redundant calls, plus built-in `isLoading`/`isError`/`isOffline` state for never-blank-screen guarantee.
- **USWDS 3.x** — Ships proven WCAG 2.2 AA-compliant components with correct ARIA patterns, keyboard behavior, and focus management. Reduces custom accessibility implementation risk.
- **Tailwind CSS v4** — Configured to use USWDS design tokens as CSS custom properties; utility classes map directly to token values.

---

## 3. USWDS Integration Architecture

### Token Mapping Strategy

USWDS design tokens are imported as CSS custom properties and aliased into Tailwind CSS v4's theme configuration. This allows Tailwind utility classes to produce USWDS-compliant values without requiring USWDS's Sass build pipeline.

```
// tailwind.config.ts (v4 syntax)
export default {
  theme: {
    extend: {
      colors: {
        // USWDS system palette mapped to Tailwind custom colors
        'blue-60': 'var(--uswds-color-blue-60)',
        'blue-70': 'var(--uswds-color-blue-70)',
        'gray-5':  'var(--uswds-color-gray-5)',
        'gray-90': 'var(--uswds-color-gray-90)',
        'red-50':  'var(--uswds-color-red-50)',
        'gold-20': 'var(--uswds-color-gold-20)',
      },
      spacing: {
        // USWDS spacing units (1 unit = 8px)
        'u-1': 'var(--uswds-spacing-1)',    // 8px
        'u-2': 'var(--uswds-spacing-2)',    // 16px
        'u-3': 'var(--uswds-spacing-3)',    // 24px
        'u-4': 'var(--uswds-spacing-4)',    // 32px
        'u-6': 'var(--uswds-spacing-6)',    // 48px
      },
      fontFamily: {
        'public-sans': 'var(--uswds-font-sans)',  // Public Sans
        'merriweather': 'var(--uswds-font-serif)', // Merriweather
      },
      fontSize: {
        'uswds-xs':  'var(--uswds-font-size-xs)',  // 12px
        'uswds-sm':  'var(--uswds-font-size-sm)',  // 14px
        'uswds-md':  'var(--uswds-font-size-md)',  // 16px
        'uswds-lg':  'var(--uswds-font-size-lg)',  // 20px
        'uswds-xl':  'var(--uswds-font-size-xl)',  // 24px
        'uswds-2xl': 'var(--uswds-font-size-2xl)', // 32px
        'uswds-3xl': 'var(--uswds-font-size-3xl)', // 40px
      },
    },
  },
}
```

### USWDS Component Usage Map

| Feature | USWDS Component(s) | Implementation Notes |
|---|---|---|
| F0 — Search input | `usa-search`, combo-box / listbox pattern | `role="combobox"` on input, `role="listbox"` on dropdown, arrow key + Enter navigation |
| F0 — GPS button | `usa-button--outline` | Icon-only button with `aria-label="Use my location"` |
| F0 — Recent location chips | `usa-tag` | Interactive chips with `role="button"` and keyboard activation |
| F1 — Unit toggle | `usa-button-group` with toggle state | ARIA `aria-pressed` on active unit button |
| F1 — Error/offline alerts | `usa-alert` (error, warning, info variants) | `aria-live="polite"` region wrapping; never disappears without user action |
| F1 — Loading skeleton | USWDS loading indicator pattern | `aria-busy="true"` on container during fetch |
| F6 — Details panel | `usa-accordion` | Single-item accordion; `aria-expanded` on trigger; `aria-controls` links trigger to panel |
| F7 — Freshness notice | `usa-alert--info` | Persistent in-page notice; not dismissible |
| F8 — Focus indicators | USWDS focus utility (2px outline + 2px offset) | Applied globally via `[data-focus-keyboard]` or `:focus-visible` |
| F9 — Footer attribution | `usa-footer` (minimal variant) | Open-Meteo CC BY 4.0 link in footer identifier section |

### USWDS Installation

```bash
npm install @uswds/uswds
```

CSS custom properties (design tokens) are imported in `src/index.css`:

```css
/* Import USWDS tokens as CSS custom properties */
@import "@uswds/uswds/css/uswds.min.css";

/* Override/extend with app-specific variables */
:root {
  --app-hero-gradient-clear-day: linear-gradient(180deg, var(--uswds-color-blue-40), var(--uswds-color-blue-60));
  --app-hero-gradient-cloudy: linear-gradient(180deg, var(--uswds-color-gray-30), var(--uswds-color-gray-50));
  --app-hero-gradient-rain: linear-gradient(180deg, var(--uswds-color-gray-50), var(--uswds-color-blue-80));
  --app-hero-gradient-night: linear-gradient(180deg, var(--uswds-color-blue-80), var(--uswds-color-gray-90));
}
```

### USWDS Breakpoints (used for responsive layout F5)

| USWDS Token | Pixel Value | Usage |
|---|---|---|
| `mobile` | 320px | Minimum supported width |
| `mobile-lg` | 480px | Compact single-column |
| `tablet` | 640px | Two-column layout begins |
| `tablet-lg` | 880px | Wider forecast panels |
| `desktop` | 1024px | Full horizontal layout |
| `desktop-lg` | 1200px | Max content width clamp |

---

## 4. Component Architecture

### Component Tree

```
App
├── QueryClientProvider (TanStack Query)
├── AppShell
│   ├── Header
│   │   └── SearchBar (usa-search + combo-box)
│   │       ├── SearchInput
│   │       ├── GpsButton (usa-button--outline)
│   │       ├── SuggestionList (role="listbox")
│   │       │   └── SuggestionItem × N (role="option")
│   │       └── RecentLocations
│   │           └── LocationChip × N (usa-tag)
│   ├── MainContent
│   │   ├── CurrentConditions (hero section)
│   │   │   ├── WeatherHero
│   │   │   │   ├── ConditionBackground (gradient div)
│   │   │   │   ├── WeatherIcon (SVG + text label)
│   │   │   │   ├── TemperatureDisplay (large int °C/°F)
│   │   │   │   ├── FeelsLike
│   │   │   │   ├── HighLow
│   │   │   │   ├── PrecipitationProbability
│   │   │   │   ├── HumidityWind
│   │   │   │   └── UnitToggle (usa-button-group)
│   │   │   ├── FreshnessIndicator
│   │   │   └── SkeletonHero (loading state)
│   │   ├── HourlyForecast (role="region" aria-label="Hourly forecast")
│   │   │   ├── HourlyScrollContainer
│   │   │   │   └── HourlyCard × 24
│   │   │   │       ├── HourLabel
│   │   │   │       ├── WeatherIcon (day/night variant)
│   │   │   │       ├── Temperature
│   │   │   │       └── PrecipitationProbability
│   │   │   └── SkeletonHourly (loading state)
│   │   ├── DailyForecast (role="region" aria-label="7-day forecast")
│   │   │   ├── DailyRow × 7
│   │   │   │   ├── DayLabel
│   │   │   │   ├── WeatherIcon (daytime variant)
│   │   │   │   ├── HighLow
│   │   │   │   └── PrecipitationProbability
│   │   │   ├── TemperatureTrendChart (Recharts AreaChart)
│   │   │   │   └── AccessibleDataTable (sr-only fallback)
│   │   │   └── SkeletonDaily (loading state)
│   │   └── DetailsPanel (usa-accordion)
│   │       ├── AccordionTrigger (aria-expanded, aria-controls)
│   │       └── AccordionContent
│   │           ├── UVIndex
│   │           ├── WindDetail (speed + cardinal direction)
│   │           ├── Visibility
│   │           ├── Humidity
│   │           ├── Sunrise
│   │           └── Sunset
│   └── Footer (usa-footer)
│       └── AttributionLink (Open-Meteo CC BY 4.0)
└── AlertOverlay (usa-alert, for global error states)
```

### Component Responsibilities

| Component | State Owned | Key Props |
|---|---|---|
| `App` | QueryClient, activeLocation, unit | — |
| `SearchBar` | inputValue, suggestions, isOpen | onLocationSelect |
| `CurrentConditions` | — | weatherData, unit, isLoading, isError |
| `HourlyForecast` | scrollPosition | hourlyData[], unit, isLoading |
| `DailyForecast` | — | dailyData[], unit, isLoading |
| `DetailsPanel` | isExpanded | currentData, unit, timezone |
| `UnitToggle` | — | unit, onToggle |
| `FreshnessIndicator` | — | dataTimestamp, isStale |

---

## 5. Data Model & State Management

### No Database — All State is Local

This app has no database schema. All persisted state uses `localStorage`. All in-memory state uses TanStack Query cache and React component state.

### localStorage Schema

| Key | Type | Description | Max Size |
|---|---|---|---|
| `swa:unit` | `"C" \| "F"` | User's preferred temperature unit | 3 bytes |
| `swa:recent` | `JSON (RecentLocation[])` | Up to 5 most recently searched locations | ~2 KB |

```typescript
interface RecentLocation {
  id: string;              // Open-Meteo location result ID
  name: string;            // Display name (city, region, country)
  latitude: number;
  longitude: number;
  addedAt: number;         // Unix timestamp (ms)
}
```

### TanStack Query Cache Keys

| Query Key | Stale Time | Purpose |
|---|---|---|
| `['weather', lat, lon]` | 10 minutes | Open-Meteo forecast (current + hourly + daily) |
| `['geocoding', searchTerm]` | 5 minutes | City name → lat/lon suggestions |
| `['reverse-geocoding', lat, lon]` | 30 minutes | GPS → city name display string |

### TanStack Query Configuration

```typescript
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 10 * 60 * 1000,      // 10 minutes
      gcTime: 30 * 60 * 1000,          // 30 minutes in cache after unmount
      retry: 2,
      refetchOnWindowFocus: false,      // Don't refetch on tab switch
      networkMode: 'offlineFirst',      // Serve cache when offline
    },
  },
});
```

---

## 6. API Design & Integration

### External APIs (all key-free)

#### Open-Meteo Forecast API

```
GET https://api.open-meteo.com/v1/forecast
  ?latitude={lat}
  &longitude={lon}
  &current=temperature_2m,relative_humidity_2m,apparent_temperature,
            is_day,precipitation_probability,weathercode,
            windspeed_10m,winddirection_10m
  &hourly=temperature_2m,precipitation_probability,weathercode,is_day
  &daily=weathercode,temperature_2m_max,temperature_2m_min,
         precipitation_probability_max,sunrise,sunset,
         uv_index_max,windspeed_10m_max,winddirection_10m_dominant,visibility_mean
  &forecast_days=8
  &timezone=auto
  &wind_speed_unit=mph
```

**CRITICAL:** `timezone=auto` is **mandatory** on every request. Without it, all time-relative displays (hourly labels, sunrise/sunset, day/night icon selection) break for locations outside the user's browser timezone. This is non-negotiable.

#### Open-Meteo Geocoding API

```
GET https://geocoding-api.open-meteo.com/v1/search
  ?name={searchTerm}
  &count=5
  &language=en
  &format=json
```

#### Nominatim Reverse Geocoding API

```
GET https://nominatim.openstreetmap.org/reverse
  ?lat={lat}
  &lon={lon}
  &format=json
  &zoom=10
  &accept-language=en
```

**Note:** Nominatim requires a `User-Agent` header identifying the application per their usage policy.

### API Error Handling Matrix

| Error Condition | User-Facing Behavior | USWDS Component |
|---|---|---|
| Forecast API 4xx/5xx | `usa-alert--error`: "Weather data unavailable. Try again shortly." | `usa-alert` |
| Forecast API timeout | `usa-alert--warning`: "Loading is taking longer than expected..." | `usa-alert` |
| Geocoding returns 0 results | Inline message below search: "City not found — try a different spelling" | `usa-alert--warning` below `usa-search` |
| Geolocation denied | Search input remains fully functional; no error state shown | — |
| Offline (no cache) | `usa-alert--error`: "Unable to load weather — check your connection" | `usa-alert` |
| Offline (cache available) | `usa-alert--info`: "Showing cached data from X minutes ago" | `usa-alert` |

---

## 7. TypeScript Interfaces

### Open-Meteo Forecast Response

```typescript
interface OpenMeteoForecastResponse {
  latitude: number;
  longitude: number;
  timezone: string;              // e.g., "America/New_York"
  timezone_abbreviation: string;
  utc_offset_seconds: number;
  current: {
    time: string;                // ISO 8601
    temperature_2m: number;      // °C (raw, round before display)
    relative_humidity_2m: number;
    apparent_temperature: number;
    is_day: 0 | 1;
    precipitation_probability: number;
    weathercode: number;         // WMO code 0–99
    windspeed_10m: number;
    winddirection_10m: number;
  };
  hourly: {
    time: string[];              // ISO 8601 array
    temperature_2m: number[];
    precipitation_probability: number[];
    weathercode: number[];
    is_day: (0 | 1)[];
  };
  daily: {
    time: string[];              // date strings, YYYY-MM-DD
    weathercode: number[];
    temperature_2m_max: number[];
    temperature_2m_min: number[];
    precipitation_probability_max: number[];
    sunrise: string[];           // ISO 8601 in location's timezone
    sunset: string[];
    uv_index_max: number[];
    windspeed_10m_max: number[];
    winddirection_10m_dominant: number[];
    visibility_mean: number[];
  };
}
```

### Open-Meteo Geocoding Response

```typescript
interface GeocodingResult {
  id: number;
  name: string;
  latitude: number;
  longitude: number;
  elevation: number;
  feature_code: string;
  country_code: string;
  timezone: string;
  population?: number;
  country_id: number;
  country: string;
  admin1?: string;
  admin2?: string;
  admin3?: string;
}

interface GeocodingResponse {
  results?: GeocodingResult[];
  generationtime_ms: number;
}
```

### Nominatim Reverse Geocoding Response

```typescript
interface NominatimReverseResponse {
  place_id: number;
  licence: string;
  display_name: string;
  address: {
    city?: string;
    town?: string;
    village?: string;
    county?: string;
    state?: string;
    country: string;
    country_code: string;
  };
}
```

### Application Domain Types

```typescript
// Processed location for use throughout the app
interface ActiveLocation {
  id: string;
  name: string;          // Human-readable display name
  latitude: number;
  longitude: number;
}

// Processed weather data after transformation
interface WeatherData {
  location: ActiveLocation;
  timezone: string;
  fetchedAt: number;     // Unix ms timestamp

  current: {
    temperatureC: number;   // Rounded integer
    feelsLikeC: number;
    humidity: number;
    windSpeedMph: number;
    windDirectionDeg: number;
    precipProbability: number;
    wmoCode: number;
    isDay: boolean;
    todayHighC: number;
    todayLowC: number;
    sunrise: string;        // HH:mm local time
    sunset: string;
  };

  hourly: HourlySlot[];   // 24 entries from current hour
  daily: DailySlot[];     // 7 entries

  details: {
    uvIndexMax: number;
    windDirectionCardinal: string;  // e.g., "NNE"
    visibilityKm: number;
    sunrise: string;
    sunset: string;
  };
}

interface HourlySlot {
  time: string;           // HH:mm local time
  temperatureC: number;
  precipProbability: number;
  wmoCode: number;
  isDay: boolean;
}

interface DailySlot {
  dayLabel: string;       // "Mon", "Tue", ... "Today" for index 0
  highC: number;
  lowC: number;
  precipProbabilityMax: number;
  wmoCode: number;        // Daytime representative code
}

// Unit preference
type TemperatureUnit = 'C' | 'F';

// WMO code condition mapping
interface WeatherCondition {
  iconId: string;         // e.g., "clear-day", "rain", "snow"
  label: string;          // e.g., "Clear Sky", "Heavy Rain"
  hasNightVariant: boolean;
}
```

---

## 8. Data Transformation Layer

All API response data is transformed before entering the React component tree. Raw API responses are never passed directly to components.

### Temperature Rounding

```typescript
export function roundTemp(raw: number): number {
  return Math.round(raw);
}

export function toFahrenheit(celsius: number): number {
  return Math.round(celsius * 9 / 5 + 32);
}

export function displayTemp(celsius: number, unit: TemperatureUnit): string {
  const value = unit === 'F' ? toFahrenheit(celsius) : roundTemp(celsius);
  return `${value}°${unit}`;
}
```

### Timezone-Aware Time Formatting

**CRITICAL:** All time display uses the API-returned `timezone` string, never `new Date()` in the user's local timezone.

```typescript
export function formatHourLabel(isoTime: string, timezone: string): string {
  return new Intl.DateTimeFormat('en-US', {
    hour: 'numeric',
    hour12: true,
    timeZone: timezone,
  }).format(new Date(isoTime));
  // e.g., "3 PM"
}

export function formatDayLabel(dateStr: string, index: number): string {
  if (index === 0) return 'Today';
  const date = new Date(dateStr + 'T12:00:00Z'); // noon UTC avoids DST boundary issues
  return new Intl.DateTimeFormat('en-US', {
    weekday: 'short',
  }).format(date);
  // e.g., "Mon", "Tue"
}

export function formatSunTime(isoTime: string, timezone: string): string {
  return new Intl.DateTimeFormat('en-US', {
    hour: 'numeric',
    minute: '2-digit',
    hour12: true,
    timeZone: timezone,
  }).format(new Date(isoTime));
  // e.g., "6:42 AM"
}
```

### WMO Code to Condition Mapping

```typescript
const WMO_CONDITIONS: Record<number, WeatherCondition> = {
  0:  { iconId: 'clear',           label: 'Clear Sky',            hasNightVariant: true },
  1:  { iconId: 'mostly-clear',    label: 'Mainly Clear',         hasNightVariant: true },
  2:  { iconId: 'partly-cloudy',   label: 'Partly Cloudy',        hasNightVariant: true },
  3:  { iconId: 'overcast',        label: 'Overcast',             hasNightVariant: false },
  45: { iconId: 'fog',             label: 'Fog',                  hasNightVariant: false },
  48: { iconId: 'fog',             label: 'Icy Fog',              hasNightVariant: false },
  51: { iconId: 'drizzle-light',   label: 'Light Drizzle',        hasNightVariant: false },
  53: { iconId: 'drizzle',         label: 'Moderate Drizzle',     hasNightVariant: false },
  55: { iconId: 'drizzle-heavy',   label: 'Dense Drizzle',        hasNightVariant: false },
  56: { iconId: 'freezing-drizzle',label: 'Freezing Drizzle',     hasNightVariant: false },
  57: { iconId: 'freezing-drizzle',label: 'Heavy Freezing Drizzle',hasNightVariant: false },
  61: { iconId: 'rain-light',      label: 'Light Rain',           hasNightVariant: false },
  63: { iconId: 'rain',            label: 'Moderate Rain',        hasNightVariant: false },
  65: { iconId: 'rain-heavy',      label: 'Heavy Rain',           hasNightVariant: false },
  66: { iconId: 'freezing-rain',   label: 'Light Freezing Rain',  hasNightVariant: false },
  67: { iconId: 'freezing-rain',   label: 'Heavy Freezing Rain',  hasNightVariant: false },
  71: { iconId: 'snow-light',      label: 'Light Snow',           hasNightVariant: false },
  73: { iconId: 'snow',            label: 'Moderate Snow',        hasNightVariant: false },
  75: { iconId: 'snow-heavy',      label: 'Heavy Snow',           hasNightVariant: false },
  77: { iconId: 'snow-grains',     label: 'Snow Grains',          hasNightVariant: false },
  80: { iconId: 'showers-light',   label: 'Light Showers',        hasNightVariant: false },
  81: { iconId: 'showers',         label: 'Moderate Showers',     hasNightVariant: false },
  82: { iconId: 'showers-heavy',   label: 'Violent Showers',      hasNightVariant: false },
  85: { iconId: 'snow-showers',    label: 'Snow Showers',         hasNightVariant: false },
  86: { iconId: 'snow-showers',    label: 'Heavy Snow Showers',   hasNightVariant: false },
  95: { iconId: 'thunderstorm',    label: 'Thunderstorm',         hasNightVariant: false },
  96: { iconId: 'thunderstorm-hail','label':'Thunderstorm w/ Hail',hasNightVariant: false },
  99: { iconId: 'thunderstorm-hail','label':'Thunderstorm, Heavy Hail',hasNightVariant: false },
};

export function getCondition(wmoCode: number, isDay: boolean): WeatherCondition & { resolvedIconId: string } {
  const condition = WMO_CONDITIONS[wmoCode] ?? WMO_CONDITIONS[0]; // fallback to clear
  const resolvedIconId = condition.hasNightVariant && !isDay
    ? `${condition.iconId}-night`
    : condition.iconId;
  return { ...condition, resolvedIconId };
}
```

### Wind Direction Conversion

```typescript
const CARDINAL_DIRECTIONS = ['N','NNE','NE','ENE','E','ESE','SE','SSE',
                              'S','SSW','SW','WSW','W','WNW','NW','NNW'];

export function degreesToCardinal(degrees: number): string {
  const index = Math.round(degrees / 22.5) % 16;
  return CARDINAL_DIRECTIONS[index];
}
```

---

## 9. Security Architecture

This app has no authentication system, no user accounts, and no server-side components. The security posture is minimal and deliberate.

### Security Considerations

| Concern | Status | Mitigation |
|---|---|---|
| API key exposure | Not applicable | Open-Meteo and Nominatim require no API keys |
| XSS via API data | Low risk | All data rendered via React (auto-escapes by default); no `dangerouslySetInnerHTML` |
| User data storage | Minimal | Only temperature unit preference and recent locations in localStorage; no PII |
| HTTPS | Required | Vercel enforces HTTPS; required for Geolocation API to function |
| Content Security Policy | Recommended | Set via `_headers` file in dist/ to restrict external scripts |
| Geolocation permission | Opt-in only | Never requested without explicit user action (GPS button tap); denial never breaks the app |
| Nominatim rate limiting | Low risk | Reverse geocoding only called on GPS button use; cached for 30 minutes |
| Open-Meteo rate limiting | Low risk | `staleTime: 10 minutes` prevents redundant calls; free tier is generous |

### Recommended `_headers` (Vercel)

```
/*
  Content-Security-Policy: default-src 'self'; connect-src https://api.open-meteo.com https://geocoding-api.open-meteo.com https://nominatim.openstreetmap.org; img-src 'self' data:; style-src 'self' 'unsafe-inline'; font-src 'self'
  X-Frame-Options: DENY
  X-Content-Type-Options: nosniff
  Referrer-Policy: strict-origin-when-cross-origin
```

---

## 10. Performance Architecture

### Bundle Budget

| Asset | Target (gzipped) | Strategy |
|---|---|---|
| Main JS bundle | < 300 KB | Vite tree-shaking; Recharts is largest dependency |
| CSS | < 20 KB | Tailwind CSS v4 purges unused utilities |
| Icons | < 50 KB | SVG sprites; lazy-loaded by WMO code |
| Total initial load | < 400 KB | |

### Performance Targets

| Metric | Target | Measurement |
|---|---|---|
| Time to First Meaningful Content | < 2s on mobile 4G | Lighthouse (simulated throttling) |
| Time to Weather Data Visible | < 2s after city select | Manual timing + TanStack Query |
| Largest Contentful Paint (LCP) | < 2.5s | Core Web Vitals |
| Total Blocking Time (TBT) | < 200ms | Lighthouse |

### Caching Strategy

```
Browser Request → TanStack Query Cache (10 min staleTime)
  │
  ├── Cache FRESH → Serve immediately (0ms)
  ├── Cache STALE → Serve stale + background revalidate
  └── Cache MISS → Fetch from API (async, show skeleton)
         │
         ├── Fetch SUCCESS → Cache + render
         └── Fetch FAIL → Show usa-alert error; cache not updated
```

---

## 11. Deployment Architecture

### Vercel Configuration

```json
// vercel.json
{
  "buildCommand": "npm run build",
  "outputDirectory": "dist",
  "framework": "vite",
  "rewrites": [
    { "source": "/(.*)", "destination": "/index.html" }
  ]
}
```

### Build Pipeline

```
GitHub push to main
  → Vercel build trigger
  → npm run build (Vite)
      → TypeScript type check (tsc --noEmit)
      → Vitest unit tests
      → axe-core accessibility audit on build output
      → Tailwind CSS v4 purge
      → Bundle output to dist/
  → Vercel deploy to edge network
  → HTTPS certificate auto-managed
  → Deploy URL posted to GitHub PR (preview deployments on PRs)
```

---

## 12. Key Architecture Decisions

| Decision | Rationale | Outcome |
|---|---|---|
| Frontend-only SPA, no backend | Eliminates server cost, infrastructure ops, and attack surface for a v1 product with no user data needs | — Pending |
| Open-Meteo API | Only free weather API with no key requirement — eliminates #1 portfolio app failure mode | — Pending |
| `timezone=auto` on all Open-Meteo requests (mandatory) | Without it, hourly labels, sunrise/sunset, and day/night icon variants break for non-local-timezone cities | — Pending |
| TanStack Query with `staleTime: 10 min` | Prevents redundant API calls on every component mount; `isLoading`/`isError` states drive the never-blank-screen guarantee | — Pending |
| Geocoding decoupled from weather fetching | City search cache invalidated independently from weather cache; prevents cascade invalidation and enables clean partial updates | — Pending |
| Geolocation opt-in, city search always primary | Geolocation denial never produces a blank screen or stuck state; search is always the fallback path | — Pending |
| USWDS design tokens via Tailwind v4 CSS custom properties | Allows utility-class-driven development while guaranteeing USWDS token compliance without the full USWDS Sass build pipeline | — Pending |
| Integer-only temperature display | Decimal temperatures ("18.47°C") undermine trust; rounding to nearest integer in the transformation layer enforced app-wide | — Pending |
| All time display via `Intl.DateTimeFormat` with explicit `timeZone` | `new Date()` uses the browser's local timezone — using the API-returned timezone string prevents the most common weather app timezone bug | — Pending |
| Recharts for temperature trend chart | React-native; `AreaChart` fits the use case; accessible fallback via `sr-only` data table is feasible; verified ARIA support before selection | — Pending |
| WCAG 2.2 AA enforced via axe-core in CI | Accessibility regressions caught at build time, not post-ship; automated audit of all condition × background combinations | — Pending |

---

*TechArch version 1.0 — generated 2026-05-01*
*Sources: PRD-SimpleWeatherApp.md v1.1, FRD-SimpleWeatherApp.md v1.0, .planning/PROJECT.md*
*Next documents: UserStories, JOURNEYS, STORY-MAP*
