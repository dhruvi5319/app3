# UX Mockup
## Simple Weather App

**Project:** Simple Weather App
**Generated:** 2026-05-01
**Based on:** UserStories-SimpleWeatherApp.md, PRD-SimpleWeatherApp.md v1.1, FRD-SimpleWeatherApp.md v1.0, JOURNEYS-SimpleWeatherApp.md
**Design System:** USWDS (U.S. Web Design System) 3.x

---

## Overview

The Simple Weather App is a single-screen SPA with no navigation between pages. All content lives in a single vertical scroll on mobile; at wider viewports, components rearrange into multi-column layouts. The design principle is **progressive density**: each persona's data needs are satisfied at increasing scroll depth — Maya (Casual Checker) is served by the first viewport, David (Commuter) needs to scroll slightly to the hourly row, and Priya (Outdoor Enthusiast) benefits from the 7-day forecast and Details panel below.

**Design principles:**
- **USWDS first:** All components use USWDS design tokens, component classes, and ARIA patterns
- **Mobile-first:** 375px is the primary design target; desktop layouts are additive
- **Never blank:** Every state (loading, error, offline, empty) has a defined visual treatment
- **Progressive disclosure:** Secondary data is accessible without cluttering primary views
- **Integer temperatures:** No decimal values displayed anywhere in the app

---

## User Flows

### Flow 1: City Search → Weather Load

**Trigger:** User opens the app (new or returning)
**User Stories:** US-0.1, US-0.3, US-0.4, US-1.1, US-1.4

```
App opens
    │
    ▼
Recent location chips visible? ─── YES ──▶ Tap chip → Weather loads
    │                                             │
    NO                                            ▼
    │                                    Skeleton → Data renders
    ▼
usa-search input focused
    │
    ▼
User types 2+ characters (300ms debounce)
    │
    ▼
Geocoding API called
    │
    ├── Results ──▶ Suggestion listbox appears (up to 5)
    │                   │
    │                   ▼
    │               User selects (click / Enter key)
    │                   │
    │                   ▼
    │               Active location set → Weather API called
    │                   │
    │                   ├── Loading ──▶ Skeleton hero
    │                   │
    │                   ├── Success ──▶ Data renders; location chip saved
    │                   │
    │                   └── Error ──▶ usa-alert--error + retry button
    │
    └── No results ──▶ usa-alert--warning: "City not found"
                            │
                            ▼
                        Input remains editable; user retries
```

**Steps:**
1. On load, `usa-search` input is focused (or recent location chips are immediately tappable)
2. User types city name; suggestion listbox appears below input
3. User selects via mouse click, touch tap, or Enter key (keyboard navigation via arrow keys)
4. Weather API called with lat/lon; skeleton hero replaces any previous content
5. API success: skeleton replaced with live data; recent locations updated in localStorage
6. API failure: `usa-alert--error` replaces skeleton; retry button available

---

### Flow 2: GPS Geolocation

**Trigger:** User taps GPS button alongside search input
**User Stories:** US-0.3

```
User taps GPS button (usa-button--outline)
    │
    ▼
Browser geolocation permission requested
    │
    ├── Granted ──▶ GPS coords obtained
    │                   │
    │                   ▼
    │               Nominatim reverse geocoding
    │                   │
    │                   ▼
    │               City name resolved → Weather loads
    │
    └── Denied / Unavailable ──▶ No error shown; search input remains active
```

---

### Flow 3: Error Recovery

**Trigger:** API fails or user is offline
**User Stories:** US-1.5, US-7.2, US-7.3

```
Weather API call fails
    │
    ├── Cached data exists ──▶ usa-alert--info: "Showing cached data from X min ago"
    │                              │
    │                              ▼
    │                         Cached data renders; freshness notice persistent
    │
    └── No cached data ──▶ usa-alert--error: "Unable to load weather"
                                │
                                ▼
                           Retry button visible
                                │
                                ├── Retry succeeds ──▶ Data renders normally
                                └── Retry fails ──▶ Alert updates with "Check your connection"
```

---

## Screen Designs

### Screen: Main Weather View (Mobile — 375px)

**Purpose:** Single-screen app entry point. Answers "what's the weather?" for the active location.
**User Stories:** US-0.1–US-0.5, US-1.1–US-1.5, US-2.1, US-3.1, US-6.1, US-7.1, US-9.1

#### Layout (Mobile — 375px)

```
┌─────────────────────────────────────────┐  ← 375px viewport
│ ┌─────────────────────────────────────┐ │
│ │  [usa-search input]  [GPS btn]      │ │  ← Search bar (always visible)
│ └─────────────────────────────────────┘ │
│ [Recent location chips: Austin · Portland]│  ← usa-tag chips (if any)
├─────────────────────────────────────────┤
│                                         │
│  ████████████████████████████████████  │  ← Condition-aware gradient background
│  ☁ Partly Cloudy                        │  ← Icon (SVG) + text label (always paired)
│                                         │
│      72°F                               │  ← Current temp (large, dominant)
│      Feels like 70°F                    │  ← Feels-like (secondary)
│                                         │
│  H: 78°F  L: 61°F    🌂 30%            │  ← High/Low + Precipitation
│  💧 65%   🌬 8 mph                     │  ← Humidity + Wind
│                                         │
│  [°C]  [°F]   Updated 4 min ago        │  ← Unit toggle + freshness
│                                         │
├─────────────────────────────────────────┤
│  ── Hourly Forecast ────────────────── │
│  ← [8AM ☀ 72° 15%] [9AM ⛅ 70° 20%] → │  ← Horizontal scroll, 24 cards
│     [10AM ⛅ 68° 25%] [11AM 🌦 66° 35%] │
├─────────────────────────────────────────┤
│  ── 7-Day Forecast ─────────────────── │
│  Today   ⛅  H:78  L:61  30%           │
│  Mon     ☀   H:82  L:63  10%           │
│  Tue     🌦  H:71  L:58  55%           │
│  Wed     ⛅  H:75  L:60  20%           │
│  Thu     ☀   H:83  L:65  5%            │
│  Fri     🌧  H:68  L:55  75%           │
│  Sat     ⛅  H:74  L:59  25%           │
├─────────────────────────────────────────┤
│  ── [▼ Details] ─────────────────────  │  ← usa-accordion trigger (collapsed default)
│   (UV · Wind · Visibility · Sunrise)    │  ← Trigger label hints at contents
├─────────────────────────────────────────┤
│  [Temperature trend chart]              │  ← Recharts AreaChart (7-day high curve)
├─────────────────────────────────────────┤
│  Weather data: Open-Meteo (CC BY 4.0)  │  ← usa-footer attribution link
└─────────────────────────────────────────┘
```

#### Information Hierarchy

| Priority | Content | Placement | Rationale |
|---|---|---|---|
| Primary | Current temperature (integer) | Hero, large type, above fold | Core answer to "what do I wear?" |
| Primary | Weather condition (icon + text) | Hero, adjacent to temperature | Communicates context (rain/sun/etc.) |
| Primary | Precipitation probability | Hero, below temperature pair | Umbrella decision — must not be buried |
| Secondary | Feels-like temperature | Hero, smaller type | Supports outfit decision |
| Secondary | Today's high/low | Hero | Planning context |
| Secondary | Humidity + wind speed | Hero | Supporting detail |
| Secondary | Unit toggle (°C/°F) | Hero, always visible | JTBD-01.2 — must not require navigation |
| Tertiary | Freshness indicator | Hero, small type | Trust signal for commuter decisions |
| Section | Hourly forecast row | Below hero, horizontal scroll | Commuter's primary use case |
| Section | 7-day daily forecast | Below hourly | Outdoor enthusiast's primary use case |
| Collapsed | Details panel | Below 7-day, collapsed default | Secondary metrics — progressive disclosure |
| Utility | Temperature trend chart | Below 7-day | Visual planning aid |
| Required | Attribution footer | Bottom of page | Legal requirement (CC BY 4.0) |

---

### Screen: Main Weather View (Desktop — 1024px+)

**Purpose:** Same content as mobile, optimized for wider viewport with horizontal layout.

#### Layout (Desktop — 1024px+)

```
┌────────────────────────────────────────────────────────────────┐
│  [usa-search input — wider]                    [GPS btn]       │  ← Full-width search bar
│  [Recent: Austin · Portland · Chicago]                         │
├────────────────────────────────────────────────────────────────┤
│ ┌─────────────────────────────────┐  ┌─────────────────────┐  │
│ │   HERO (left col, 60%)          │  │  7-DAY (right, 40%) │  │
│ │                                 │  │                     │  │
│ │  ☁ Partly Cloudy                │  │  Today  ⛅ 78/61 30%│  │
│ │                                 │  │  Mon    ☀  82/63 10%│  │
│ │        72°F                     │  │  Tue    🌦 71/58 55%│  │
│ │        Feels like 70°F          │  │  Wed    ⛅ 75/60 20%│  │
│ │                                 │  │  Thu    ☀  83/65  5%│  │
│ │  H: 78°F   L: 61°F   🌂 30%    │  │  Fri    🌧 68/55 75%│  │
│ │  💧 65%    🌬 8 mph             │  │  Sat    ⛅ 74/59 25%│  │
│ │                                 │  └─────────────────────┘  │
│ │  [°C] [°F]   Updated 4 min ago  │                           │
│ └─────────────────────────────────┘                           │
├────────────────────────────────────────────────────────────────┤
│  ── Hourly Forecast ──────────────────────────────────────────│
│  [8AM ☀ 72° 15%] [9AM ⛅ 70° 20%] [10AM 68° 25%] ... →      │
├────────────────────────────────────────────────────────────────┤
│ ┌─────────────────────────────────────────────────────────┐   │
│ │  [▼ Details: UV · Wind · Visibility · Sunrise · Sunset] │   │  ← usa-accordion
│ │  (expanded state shown below)                           │   │
│ └─────────────────────────────────────────────────────────┘   │
├────────────────────────────────────────────────────────────────┤
│  [Temperature trend AreaChart — full width]                    │
├────────────────────────────────────────────────────────────────┤
│  Weather data: Open-Meteo (CC BY 4.0)  ·  [usa-footer]        │
└────────────────────────────────────────────────────────────────┘
```

---

### Screen: Details Panel (Expanded State)

**Purpose:** Secondary metrics for outdoor planning. Collapsed by default; expanded on user action.
**User Stories:** US-6.1, US-6.2

#### Layout (Details Expanded)

```
┌────────────────────────────────────────────┐
│  [▲ Hide Details]  ← usa-accordion trigger │  ← aria-expanded="true"
├────────────────────────────────────────────┤
│                                            │
│  UV Index         4 — Moderate             │
│  Wind             12 mph  SSE (162°)       │
│  Visibility       8 km                     │
│  Humidity         65%                      │
│  Sunrise          6:18 AM                  │  ← City's local time
│  Sunset           8:42 PM                  │  ← City's local time
│                                            │
└────────────────────────────────────────────┘
```

**Notes:**
- All times use the API-returned `timezone` field — never the user's browser timezone
- Wind direction shown as both cardinal (SSE) and degrees (162°) per FRD spec
- Panel returns to collapsed state on page reload (no persistence)

---

### Screen: Autocomplete Suggestion List

**Purpose:** City name disambiguation during search.
**User Stories:** US-0.1, US-0.2, US-0.5

#### Layout

```
┌─────────────────────────────────────────┐
│  [Austin                          ] [GPS]│  ← usa-search input (focused)
│  ┌───────────────────────────────────┐  │
│  │ ► Austin, Texas, United States   │  │  ← Highlighted (Down arrow)
│  │   Austin, Manitoba, Canada       │  │
│  │   Austin, Nevada, United States  │  │
│  │   Austintown, Ohio, United States│  │
│  │   Austinburg, Ohio, United States│  │
│  └───────────────────────────────────┘  │
│  [Austin   · Portland · Chicago]        │  ← Recent chips (behind suggestion list)
└─────────────────────────────────────────┘
```

**ARIA structure:**
```
<input role="combobox" aria-expanded="true" aria-controls="suggestion-list" aria-activedescendant="opt-0">
<ul id="suggestion-list" role="listbox">
  <li id="opt-0" role="option" aria-selected="true">Austin, Texas, United States</li>
  <li id="opt-1" role="option" aria-selected="false">Austin, Manitoba, Canada</li>
  ...
</ul>
<div aria-live="polite">5 results available</div>
```

---

## Interaction Patterns

### Pattern: Horizontal Hourly Scroll

**When to use:** Hourly forecast row (24 cards)
**Behavior:**
- Touch: native horizontal touch scroll; no JS required
- Mouse: horizontal scroll wheel; or grab-and-drag on desktop
- Keyboard: Tab moves focus to the scroll container region; arrow keys scroll horizontally within it
- The container has `role="region"` and `aria-label="Hourly forecast"`

**Example usage:** F2 hourly forecast row

---

### Pattern: Unit Toggle (Temperature °C/°F)

**When to use:** Everywhere temperature is displayed
**Behavior:**
- Two `usa-button-group` buttons: "°C" and "°F"
- Active button: `aria-pressed="true"`, filled USWDS button style
- Inactive button: `aria-pressed="false"`, outline USWDS button style
- On toggle: all temperature values in hero, hourly, and 7-day update immediately (no page reload)
- Preference stored in `localStorage` key `swa:unit` on every toggle

**Example markup:**
```html
<div class="usa-button-group" role="group" aria-label="Temperature unit">
  <button class="usa-button" aria-pressed="true">°F</button>
  <button class="usa-button usa-button--outline" aria-pressed="false">°C</button>
</div>
```

---

### Pattern: Skeleton Loading

**When to use:** Any time weather data is being fetched
**Behavior:**
- Hero section replaced with a shimmer-animated skeleton that matches the approximate layout of the loaded state
- Skeleton prevents layout shift when data arrives
- `aria-busy="true"` on the hero container during loading
- `prefers-reduced-motion`: shimmer animation replaced with a static grey placeholder

**Skeleton layout (hero):**
```
┌─────────────────────────────────────────┐
│  [████████████████] ← condition         │
│                                         │
│  [██████] ← temperature                 │
│  [████████████] ← feels-like            │
│                                         │
│  [██████] [█████████] [███] ← H/L/precip│
└─────────────────────────────────────────┘
```

---

### Pattern: USWDS Alert States

**When to use:** All error, warning, and informational states
**Behavior:** `usa-alert` component with appropriate variant

| Scenario | USWDS Alert Variant | Heading | Body |
|---|---|---|---|
| API failure (no cache) | `usa-alert--error` | "Weather unavailable" | "Please try again shortly." + retry button |
| Offline with cached data | `usa-alert--info` | "Using cached data" | "Showing weather from X minutes ago. Connect to refresh." |
| Offline, no cache | `usa-alert--error` | "No connection" | "Unable to load weather — check your connection." + retry button |
| City not found | `usa-alert--warning` (inline, below search) | — | "City not found — try a different spelling" |
| Slow loading (>5s) | `usa-alert--info` | "Still loading…" | "Weather data is taking longer than expected." |

All alerts use `aria-live="polite"` (except API failures which use `"assertive"`).

---

### Pattern: Condition-Aware Background Gradient

**When to use:** Current conditions hero section
**Behavior:** The hero `div` applies one of ~8 predefined linear gradients based on `wmoCode` group + `isDay`.

| Condition Group | Day Gradient (USWDS tokens) | Night Gradient |
|---|---|---|
| Clear (0–1) | `blue-40` → `blue-60` | `blue-80` → `gray-90` |
| Partly Cloudy (2) | `blue-30` → `blue-50` | `blue-70` → `gray-80` |
| Overcast (3) | `gray-20` → `gray-40` | `gray-60` → `gray-80` |
| Fog (45, 48) | `gray-10` → `gray-30` | `gray-50` → `gray-70` |
| Rain/Drizzle (51–67, 80–82) | `blue-50` → `gray-50` | `blue-70` → `gray-80` |
| Snow (71–77, 85–86) | `gray-5` → `blue-10` | `gray-30` → `blue-40` |
| Thunderstorm (95–99) | `gray-60` → `blue-80` | `gray-80` → `gray-90` |

All gradient backgrounds validated for ≥ 4.5:1 contrast ratio with USWDS white text before ship.

---

## Responsive Considerations

### Mobile (375px – 639px) — USWDS `mobile` to `tablet`

- Single-column stack throughout
- Hero occupies full viewport width
- Hourly row horizontal-scrolls within the full width
- 7-day rows are full-width with compact padding
- Details accordion trigger and content are full-width
- Temperature trend chart is full-width, height capped at 120px
- All tap targets ≥ 44×44px
- Font size: USWDS `uswds-xl` (24px) for current temperature; `uswds-md` (16px) for supporting data

### Tablet (640px – 1023px) — USWDS `tablet`

- Hero and 7-day list shift to a 60/40 two-column layout
- Hourly row remains full-width horizontal scroll
- Details accordion spans full width
- Temperature trend chart spans full width, height increased to 160px

### Desktop (1024px+) — USWDS `desktop`

- Max content width: 1200px (centered, with horizontal padding)
- Hero (left 60%) + 7-day (right 40%) side by side
- Hourly row below the hero/7-day pair, full width
- Details accordion and chart below hourly row
- All interactive elements maintain 44px minimum height (USWDS requirement)
- Search bar spans full content width for easy keyboard access

### 125% Browser Zoom (Priya Nair's use pattern)

- USWDS grid with percentage-based columns reflows correctly
- `em`-based USWDS spacing units scale proportionally
- No horizontal overflow at any breakpoint when zoomed to 125%
- All text content wraps within column bounds (no `white-space: nowrap` on variable-length content)

---

## Accessibility Notes

### Color Contrast
- All text on condition-aware gradient backgrounds: ≥ 4.5:1 contrast ratio (WCAG 1.4.3)
- USWDS color tokens selected for all palette values — token compliance verifies contrast minimums
- Validated across all 7 condition group × 2 time-of-day combinations (14 total)
- axe-core automated scan must pass on production build with zero contrast violations

### Keyboard Navigation
- Tab order: [Skip nav] → [Search input] → [GPS button] → [Recent chip 1..N] → [Hourly row region] → [7-day rows] → [Details accordion trigger] → [Unit toggle °C] → [Unit toggle °F] → [Footer link]
- Search autocomplete: Down arrow enters listbox, Up/Down navigates options, Enter selects, Escape dismisses
- Details accordion: Enter/Space on trigger to toggle; focus stays on trigger after collapse

### ARIA Labels
- Search input: `aria-label="Search for a city"` (or label element)
- GPS button: `aria-label="Use my location"`
- Hourly scroll container: `role="region"` `aria-label="Hourly forecast"`
- 7-day section: `role="region"` `aria-label="7-day forecast"`
- Unit toggle group: `role="group"` `aria-label="Temperature unit"`
- Details accordion trigger: `aria-expanded` + `aria-controls`
- Recharts AreaChart: `aria-hidden="true"` + adjacent `sr-only` data table with same values
- Live regions: `aria-live="polite"` for data load announcements and freshness updates

### Screen Reader Experience
- On city selection: `aria-live="polite"` region announces "Weather loaded for [City Name]"
- On autocomplete open: `aria-live="polite"` announces "N results available"
- On unit toggle: temperature values update in place; `aria-live="polite"` can announce "Changed to Fahrenheit"
- All icons are `aria-hidden="true"` (decorative); the adjacent text label carries the semantic meaning

### Focus Management
- USWDS `:focus-visible` focus ring (2px solid, 2px offset) on all interactive elements
- No `outline: none` without replacement focus treatment
- Focus never trapped (no modals or dialogs in this app's v1 scope)
- Skip to main content link as first tab stop (WCAG 2.4.1)

---

*UX-Mockup version 1.0 — generated 2026-05-01*
*Sources: UserStories-SimpleWeatherApp.md, PRD-SimpleWeatherApp.md v1.1, FRD-SimpleWeatherApp.md v1.0, JOURNEYS-SimpleWeatherApp.md*
*Design System: USWDS 3.x — all components, tokens, and ARIA patterns derived from USWDS documentation*
