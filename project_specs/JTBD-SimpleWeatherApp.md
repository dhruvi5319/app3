# Jobs-to-be-Done Document
## Simple Weather App

| Field | Value |
|---|---|
| **Product Name** | Simple Weather App |
| **Version** | 1.0 |
| **Date** | 2026-05-01 |
| **Status** | Active |
| **Related Personas** | PERSONAS-SimpleWeatherApp.md |
| **Related PRD** | PRD-SimpleWeatherApp.md v1.1 |

---

## Table of Contents

1. [JTBD Summary Table](#1-jtbd-summary-table)
2. [PER-01: Maya Torres — Casual Checker](#2-per-01-maya-torres--casual-checker)
3. [PER-02: David Park — Commuter](#3-per-02-david-park--commuter)
4. [PER-03: Priya Nair — Outdoor Enthusiast](#4-per-03-priya-nair--outdoor-enthusiast)
5. [Outcome-to-Feature Traceability](#5-outcome-to-feature-traceability)
6. [NaC Preview](#6-nac-preview)

---

## 1. JTBD Summary Table

| JTBD-ID | Persona | Job Statement (abbreviated) | Priority |
|---|---|---|---|
| JTBD-01.1 | PER-01 Maya Torres | Get the umbrella answer instantly on mobile | P0 |
| JTBD-01.2 | PER-01 Maya Torres | Switch temperature units without navigating away | P0 |
| JTBD-01.3 | PER-01 Maya Torres | Trust the app to never show a blank screen | P1 |
| JTBD-02.1 | PER-02 David Park | Plan commute windows using hourly precipitation | P1 |
| JTBD-02.2 | PER-02 David Park | Operate the full app by keyboard on desktop | P1 |
| JTBD-02.3 | PER-02 David Park | Trust that hourly times reflect the searched city's timezone | P0 |
| JTBD-03.1 | PER-03 Priya Nair | Choose the best day for outdoor activities using 7-day forecast | P0 |
| JTBD-03.2 | PER-03 Priya Nair | Access secondary metrics without cluttering the main view | P1 |
| JTBD-03.3 | PER-03 Priya Nair | Distinguish weather conditions without relying on color | P0 |

---

## 2. PER-01: Maya Torres — Casual Checker

### JTBD-01.1: Get the Umbrella Answer Instantly on Mobile

**Job Statement:**
When I open the weather app on my phone in the morning before leaving home, I want to see the current temperature, weather condition, and precipitation probability immediately above the fold, so I can decide what to wear and whether to grab an umbrella without reading more than three data points.

**Current Alternatives:**
- Uses phone's built-in weather widget, which lacks precipitation probability
- Opens Weather.com or AccuWeather, waits through 4–8 seconds of ads and tracking scripts before seeing relevant data
- Asks a smart speaker — loses the visual confirmation she wants

**Hiring Criteria:**
- Current temperature visible in large type above the fold on a 375px mobile screen with no scrolling
- Precipitation probability displayed prominently alongside the temperature
- Data visible within 2 seconds of typing a city name on a mobile connection
- No account prompt, no notification permission request, no ad blocking the answer

**Success Measure:** Maya can read current temperature, condition, and precipitation probability within 3 seconds of opening the app on a mobile device, with zero interaction required beyond city search.

**Related Features:** F0, F1
**Priority:** P0

---

### JTBD-01.2: Switch Temperature Units Without Navigating Away

**Job Statement:**
When I move between contexts that use different temperature scales (e.g., traveling internationally or reading a forecast shared in Celsius), I want to toggle between °C and °F directly from the main weather screen, so I can reinterpret the data without leaving the view or searching through settings menus.

**Current Alternatives:**
- Digs through app settings menus, often losing her current city in the process
- Opens a second tab to do a manual unit conversion
- Uninstalls apps that bury this toggle, switches to a different app

**Hiring Criteria:**
- °C/°F toggle visible on the main screen without any navigation gesture
- Toggle is immediately visible on first visit — no discovery required
- Preference persisted in localStorage so she does not have to toggle again on next visit

**Success Measure:** Maya locates and uses the temperature unit toggle within 10 seconds of first encountering it, with zero settings navigation required.

**Related Features:** F1
**Priority:** P0

---

### JTBD-01.3: Trust the App to Never Show a Blank Screen

**Job Statement:**
When the network is slow or the API fails while I am checking weather before leaving for work, I want to see a clear, helpful message or cached data instead of a blank screen or spinner that never resolves, so I can still make a decision or know to check another source.

**Current Alternatives:**
- Puts the phone down and checks again later — loses time in her morning routine
- Accepts the blank screen as a broken app and switches to her phone's built-in widget
- Refreshes repeatedly with no indication of whether a retry is working

**Hiring Criteria:**
- Skeleton loading state shown while data is fetching — never a blank screen during load
- Clear, friendly error message displayed on API failure (USWDS `usa-alert` component)
- Last cached data shown with a "showing data from X minutes ago" notice when offline
- "City not found" message shown if geocoding returns no results — never a stuck state

**Success Measure:** Zero blank-screen occurrences across all tested error paths (API failure, offline, city not found, geolocation denied).

**Related Features:** F0, F1, F7
**Priority:** P1

---

## 3. PER-02: David Park — Commuter

### JTBD-02.1: Plan Commute Windows Using Hourly Precipitation

**Job Statement:**
When I am deciding whether to bike or take transit for my morning or evening commute, I want to scan the hourly precipitation probability for the next 24 hours in a single scrollable view, so I can identify the dry windows and choose the right departure time without navigating to a separate screen or paying for a premium tier.

**Current Alternatives:**
- Uses a mainstream app's hourly view, which requires 3+ taps to reach and is often paywalled
- Checks the 10-day daily forecast and extrapolates — imprecise for same-day planning
- Uses `wttr.in` in a terminal — fast but no visual scanning, no probability per hour

**Hiring Criteria:**
- Horizontally scrollable hourly forecast row visible on the main screen as a first-class component — no tab or paywall
- Precipitation probability shown on every hourly card — never omitted
- Hourly time labels in the searched city's local timezone (not the user's browser timezone)
- 44px minimum touch targets on all hourly cards for tap accuracy on a moving train

**Success Measure:** David identifies the first dry commute window for the evening by scanning the hourly row in under 15 seconds, without leaving the main screen.

**Related Features:** F2, F4, F7
**Priority:** P1

---

### JTBD-02.2: Operate the Full App by Keyboard on Desktop

**Job Statement:**
When I am checking weather at my work desk using my laptop, I want to navigate the entire app — search, select a city, scroll the forecast, expand details — using only keyboard controls, so I can operate it fluently without switching from keyboard to mouse between tasks.

**Current Alternatives:**
- Forces mouse use for autocomplete dropdowns that are not keyboard-navigable — interrupts his keyboard-first workflow
- Tabs past interactive elements with no visible focus indicator — cannot tell where he is in the page
- Falls back to a browser extension or CLI weather tool that has better keyboard support

**Hiring Criteria:**
- Autocomplete suggestions navigable via arrow keys; Enter to select, Escape to dismiss (USWDS combo-box pattern)
- Visible USWDS focus indicator (2px solid outline with offset) on every interactive element
- Logical Tab order that moves through: search input → hourly forecast → 7-day forecast → details panel → unit toggle → footer
- USWDS accordion trigger for the Details panel responds to Enter and Space keystrokes

**Success Measure:** David completes a full weather check session (search city → read hourly → expand details panel) using only keyboard controls, with no forced mouse interaction.

**Related Features:** F0, F6, F8
**Priority:** P1

---

### JTBD-02.3: Trust That Hourly Times Reflect the Searched City's Timezone

**Job Statement:**
When I search for weather in a city in a different timezone (e.g., checking conditions at my travel destination or a client's city), I want all time displays — hourly labels, sunrise, sunset — to reflect that city's local time, so I can reason about the weather relative to activities happening in that location, not in my browser's timezone.

**Current Alternatives:**
- Many apps display times in the user's browser timezone — David has been confused by this and made wrong plans
- Manually adds or subtracts hours from displayed times — error-prone
- Accepts the timezone ambiguity and verifies with a second source

**Hiring Criteria:**
- `timezone=auto` set on every Open-Meteo API request — non-negotiable
- All hourly time labels derived from the API's returned timezone, not `new Date()` in the user's browser
- Sunrise and sunset times in the Details panel reflect the searched city's local time
- Timezone offset clearly derivable from displayed times (i.e., "9 AM" means 9 AM local to the searched city)

**Success Measure:** David searches for a city in a timezone 5 hours different from his own and all hourly labels and sunrise/sunset times correctly reflect that city's local time, verified by cross-referencing with a known sunrise time for that city.

**Related Features:** F2, F4, F6
**Priority:** P0

---

## 4. PER-03: Priya Nair — Outdoor Enthusiast

### JTBD-03.1: Choose the Best Day for Outdoor Activities Using the 7-Day Forecast

**Job Statement:**
When I am planning a weekend cycling ride or a day of garden work, I want to scan the 7-day daily forecast showing high/low temperatures, precipitation probability, and a temperature trend chart for the full week, so I can compare days and choose the optimal one for physical outdoor activity without an account or paywall.

**Current Alternatives:**
- Uses a mainstream app that shows only 3 days on mobile — has to tap "more" and often hits an account wall
- Checks multiple apps and reconciles conflicting forecasts — time-consuming
- Uses Weather Underground for 7-day, but its interface is cluttered with ads and station data she does not need

**Hiring Criteria:**
- All 7 forecast days visible in a single vertical list with no pagination, account wall, or "see more" tap
- High/low temperature pair shown per day (high first, then low)
- Precipitation probability shown on every daily row — never omitted
- Recharts AreaChart temperature trend visualization visible alongside or below the daily list
- Data from the same Open-Meteo API call as current conditions — no additional API request required

**Success Measure:** Priya compares Saturday and Sunday conditions and identifies the better cycling day in under 30 seconds, using only the 7-day forecast view and temperature trend chart, without navigating to any secondary screen.

**Related Features:** F3, F5
**Priority:** P0

---

### JTBD-03.2: Access Secondary Metrics Without Cluttering the Main View

**Job Statement:**
When I need to check UV index before deciding whether to do sun-exposed garden work, or wind direction before a cycling route, I want to expand a secondary details panel to see UV, wind speed, wind direction, visibility, and sunrise/sunset — without those metrics being permanently visible and cluttering the main weather view during my quick daily checks.

**Current Alternatives:**
- Uses apps that show all metrics always — overwhelming during quick checks, forces her to scan past noise to find the data she needs
- Some apps bury UV and wind direction in a "More Details" section that requires 3+ taps to navigate to
- Uses a separate dedicated UV index app alongside a weather app — two apps for one activity decision

**Hiring Criteria:**
- Collapsible "Details" panel (USWDS accordion) collapsed by default — secondary metrics are not in the main view on page load
- Single tap/click expands the panel to reveal: UV index, wind speed, wind direction (cardinal + degrees), visibility, humidity, sunrise time, sunset time
- All values use the searched city's local timezone for sunrise/sunset
- Panel state returns to collapsed on page reload — does not persist open state

**Success Measure:** Priya accesses the UV index value and wind direction within 5 seconds of opening the Details panel, with a single tap/click, from any viewport width (375px–1280px+).

**Related Features:** F6, F8
**Priority:** P1

---

### JTBD-03.3: Distinguish Weather Conditions Without Relying on Color

**Job Statement:**
When I scan weather condition icons across the hourly forecast, daily forecast, and current conditions display, I want each condition to be communicated by both an icon and a text label — never by icon or color alone — so I can reliably distinguish between conditions like "Light Rain" and "Heavy Rain" without depending on color differentiation that my color vision deficiency makes unreliable.

**Current Alternatives:**
- Many apps use color-coded icons without text labels — Priya often misreads condition severity
- She zooms into icons to examine shape detail as a substitute for color — slow and frustrating
- She has memorized icon shapes in her current app as a workaround — any new app resets this learned pattern

**Hiring Criteria:**
- Every weather icon displayed alongside a text label describing the condition (e.g., "Partly Cloudy", "Light Rain")
- No condition communicated by color alone — WCAG 1.4.1 compliance enforced throughout (F8)
- Icons cover the full WMO weather code range (0–99) — no unknown/fallback icon for any valid API response
- Condition-aware background gradients achieve WCAG 1.4.3 minimum 4.5:1 contrast ratio with overlaid text — validated for all condition × time-of-day combinations

**Success Measure:** Priya correctly identifies the forecast condition for all 7 forecast days — including edge cases like "Freezing Drizzle" vs "Drizzle" — without color differentiation, using only icon shape and text label, with zero errors.

**Related Features:** F4, F8
**Priority:** P0

---

## 5. Outcome-to-Feature Traceability

| JTBD-ID | Feature ID | Feature Name | Expected Outcome |
|---|---|---|---|
| JTBD-01.1 | F0 | Location Search & Detection | City resolved to lat/lon in < 2s; recent locations persist |
| JTBD-01.1 | F1 | Current Conditions Display | Temp + condition + precipitation probability above fold on 375px mobile |
| JTBD-01.2 | F1 | Current Conditions Display | °C/°F toggle visible and functional on main screen; persisted in localStorage |
| JTBD-01.3 | F0 | Location Search & Detection | "City not found" message on geocoding failure; never blank |
| JTBD-01.3 | F1 | Current Conditions Display | Skeleton loading state; error message on API failure |
| JTBD-01.3 | F7 | Data Freshness & Stale State Handling | Cached data shown offline; "showing cached data from X min ago" notice |
| JTBD-02.1 | F2 | Hourly Forecast | 24-card scrollable row on main screen; precipitation per card; 44px targets |
| JTBD-02.1 | F4 | Weather Icons & Visual Indicators | Day/night icon variants per card using city's local timezone |
| JTBD-02.1 | F7 | Data Freshness & Stale State Handling | "Updated X minutes ago" indicator always visible |
| JTBD-02.2 | F0 | Location Search & Detection | Arrow key navigation in autocomplete; Enter to select; Escape to dismiss |
| JTBD-02.2 | F6 | Secondary Weather Details Panel | USWDS accordion responds to Enter/Space; focus managed on expand/collapse |
| JTBD-02.2 | F8 | Accessibility (WCAG AA) | USWDS 2px focus indicator; logical tab order; all elements keyboard-operable |
| JTBD-02.3 | F2 | Hourly Forecast | All hourly labels in city's local timezone via timezone=auto |
| JTBD-02.3 | F4 | Weather Icons & Visual Indicators | Day/night icon logic uses city's local sunrise/sunset, not browser timezone |
| JTBD-02.3 | F6 | Secondary Weather Details Panel | Sunrise/sunset in city's local time |
| JTBD-03.1 | F3 | 7-Day Daily Forecast | All 7 days visible; high/low + precipitation per day; AreaChart temperature trend |
| JTBD-03.1 | F5 | Responsive Layout | 7-day list renders correctly from 375px to 1280px+ without overflow |
| JTBD-03.2 | F6 | Secondary Weather Details Panel | USWDS accordion collapsed by default; UV, wind, visibility, sunrise/sunset on expand |
| JTBD-03.2 | F8 | Accessibility (WCAG AA) | accordion aria-expanded communicated; 44px trigger; keyboard Enter/Space support |
| JTBD-03.3 | F4 | Weather Icons & Visual Indicators | Every condition has icon + text label; all WMO codes (0–99) covered |
| JTBD-03.3 | F8 | Accessibility (WCAG AA) | WCAG 1.4.1: no condition by color alone; 4.5:1 contrast on all condition backgrounds |

---

## 6. NaC Preview

Natural Acceptance Criteria derived from job success measures. These will be refined in STORY-MAP.

| JTBD-ID | Outcome | Candidate Natural Acceptance Criteria |
|---|---|---|
| JTBD-01.1 | Current temp + condition + precipitation visible in < 3s | Given a city is searched, when the API responds, then temperature, condition label, and precipitation % are all visible above the fold on a 375px screen without scrolling |
| JTBD-01.1 | Data visible in < 2s on mobile connection | Given a city is entered, when the user submits, then weather data is visible within 2 seconds on a simulated mobile 4G connection (Lighthouse audit) |
| JTBD-01.2 | Unit toggle found in < 10s on first visit | Given the app is opened for the first time, when the user looks for the unit toggle, then it is visible on the main screen without any navigation or settings access |
| JTBD-01.2 | Unit preference persists across sessions | Given the user has selected °F, when they reload the app, then the unit is still °F without re-toggling |
| JTBD-01.3 | Zero blank screens on any error path | Given any error condition (API failure, offline, city not found, geolocation denied), when the condition occurs, then a visible message or skeleton/cached-data state is shown — never a blank screen |
| JTBD-02.1 | Hourly row visible on main screen | Given a city is loaded, when the user views the main screen, then the 24-hour forecast row is visible without navigating to a separate screen or tapping through a paywall |
| JTBD-02.1 | Precipitation on every hourly card | Given the hourly row is displayed, when any card is inspected, then a precipitation probability percentage is shown on every card |
| JTBD-02.2 | Full keyboard navigation of search autocomplete | Given the search input is focused, when the user types 2+ characters and presses the down arrow, then suggestions are highlighted and selectable via Enter, dismissible via Escape |
| JTBD-02.2 | USWDS focus indicator on all interactive elements | Given keyboard navigation is active, when any interactive element receives focus, then a USWDS-compliant 2px solid focus ring with offset is visible |
| JTBD-02.3 | Hourly times in searched city's timezone | Given a city in a timezone different from the user's browser, when the hourly forecast is displayed, then all time labels reflect that city's local time |
| JTBD-02.3 | Sunrise/sunset in searched city's timezone | Given a city is searched and the Details panel is expanded, when sunrise/sunset times are shown, then they match the city's local sunrise/sunset (verifiable against known almanac data) |
| JTBD-03.1 | All 7 days visible without interaction | Given a city is loaded, when the 7-day section is viewed, then all 7 days are visible without any "see more" tap, paywall, or account gate |
| JTBD-03.1 | AreaChart renders alongside daily forecast | Given the 7-day section is visible, when the page loads, then a temperature trend AreaChart is rendered adjacent to the daily list |
| JTBD-03.2 | Details panel collapsed by default | Given the app loads with data, when the Details panel is in its default state, then it is collapsed and secondary metrics (UV, wind, visibility, sunrise/sunset) are not visible in the main view |
| JTBD-03.2 | Single interaction expands Details panel | Given the Details panel is collapsed, when the user taps/clicks or presses Enter/Space on the trigger, then the panel expands and all secondary metrics are visible |
| JTBD-03.3 | Every icon paired with text label | Given weather data is displayed anywhere in the app, when any condition icon is shown, then a text label describing the condition appears adjacent to the icon |
| JTBD-03.3 | All WMO codes produce non-fallback icons | Given the Open-Meteo API returns any WMO code (0–99), when the icon is rendered, then a specific (non-generic fallback) icon and text label are displayed |

---

*JTBD version 1.0 — generated 2026-05-01*
*Sources: PERSONAS-SimpleWeatherApp.md, PRD-SimpleWeatherApp.md v1.1, .planning/PROJECT.md*
*Next documents: TechArch, UserStories, JOURNEYS*
