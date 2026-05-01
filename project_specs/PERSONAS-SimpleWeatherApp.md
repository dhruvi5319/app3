# Personas Document
## Simple Weather App

| Field | Value |
|---|---|
| **Product Name** | Simple Weather App |
| **Version** | 1.0 |
| **Date** | 2026-05-01 |
| **Status** | Active |
| **Related PRD** | PRD-SimpleWeatherApp.md v1.1 |
| **Derived From** | PRD Section 4 (Target Users & Personas), Section 2 (Problem Statement), Section 6 (Feature Requirements), Section 8 (Success Metrics) |

---

## Table of Contents

1. [Persona Summary Table](#1-persona-summary-table)
2. [PER-01: The Casual Checker](#2-per-01-the-casual-checker)
3. [PER-02: The Commuter](#3-per-02-the-commuter)
4. [PER-03: The Outdoor Enthusiast](#4-per-03-the-outdoor-enthusiast)
5. [Persona Relationships](#5-persona-relationships)
6. [Feature-Persona Matrix](#6-feature-persona-matrix)

---

## 1. Persona Summary Table

| ID | Name | Role | Primary Goal | Frequency |
|---|---|---|---|---|
| PER-01 | Maya Torres | Casual Checker | Get current temp + umbrella answer in under 3 seconds | 1–3 checks/day |
| PER-02 | David Park | Commuter | Scan hourly conditions to plan commute windows | 2–4 checks/day |
| PER-03 | Priya Nair | Outdoor Enthusiast | Plan multi-day outdoor activities using full forecast + secondary metrics | 3–5 checks/day |

---

## 2. PER-01: Maya Torres

**Role:** Casual Checker — Morning Decision-Maker

---

**Role & Context:**

Maya is a 34-year-old marketing coordinator who checks the weather as part of her morning routine before leaving for work. She opens the app on her phone while still in her kitchen — one hand holding coffee, the other swiping — expecting to see the answer before she sets the mug down. She uses weather data purely to make one or two decisions each day: what to wear, and whether to grab an umbrella or pack sunscreen. Maya checks weather 1–3 times daily, almost always on mobile, and almost always within 30 seconds of waking up or before leaving a location. She is not interested in meteorology — she wants a fast, trustworthy answer and nothing else.

Maya represents the largest segment of daily weather app users. She has abandoned multiple mainstream apps after being hit with autoplay video ads, notification permission pop-ups, or account registration walls before seeing a single temperature reading. She currently uses her phone's built-in weather widget, but finds it too simplified (no precipitation probability) and occasionally wrong about her neighborhood. She values apps that feel instant and honest over apps that feel feature-rich.

Maya expects a USWDS-standard accessible interface without knowing what USWDS is — large readable type, clear tap targets, no mystery gestures, and no layout surprises between portrait and landscape orientation. She has no accessibility requirements of her own but benefits from USWDS's enforced legibility, spacing, and contrast standards.

---

**Goals:**
- See current temperature and weather condition **above the fold without scrolling** — answer the "what do I wear?" question immediately (F1)
- Know precipitation probability at a glance so she can decide whether to grab an umbrella (F1)
- Toggle between °C and °F from the main screen without digging through settings (F1)
- Load the weather data for her city within 2 seconds of opening the app on a mobile connection (F0, F1)
- Never be asked to create an account or grant notification permissions just to see current weather (F0)

**Pain Points:**
- Mainstream apps (Weather.com, AccuWeather) take 4–8 seconds to show useful data due to ads and tracking scripts
- Autoplay video and sponsored content compete for screen space where temperature should appear
- The °C/°F toggle is buried in settings menus on most apps — she has to hunt for it every time she switches devices
- Notification permission prompts and account walls appear before the weather data
- "18.47°C" style precision reads as untrustworthy rather than accurate — she wants "18°C"
- Apps that show a blank screen or spinner with no feedback when the connection is slow

**Technical Expertise:** Basic — comfortable with standard mobile app patterns; does not use keyboard shortcuts; relies entirely on touch. Expects USWDS-standard touch targets (≥44px) on all interactive elements without knowing they're called that.

**Top Tasks:**
1. **Search for or confirm her city location** — types city name or taps GPS button to set location (daily, critical) *(F0)*
2. **Read current temperature and condition** — glances at the hero section to get the core answer (daily, critical) *(F1)*
3. **Check today's precipitation probability** — confirms whether rain is expected today (daily, high) *(F1)*
4. **Toggle temperature unit** — switches between °C and °F when context changes (occasional, medium) *(F1)*
5. **Check today's high/low** — decides whether to pack a layer for the afternoon (daily, medium) *(F1)*

**Success Criteria:**
- Can get the current temperature and precipitation probability visible within 2 seconds of typing her city name
- Never encounters a blank screen or unhandled error state — USWDS alert components communicate any failure clearly
- Unit toggle is visible on the main screen without any navigation — found on first visit, zero confusion
- Zero account prompts, zero notification permission requests during a normal weather check session
- All text legible at arm's length on a 375px mobile screen using USWDS typography scale

---

## 3. PER-02: David Park

**Role:** Commuter — Transit and Timing Planner

---

**Role & Context:**

David is a 28-year-old software engineer who commutes 45 minutes each way by public transit and bike. He lives in a climate where weather changes meaningfully within a single day — mornings can be foggy and cold while afternoons are warm — so checking a single "today's high" is not enough for him. He needs to know what conditions look like hour by hour during his commute window (7–9 AM outbound, 6–8 PM return). David checks weather 2–4 times daily: once before leaving in the morning, once mid-morning if rain looks possible, and once before leaving the office.

David is technically proficient and uses a mix of mobile and desktop throughout his day. He switches between his phone during transit and his laptop at work. He expects the app to behave identically on both form factors — he navigates on desktop primarily by keyboard and mouse, and appreciates correct focus management and keyboard-navigable components. He has previously used developer-adjacent tools like `wttr.in` and appreciates their speed, but needs actual visual clarity for quick scanning during a commute.

David is a secondary beneficiary of USWDS accessibility standards: he uses keyboard navigation on desktop and expects logical tab order, visible focus indicators, and keyboard-operable components (USWDS 2px focus outline, Enter/Space on interactive elements). He would notice — and be frustrated by — mouse-trap interactions or inaccessible autocomplete dropdowns.

---

**Goals:**
- See an **hourly forecast for the next 24 hours** without navigating to a separate screen or paying for premium access (F2)
- Scan precipitation probability **per hour** to identify rain windows during commute times (F2)
- Load a different city quickly (e.g., destination city on a work trip) without needing to log in or reconfigure (F0)
- Navigate the full app by keyboard on desktop — tab order, arrow keys in autocomplete, Enter to select (F0, F8)
- Trust that hourly times are in the correct **local timezone for the searched city**, not his browser's timezone (F2, F4)

**Pain Points:**
- Mainstream apps hide the hourly forecast behind a paywall or a separate screen requiring 3+ taps
- Hourly cards on mobile apps frequently use touch targets too small to tap accurately on a moving train
- Many apps show hourly times in the user's local timezone rather than the searched city's timezone — causing confusion when searching for weather in another time zone
- No way to quickly switch locations between home and work cities without re-typing each time
- Autocomplete dropdowns that are not keyboard-navigable, forcing mouse use on desktop

**Technical Expertise:** Advanced — uses keyboard shortcuts regularly on desktop, comfortable with web app patterns, understands progressive disclosure. Expects USWDS keyboard navigation patterns: arrow keys in list boxes, Escape to dismiss, Tab to move between interactive regions.

**Top Tasks:**
1. **Search for a city by name using autocomplete** — types 2+ characters, uses arrow keys to navigate suggestions, Enter to select (daily, critical) *(F0)*
2. **Scan the hourly forecast row for commute window** — horizontally scrolls through the next 24 hours, reading precipitation probability per card (daily, critical) *(F2)*
3. **Identify the rain cutoff hour** — looks for the first and last hour with >30% precipitation probability to bracket the commute window (daily, high) *(F2)*
4. **Check data freshness indicator** — confirms the displayed data is current before making a commute decision (daily, medium) *(F7)*
5. **Switch between cities** — re-searches for a different location mid-session (weekly, medium) *(F0)*

**Success Criteria:**
- Hourly forecast visible on the main screen as a first-class component — no tab switching or paywall
- All hourly cards display precipitation probability without exception
- Hourly time labels correctly reflect the searched city's local timezone (not the user's browser timezone)
- Full app keyboard-navigable on desktop: autocomplete via arrow keys, Enter to confirm, Escape to dismiss, Tab between major regions
- USWDS focus indicators (2px solid offset) visible on all interactive elements during keyboard navigation
- Horizontal scroll container labeled as `role="region"` with `aria-label="Hourly forecast"` for screen reader users
- Data freshness indicator always visible so David can decide whether to force a refresh

---

## 4. PER-03: Priya Nair

**Role:** Outdoor Enthusiast — Weekend Activity Planner

---

**Role & Context:**

Priya is a 41-year-old landscape architect and recreational cyclist who plans her weekend rides and garden work around weather windows. She thinks about weather in terms of multi-day planning horizons — she is not asking "will it rain in the next hour?" but rather "is Saturday or Sunday better for a 40-mile ride?" and "which afternoon this week has the lowest UV index for transplanting seedlings?" She checks weather 3–5 times daily during the week leading up to a planned outdoor activity, and she cares about secondary metrics (UV index, wind speed and direction, visibility, sunrise/sunset) that most casual users ignore.

Priya uses the app primarily on her tablet and laptop at home or at her desk, where she has the screen space to review full-week forecasts in detail. She is comfortable with multi-section layouts and is used to expanding detail panels to see the full picture — but she does not want secondary data cluttering the main view when she is doing a quick check. She appreciates the progressive disclosure model (a collapsible Details panel) precisely because it gives her the power without the noise.

Priya has a mild color vision deficiency (deuteranomaly) and relies on icon + text label pairings rather than color alone to distinguish weather conditions. She directly benefits from the USWDS requirement that weather condition is never conveyed by color alone (WCAG 1.4.1) — the icon + text label pairing mandated by the design system is not optional for her. She also uses browser zoom at 125% on her laptop and expects the USWDS layout to reflow without overflow at larger text sizes.

---

**Goals:**
- See a **7-day daily forecast** showing high/low temperatures and precipitation probability without an account or paywall (F3)
- Access **secondary metrics** (UV index, wind direction, visibility, sunrise/sunset) when planning outdoor timing — without those metrics cluttering the view for quick checks (F6)
- Understand the week's **temperature trend at a glance** via the Recharts area chart (F3)
- Rely on **icon + text label pairings** to distinguish weather conditions — never icon-only displays (F4, F8)
- Use the app **at 125% browser zoom** on a 1280px desktop without horizontal overflow or truncated labels (F5)

**Pain Points:**
- Apps that show only 3 days of forecast on mobile, requiring a "see more" tap to get to day 5 and 6
- Secondary metrics (UV, wind direction, sunrise/sunset) buried in hard-to-find settings screens or absent entirely
- Weather condition icons used without text labels — color-coded icons she cannot reliably distinguish
- 7-day forecast gated behind an account registration or premium paywall
- Apps that show forecast data without timezone awareness — sunrise/sunset displayed in wrong timezone for a searched city
- Cluttered layouts where all secondary data is always visible, creating visual noise during quick morning checks

**Technical Expertise:** Intermediate — comfortable with web apps, multi-section layouts, and progressive disclosure patterns. Uses browser zoom regularly. Does not use screen readers but benefits from USWDS accessibility guarantees (sufficient contrast, icon+text pairings, semantic layout). Navigates by mouse and touchpad; rarely uses keyboard shortcuts.

**Top Tasks:**
1. **Review the 7-day forecast** — scans all 7 days for high/low and precipitation to choose the best outdoor day (multiple times/week, critical) *(F3)*
2. **Expand the Details panel for secondary metrics** — opens the USWDS accordion to review UV index, wind direction, visibility, and sunrise/sunset (multiple times/week, high) *(F6)*
3. **Read the temperature trend chart** — interprets the Recharts AreaChart to understand the week's temperature arc before committing to weekend plans (weekly, high) *(F3)*
4. **Confirm sunrise/sunset times** — checks local sunrise/sunset in the Details panel to plan activity start times (weekly, medium) *(F6)*
5. **Identify the low-UV window** — reviews UV index value to choose the right time of day for sun-exposed garden work (weekly, medium) *(F6)*

**Success Criteria:**
- All 7 days of daily forecast visible without scrolling past a paywall or "see more" interaction
- Secondary Details panel accessible via a single tap/click on the USWDS accordion trigger — expanded/collapsed state communicated via `aria-expanded`
- Every weather condition row and card shows **both** an icon and a text label — icon-only display never occurs (WCAG 1.4.1)
- Temperature trend AreaChart renders at all viewport widths (375px–1280px+) without overflow
- USWDS accordion trigger meets 44px minimum touch target and responds to Enter/Space keyboard input
- Sunrise/sunset times reflect the searched city's local timezone — not the user's browser timezone
- Layout reflows correctly at 125% browser zoom with USWDS responsive grid breakpoints maintaining correct column structure
- Details panel defaults to collapsed on page load — secondary metrics present but non-intrusive

---

## 5. Persona Relationships

| Interaction | From | To | Nature |
|---|---|---|---|
| Shares location search entry point | PER-01 (Casual Checker) | PER-02 (Commuter) | Both use F0 city search as their primary entry; PER-02 also uses keyboard autocomplete navigation that PER-01 does not |
| Hourly → Daily progression | PER-02 (Commuter) | PER-03 (Outdoor Enthusiast) | Commuter is satisfied by 24-hour view; Outdoor Enthusiast extends that need to 7 days and secondary metrics |
| Accessibility baseline overlap | PER-01 | PER-03 | PER-01 benefits from USWDS legibility and touch targets; PER-03 requires icon+text pairings and zoom-safe layouts — both are served by the same USWDS compliance baseline |
| Progressive disclosure design tension | PER-01 (Casual Checker) | PER-03 (Outdoor Enthusiast) | PER-01 needs secondary data hidden (avoid clutter); PER-03 needs secondary data accessible (Details panel). USWDS accordion resolves the tension: data is present but collapsed by default |

**Summary:** The three personas exist on a spectrum of information depth, not separate products. PER-01 needs the hero section only. PER-02 needs the hero section plus the hourly row. PER-03 needs the hero section, the 7-day forecast, the temperature chart, and the Details panel. Each persona's needs are additive — satisfying PER-03 without degrading PER-01's experience is the core UX design challenge, solved by progressive disclosure and USWDS component hierarchy.

---

## 6. Feature-Persona Matrix

**Key:** `P` = Primary (persona is the target driver for this feature) · `S` = Secondary (persona benefits from this feature but is not its primary driver) · `—` = Minimal / not relevant

| Feature | ID | Priority | PER-01 Casual Checker | PER-02 Commuter | PER-03 Outdoor Enthusiast |
|---|---|---|---|---|---|
| Location Search & Detection | F0 | P0 | P | P | S |
| Current Conditions Display | F1 | P0 | P | P | S |
| Hourly Forecast | F2 | P1 | S | P | S |
| 7-Day Daily Forecast | F3 | P0 | — | S | P |
| Weather Icons & Visual Indicators | F4 | P0 | S | S | P |
| Responsive Layout — Desktop & Mobile | F5 | P0 | P | P | P |
| Secondary Weather Details Panel | F6 | P1 | — | — | P |
| Data Freshness & Stale State Handling | F7 | P1 | S | P | S |
| Accessibility (WCAG AA Compliance) | F8 | P1 | S | P | P |
| Attribution & Deployment | F9 | P0 | S | S | S |

### Matrix Notes

- **F0 (Location Search):** Primary for PER-01 and PER-02 who search quickly by city name; Secondary for PER-03 who also uses it but spends more time in forecast views than in search. PER-02 additionally drives the keyboard-navigable autocomplete requirement (USWDS combo-box pattern, arrow key navigation).
- **F1 (Current Conditions):** Primary for both PER-01 (it is her entire use case) and PER-02 (entry context before the hourly row). Secondary for PER-03 who always progresses to the 7-day view.
- **F2 (Hourly Forecast):** Exclusively primary for PER-02. PER-01 occasionally scans it; PER-03 uses it marginally as a same-day check before a morning ride.
- **F3 (7-Day Forecast):** PER-03's core deliverable. PER-02 checks it occasionally for weekend planning. PER-01 rarely scrolls past the hero section.
- **F4 (Icons & Visual Indicators):** Primary for PER-03 due to color vision accessibility requirement (icon + text label mandatory). All three personas benefit from condition-aware backgrounds, but PER-03's deuteranomaly makes this a functional need, not an aesthetic one.
- **F5 (Responsive Layout):** Primary for all three — PER-01 on 375px mobile, PER-02 on both mobile and desktop, PER-03 at 125% zoom on desktop. USWDS responsive grid breakpoints (`mobile-lg`, `tablet`, `desktop`) must serve all three profiles.
- **F6 (Details Panel):** Exclusively primary for PER-03. Collapsed by default specifically to protect PER-01's experience. USWDS accordion keyboard behavior also supports PER-02's desktop keyboard navigation style.
- **F7 (Data Freshness):** Primary for PER-02, who makes time-sensitive commute decisions and must trust the data timestamp before acting on it. Secondary for PER-01 and PER-03.
- **F8 (Accessibility):** Primary for PER-02 (keyboard navigation on desktop) and PER-03 (color vision deficiency, browser zoom). Secondary for PER-01, who benefits from USWDS touch targets and legibility without requiring assistive technology.
- **F9 (Attribution & Deployment):** Secondary / background for all personas — HTTPS enables geolocation (F0); Open-Meteo attribution is a license requirement, not a user feature.

---

*PERSONAS version 1.0 — generated 2026-05-01*
*Sources: PRD-SimpleWeatherApp.md v1.1, .planning/PROJECT.md*
*USWDS integration: Accessibility touchpoints (keyboard navigation, icon+text pairings, touch targets, focus indicators, zoom-safe layouts, progressive disclosure) reflected per-persona based on USWDS component requirements*
*Next documents: JTBD (Jobs-to-be-Done), UserJourneys, UserStories*
