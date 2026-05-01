# Story Map Document
## Simple Weather App

| Field | Value |
|---|---|
| **Product Name** | Simple Weather App |
| **Version** | 1.0 |
| **Date** | 2026-05-01 |
| **Status** | Active |
| **Related Personas** | PERSONAS-SimpleWeatherApp.md |
| **Related JTBD** | JTBD-SimpleWeatherApp.md |
| **Related Journeys** | JOURNEYS-SimpleWeatherApp.md |
| **Related UserStories** | UserStories-SimpleWeatherApp.md |
| **Related PRD** | PRD-SimpleWeatherApp.md v1.1 |

---

## Table of Contents

1. [Overview](#1-overview)
2. [Story Map Matrix](#2-story-map-matrix)
3. [NaC Derivation Table](#3-nac-derivation-table)
4. [Release Planning](#4-release-planning)
5. [Coverage Analysis](#5-coverage-analysis)
6. [NaC-to-Acceptance Criteria Mapping](#6-nac-to-acceptance-criteria-mapping)

---

## 1. Overview

This story map organizes all 32 user stories across journey stages (X-axis) and detail levels (Y-axis). The X-axis follows the canonical user journey: **Find Location → Read Current Data → Explore Forecast → Access Details → Trust & Verify**. Each story is annotated with a Natural Acceptance Criteria (NaC) derived from the intersection of a JTBD outcome and a journey stage touchpoint.

**NaC Concept:** NaC are not invented — they are derived from the intersection of:
- A specific JTBD outcome (the "what matters")
- A journey stage (the "when/where")
- A user story (the "what is built")

**Release structure:**
- **R1 (MVP):** P0 stories that enable JRN-01.1 (Maya's core check) end-to-end
- **R2 (Commuter):** P1 stories that enable JRN-02.1 (David's hourly scan) end-to-end
- **R3 (Enthusiast):** P1 stories that enable JRN-03.1 and JRN-03.2 end-to-end

---

## 2. Story Map Matrix

### Journey Stage Columns

| # | Stage | Description | Primary Personas |
|---|---|---|---|
| 1 | Find Location | Search for a city, GPS opt-in, recent chips, error states | PER-01, PER-02 |
| 2 | Read Current Data | Hero section — temp, condition, high/low, precip, unit toggle | PER-01, PER-02 |
| 3 | Explore Forecast | Hourly row, 7-day list, temperature trend chart | PER-02, PER-03 |
| 4 | Access Details | Secondary metrics panel (UV, wind, sunrise/sunset) | PER-03 |
| 5 | Trust & Verify | Data freshness, offline/error handling, attribution | All |
| 6 | Cross-Cutting | Accessibility (keyboard, focus, screen reader, reduced motion), responsive layout, deployment | All |

---

### Story Map Matrix Table

| Persona | Journey Stage | Activity | Epic | Story ID | Story Title | NaC | Release |
|---|---|---|---|---|---|---|---|
| PER-01 | Find Location | City name autocomplete search | Epic 0 (F0) | US-0.1 | City Name Search with Autocomplete | JTBD-01.1: Temp visible in < 3s → City resolved to lat/lon before weather fetch begins | R1 |
| PER-02 | Find Location | Keyboard navigation in autocomplete | Epic 0 (F0) | US-0.2 | Keyboard Navigation in Autocomplete | JTBD-02.2: Full keyboard nav → Arrow/Enter/Escape work in suggestion listbox | R1 |
| PER-01 | Find Location | GPS opt-in button | Epic 0 (F0) | US-0.3 | GPS Geolocation (Opt-In) | JTBD-01.1: Zero friction location → GPS denial never shows error or blank | R1 |
| PER-01 | Find Location | Recent location quick-select | Epic 0 (F0) | US-0.4 | Recent Locations Quick-Select | JTBD-01.1: Zero-re-type repeat visits → Previously searched city loadable in one tap | R2 |
| PER-01 | Find Location | City not found error | Epic 0 (F0) | US-0.5 | City Not Found Error State | JTBD-01.3: Never blank screen → usa-alert--warning shown on zero geocoding results | R1 |
| PER-01 | Read Current Data | Temperature + condition display | Epic 1 (F1) | US-1.1 | View Current Temperature and Condition | JTBD-01.1: Umbrella answer in < 3s → Integer temp + condition + icon+label above fold on 375px | R1 |
| PER-01 | Read Current Data | Supporting data (feels-like, H/L, precip, humidity, wind) | Epic 1 (F1) | US-1.2 | View Supporting Current Data | JTBD-01.1: All outfit decision data in one glance → Feels-like, H/L, precip% all in hero | R1 |
| PER-01 | Read Current Data | Unit toggle | Epic 1 (F1) | US-1.3 | Toggle Temperature Units (°C / °F) | JTBD-01.2: Toggle visible in < 10s → usa-button-group visible on main screen; persisted in localStorage | R1 |
| PER-01 | Read Current Data | Loading skeleton | Epic 1 (F1) | US-1.4 | Loading State for Current Conditions | JTBD-01.3: Never blank → Skeleton replaces blank during fetch; aria-busy="true" | R1 |
| PER-01 | Read Current Data | Error state for conditions | Epic 1 (F1) | US-1.5 | Error State for Current Conditions | JTBD-01.3: Never blank → usa-alert--error on API failure; retry button present | R1 |
| PER-02 | Explore Forecast | 24-hour scrollable row | Epic 2 (F2) | US-2.1 | View 24-Hour Forecast in Scrollable Row | JTBD-02.1: First dry window in < 15s → Hourly row on main screen; precip on every card | R2 |
| PER-02 | Explore Forecast | Correct timezone in hourly | Epic 2 (F2) | US-2.2 | Correct Timezone in Hourly Labels | JTBD-02.3: All times in city's timezone → Hourly labels use API timezone string; never browser timezone | R1 |
| PER-02 | Explore Forecast | Accessible hourly touch targets | Epic 2 (F2) | US-2.3 | Accessible Touch Targets on Hourly Cards | JTBD-02.1: Tap accuracy on moving train → All interactive elements ≥ 44×44px | R2 |
| PER-03 | Explore Forecast | 7-day daily forecast | Epic 3 (F3) | US-3.1 | View Full 7-Day Daily Forecast | JTBD-03.1: Compare days in < 30s → All 7 days visible; no paywall; precip on every row | R1 |
| PER-03 | Explore Forecast | Temperature trend chart | Epic 3 (F3) | US-3.2 | View Temperature Trend Chart | JTBD-03.1: Temperature arc at a glance → AreaChart alongside 7-day; sr-only fallback | R2 |
| PER-03 | Explore Forecast | Full WMO code icon coverage | Epic 4 (F4) | US-4.1 | Full WMO Code Icon Coverage | JTBD-03.3: No generic fallback → Every WMO code 0–99 produces specific icon + text label | R1 |
| PER-01 | Read Current Data | Day/night icon variants | Epic 4 (F4) | US-4.2 | Day/Night Icon Variants | JTBD-01.1: Correct time-of-day context → Sun never shown when is_day=0; moon shown at night | R1 |
| PER-01 | Read Current Data | Condition-aware gradient | Epic 4 (F4) | US-4.3 | Condition-Aware Background Gradient | JTBD-01.1: Visual weather context before reading data → Gradient reflects condition × time; 4.5:1 contrast | R2 |
| PER-01 | Cross-Cutting | Mobile layout (375px) | Epic 5 (F5) | US-5.1 | Usable Layout on Mobile (375px) | JTBD-01.1: Umbrella answer in < 3s on mobile → All data above fold at 375px; no overflow; 44px targets | R1 |
| PER-02 | Cross-Cutting | Tablet/desktop layout | Epic 5 (F5) | US-5.2 | Usable Layout on Tablet and Desktop | JTBD-02.2: Keyboard-first desktop use → USWDS breakpoints; correct 125% zoom; no overflow | R2 |
| PER-03 | Access Details | Expand details panel | Epic 6 (F6) | US-6.1 | Expand Details Panel to View Secondary Metrics | JTBD-03.2: UV + wind in < 5s → usa-accordion collapsed by default; expands on single tap/Enter/Space | R2 |
| PER-02 | Access Details | Accessible accordion | Epic 6 (F6) | US-6.2 | Accessible Accordion Behavior for Details Panel | JTBD-02.2: Keyboard-only details access → aria-expanded updates; hidden attr; focus preserved | R2 |
| PER-02 | Trust & Verify | Data freshness indicator | Epic 7 (F7) | US-7.1 | View Data Freshness Indicator | JTBD-02.1: Trust data before commute decision → "Updated X min ago" always visible; updates every min | R2 |
| PER-02 | Trust & Verify | Graceful offline with cache | Epic 7 (F7) | US-7.2 | Graceful Offline Handling with Cached Data | JTBD-02.1: Still useful on spotty mobile → Cached data + usa-alert--info notice when offline | R2 |
| PER-01 | Trust & Verify | No-cache offline error | Epic 7 (F7) | US-7.3 | No-Cache Offline Error State | JTBD-01.3: Never blank → usa-alert--error on offline + no cache; retry button present | R1 |
| PER-02 | Cross-Cutting | Full keyboard navigation | Epic 8 (F8) | US-8.1 | Full Keyboard Navigation | JTBD-02.2: Zero forced mouse → All elements Tab-reachable; no traps; skip nav present | R1 |
| PER-02 | Cross-Cutting | USWDS focus indicators | Epic 8 (F8) | US-8.2 | Visible USWDS Focus Indicators | JTBD-02.2: Always know keyboard position → 2px USWDS focus ring on every interactive element | R1 |
| All | Cross-Cutting | Screen reader announcements | Epic 8 (F8) | US-8.3 | Screen Reader Announcements for Weather Updates | JTBD-01.3: Trust updates communicated → aria-live regions announce data load and freshness changes | R2 |
| PER-03 | Cross-Cutting | Color-independent conditions | Epic 8 (F8) | US-8.4 | Color Independence for Weather Conditions | JTBD-03.3: Conditions readable without color → WCAG 1.4.1; icon+text always paired; axe-core passes | R1 |
| All | Cross-Cutting | Reduced motion support | Epic 8 (F8) | US-8.5 | Reduced Motion Support | JTBD-01.3: No accessibility regressions → prefers-reduced-motion disables all transitions; Recharts animations off | R2 |
| All | Trust & Verify | Open-Meteo attribution footer | Epic 9 (F9) | US-9.1 | Open-Meteo Attribution in Footer | License compliance → usa-footer present; CC BY 4.0 link on every page load | R1 |
| All | Trust & Verify | Vercel HTTPS deployment | Epic 9 (F9) | US-9.2 | Vercel HTTPS Deployment with Auto-Deploy | Geolocation requires HTTPS → App live at public HTTPS URL; auto-deploy from main branch | R1 |

---

## 3. NaC Derivation Table

Full traceability chain: JTBD outcome → Journey stage → NaC → Story

| JTBD-ID | JTBD Outcome | Journey Stage (JRN) | NaC (Derived) | Story |
|---|---|---|---|---|
| JTBD-01.1 | Get umbrella answer in < 3s on mobile | JRN-01.1: Open app → Read data | City resolved and weather visible in < 2s on mobile 4G; integer temp + precip% above fold at 375px | US-0.1, US-1.1, US-1.2, US-5.1 |
| JTBD-01.1 | No friction location detection | JRN-01.1: Search → Select | GPS denial never produces error or blank screen; city search always functional | US-0.3 |
| JTBD-01.2 | Unit toggle visible on first visit | JRN-01.1: Read data → Decide | usa-button-group visible on main screen without navigation; preference persisted | US-1.3 |
| JTBD-01.3 | Never show blank screen | JRN-01.2: Open app → See error | Skeleton shown during fetch; usa-alert--error within 8s of failure; cached data shown offline | US-1.4, US-1.5, US-7.3 |
| JTBD-01.3 | Trust app to communicate failure | JRN-01.2: Retry | usa-alert--error with retry button; second failure shows "check your connection" | US-1.5, US-7.3 |
| JTBD-02.1 | First dry window in < 15s | JRN-02.1: Find hourly row → Read cards | Hourly row on main screen; precipitation on every card; 24 cards covering next 24 hours | US-2.1, US-2.3 |
| JTBD-02.1 | Trust data before commute decision | JRN-02.1: Decide & act | "Updated X min ago" always visible; TanStack Query staleTime 10 min | US-7.1, US-7.2 |
| JTBD-02.2 | Full keyboard navigation | JRN-02.2: Navigate → Search → Select → Expand | Arrow/Enter/Escape in autocomplete; USWDS 2px focus ring; logical Tab order; accordion keyboard support | US-0.2, US-6.2, US-8.1, US-8.2 |
| JTBD-02.3 | Hourly times in city's timezone | JRN-02.1: Read hourly cards | All hourly labels derived from API-returned timezone string; never browser timezone | US-2.2 |
| JTBD-02.3 | Sunrise/sunset in city's timezone | JRN-03.2: Read secondary data | Sunrise/sunset in Details panel reflect searched city's local time | US-6.1 |
| JTBD-03.1 | All 7 days visible, no paywall | JRN-03.1: Compare 7 days | 7 forecast days in single vertical list; no pagination, paywall, or "see more" | US-3.1 |
| JTBD-03.1 | Temperature arc at a glance | JRN-03.1: Read temperature trend | Recharts AreaChart alongside 7-day list; sr-only fallback table; renders at all viewports | US-3.2 |
| JTBD-03.2 | UV + wind accessible in < 5s | JRN-03.2: Find panel → Expand | usa-accordion collapsed by default; single tap/Enter/Space expands; UV, wind, sunrise, sunset all visible | US-6.1, US-6.2 |
| JTBD-03.3 | Conditions readable without color | JRN-03.1: Compare 7 days; JRN-03.2: Read data | Every condition icon paired with text label; no color-only condition indicators; WCAG 1.4.1 | US-4.1, US-8.4 |
| All | Correct time-of-day icon variants | JRN-01.1: Read data; JRN-02.1: Read hourly cards | Sun never shown when is_day=0 (from API, not browser); moon shown for night conditions | US-4.2 |
| All | Gradient communicates weather context | JRN-01.1: Read data | Gradient shifts by condition × time-of-day; all text achieves 4.5:1 contrast on any gradient | US-4.3 |
| All | Accessibility for all users | All journeys | WCAG 2.2 AA; prefers-reduced-motion; aria-live announcements; icon+text pairings | US-8.3, US-8.4, US-8.5 |
| All | Attribution + HTTPS | All journeys | usa-footer with CC BY 4.0 link; Vercel HTTPS; auto-deploy | US-9.1, US-9.2 |

---

## 4. Release Planning

### R1: MVP — Core Weather Check (JRN-01.1 end-to-end)

**Theme:** Any user can open the app, find their city, and get a complete current weather answer in under 2 seconds, on any device, with no blank screens and no accessibility violations.

**Personas served:** PER-01 (fully), PER-02 (partially), PER-03 (partially)
**JTBD addressed:** JTBD-01.1, JTBD-01.2, JTBD-01.3, JTBD-02.3 (timezone), JTBD-03.3 (color independence)

**R1 Stories:**

| Story | Title | Feature |
|---|---|---|
| US-0.1 | City Name Search with Autocomplete | F0 |
| US-0.2 | Keyboard Navigation in Autocomplete | F0 |
| US-0.3 | GPS Geolocation (Opt-In) | F0 |
| US-0.5 | City Not Found Error State | F0 |
| US-1.1 | View Current Temperature and Condition | F1 |
| US-1.2 | View Supporting Current Data | F1 |
| US-1.3 | Toggle Temperature Units (°C / °F) | F1 |
| US-1.4 | Loading State for Current Conditions | F1 |
| US-1.5 | Error State for Current Conditions | F1 |
| US-2.2 | Correct Timezone in Hourly Labels | F2 |
| US-3.1 | View Full 7-Day Daily Forecast | F3 |
| US-4.1 | Full WMO Code Icon Coverage | F4 |
| US-4.2 | Day/Night Icon Variants | F4 |
| US-5.1 | Usable Layout on Mobile (375px) | F5 |
| US-7.3 | No-Cache Offline Error State | F7 |
| US-8.1 | Full Keyboard Navigation | F8 |
| US-8.2 | Visible USWDS Focus Indicators | F8 |
| US-8.4 | Color Independence for Weather Conditions | F8 |
| US-9.1 | Open-Meteo Attribution in Footer | F9 |
| US-9.2 | Vercel HTTPS Deployment with Auto-Deploy | F9 |

**R1 delivers:** JRN-01.1 (Maya's morning umbrella check) runs end-to-end. JRN-01.2 (Maya's error state) runs end-to-end. Timezone correctness (JRN-02.1, JRN-03.1) established from day 1. Color independence (JTBD-03.3) built in from day 1.

---

### R2: Commuter & Enthusiast Depth

**Theme:** Commuters get hourly precipitation scanning; outdoor enthusiasts get temperature trend charts and secondary details; all users get freshness indicators and offline resilience.

**Personas served:** PER-02 (fully), PER-03 (fully), PER-01 (enhanced)
**JTBD addressed:** JTBD-02.1, JTBD-02.2 (fully), JTBD-03.1 (chart), JTBD-03.2 (details panel)

**R2 Stories:**

| Story | Title | Feature |
|---|---|---|
| US-0.4 | Recent Locations Quick-Select | F0 |
| US-2.1 | View 24-Hour Forecast in Scrollable Row | F2 |
| US-2.3 | Accessible Touch Targets on Hourly Cards | F2 |
| US-3.2 | View Temperature Trend Chart | F3 |
| US-4.3 | Condition-Aware Background Gradient | F4 |
| US-5.2 | Usable Layout on Tablet and Desktop | F5 |
| US-6.1 | Expand Details Panel to View Secondary Metrics | F6 |
| US-6.2 | Accessible Accordion Behavior for Details Panel | F6 |
| US-7.1 | View Data Freshness Indicator | F7 |
| US-7.2 | Graceful Offline Handling with Cached Data | F7 |
| US-8.3 | Screen Reader Announcements for Weather Updates | F8 |
| US-8.5 | Reduced Motion Support | F8 |

**R2 delivers:** JRN-02.1 (David's commute scan), JRN-02.2 (David's keyboard session), JRN-03.1 (Priya's ride planning), and JRN-03.2 (Priya's garden check) all run end-to-end.

---

## 5. Coverage Analysis

### Persona Coverage Per Release

| Persona | R1 | R2 |
|---|---|---|
| PER-01 (Maya Torres) | ✓ Full journey (JRN-01.1, JRN-01.2) | Enhanced (recent chips, gradient, reduced motion) |
| PER-02 (David Park) | Partial (search keyboard, timezone, basic keyboard nav) | ✓ Full journey (JRN-02.1, JRN-02.2) |
| PER-03 (Priya Nair) | Partial (7-day list, icon+text, color independence) | ✓ Full journey (JRN-03.1, JRN-03.2) |

### JTBD Coverage Per Release

| JTBD | R1 | R2 |
|---|---|---|
| JTBD-01.1 (umbrella answer in < 3s) | ✓ Fully addressed | — |
| JTBD-01.2 (unit toggle visible) | ✓ Fully addressed | — |
| JTBD-01.3 (never blank screen) | ✓ Fully addressed | Enhanced (screen reader announcements) |
| JTBD-02.1 (commute hourly scan) | Partial (timezone correct) | ✓ Fully addressed |
| JTBD-02.2 (full keyboard nav) | Partial (search keyboard, tab order) | ✓ Fully addressed |
| JTBD-02.3 (timezone correctness) | ✓ Fully addressed | — |
| JTBD-03.1 (7-day planning) | Partial (7-day list) | ✓ Chart added |
| JTBD-03.2 (secondary metrics) | — | ✓ Fully addressed |
| JTBD-03.3 (no color dependency) | ✓ Fully addressed | — |

### Gap Analysis

**Journey stages with no mapped stories:** None — all 6 journey stage columns have story coverage.

**JTBD outcomes with no derived NaC:** None — all 9 JTBD have at least one NaC derivation.

**Orphan stories (not mapped to journey stage):** None — all 32 stories are placed in the matrix.

**Personas not served by R1:** PER-02 and PER-03 are partially served in R1 but get full journey coverage in R2.

---

## 6. NaC-to-Acceptance Criteria Mapping

Verifies that each NaC aligns with at least one acceptance criterion in the corresponding UserStory.

| NaC | Story | Aligned Acceptance Criterion |
|---|---|---|
| City resolved and weather visible in < 2s on mobile 4G | US-0.1, US-1.1 | US-1.1: "Temperature and condition visible above fold on 375px without scrolling" |
| GPS denial never produces error or blank | US-0.3 | US-0.3: "If geolocation denied, search input remains functional with no error state shown" |
| usa-button-group visible on main screen; persisted | US-1.3 | US-1.3: "Unit toggle visible on main screen without any navigation"; "preference persisted in localStorage" |
| Skeleton during fetch; never blank | US-1.4 | US-1.4: "Skeleton loading state shown — no blank screen during load" |
| usa-alert--error on API failure; retry button | US-1.5 | US-1.5: "USWDS usa-alert--error shown on 4xx/5xx"; "retry button visible" |
| Hourly row on main screen; precip on every card | US-2.1 | US-2.1: "Row not behind tab, paywall, or 'see more'"; "Precipitation probability shown on every card" |
| All hourly labels in city's timezone | US-2.2 | US-2.2: "All hourly time labels derived from API-returned timezone field using Intl.DateTimeFormat" |
| 7 forecast days visible without paywall | US-3.1 | US-3.1: "All 7 forecast days visible in single list — no pagination, paywall, or 'see more'" |
| AreaChart alongside 7-day; sr-only fallback | US-3.2 | US-3.2: "AreaChart rendered adjacent to daily list"; "sr-only data table or aria-label provided" |
| Every WMO code produces specific icon + text label | US-4.1 | US-4.1: "Icon set covers full WMO range 0–99"; "Every WMO code produces specific named icon and text label" |
| Sun never when is_day=0; moon at night | US-4.2 | US-4.2: "Sun icon never appears when is_day=0"; "Moon/stars never appears when is_day=1" |
| Gradient; 4.5:1 contrast on all backgrounds | US-4.3 | US-4.3: "All text on any gradient achieves minimum 4.5:1 contrast ratio (WCAG 1.4.3)" |
| usa-accordion collapsed; single tap expands; all secondary metrics visible | US-6.1 | US-6.1: "Panel collapsed by default on page load"; "Single tap/click expands"; "UV, wind, visibility, humidity, sunrise, sunset shown" |
| aria-expanded updates; hidden attr; focus preserved | US-6.2 | US-6.2: "aria-expanded='false' when collapsed, 'true' when expanded"; "panel has hidden attribute when collapsed" |
| "Updated X min ago" visible; updates every min | US-7.1 | US-7.1: "Freshness timestamp visible at all times when data loaded"; "updates every minute" |
| Cached data + usa-alert--info when offline | US-7.2 | US-7.2: "Cached data shown with usa-alert--info: 'Showing cached data from X minutes ago'" |
| All elements Tab-reachable; no traps; skip nav | US-8.1 | US-8.1: "All interactive elements reachable via Tab"; "No keyboard traps"; "Skip navigation link is first focusable element" |
| 2px USWDS focus ring on every interactive element | US-8.2 | US-8.2: "USWDS-standard focus indicator on all focusable elements via :focus-visible" |
| aria-live announces data load and freshness | US-8.3 | US-8.3: "aria-live='polite' region announces 'Weather loaded for [City]' on new location" |
| WCAG 1.4.1; icon+text always paired; axe-core passes | US-8.4 | US-8.4: "Every weather condition icon always accompanied by text label"; "axe-core automated audit passes" |
| prefers-reduced-motion disables transitions | US-8.5 | US-8.5: "All CSS transitions check prefers-reduced-motion: reduce"; "Recharts animations disabled when active" |
| usa-footer with CC BY 4.0 link on every page | US-9.1 | US-9.1: "usa-footer present on every page"; "Open-Meteo attribution link satisfies CC BY 4.0" |
| App live at public HTTPS URL; auto-deploy | US-9.2 | US-9.2: "Deployed to Vercel over HTTPS"; "Push to main triggers auto-deploy" |

**NaC alignment:** ✓ All 23 NaC entries aligned to at least one specific acceptance criterion in the corresponding UserStory. No NaC is unsupported.

---

*STORY-MAP version 1.0 — generated 2026-05-01*
*Sources: PERSONAS-SimpleWeatherApp.md, JTBD-SimpleWeatherApp.md, JOURNEYS-SimpleWeatherApp.md, UserStories-SimpleWeatherApp.md, PRD-SimpleWeatherApp.md v1.1*
*Next documents: UX-Mockup, Validator, RTM*
