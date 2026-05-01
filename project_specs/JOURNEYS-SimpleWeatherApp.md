# User Journeys Document
## Simple Weather App

| Field | Value |
|---|---|
| **Product Name** | Simple Weather App |
| **Version** | 1.0 |
| **Date** | 2026-05-01 |
| **Status** | Active |
| **Related Personas** | PERSONAS-SimpleWeatherApp.md |
| **Related JTBD** | JTBD-SimpleWeatherApp.md |
| **Related PRD** | PRD-SimpleWeatherApp.md v1.1 |

---

## Table of Contents

1. [Journey Index](#1-journey-index)
2. [JRN-01.1: Maya's Morning Umbrella Check](#2-jrn-011-mayas-morning-umbrella-check)
3. [JRN-01.2: Maya Encounters an Error State](#3-jrn-012-maya-encounters-an-error-state)
4. [JRN-02.1: David's Pre-Commute Hourly Scan](#4-jrn-021-davids-pre-commute-hourly-scan)
5. [JRN-02.2: David Navigates by Keyboard on Desktop](#5-jrn-022-david-navigates-by-keyboard-on-desktop)
6. [JRN-03.1: Priya Plans a Weekend Ride](#6-jrn-031-priya-plans-a-weekend-ride)
7. [JRN-03.2: Priya Checks Secondary Metrics for Garden Work](#7-jrn-032-priya-checks-secondary-metrics-for-garden-work)
8. [Cross-Journey Patterns](#8-cross-journey-patterns)
9. [Journey-to-JTBD Traceability](#9-journey-to-jtbd-traceability)

---

## 1. Journey Index

| JRN-ID | Persona | Scenario | Key JTBD | Stages |
|---|---|---|---|---|
| JRN-01.1 | PER-01 Maya Torres | Morning umbrella/outfit decision on mobile | JTBD-01.1, JTBD-01.2 | 5 |
| JRN-01.2 | PER-01 Maya Torres | API error encountered mid-session | JTBD-01.3 | 4 |
| JRN-02.1 | PER-02 David Park | Pre-commute hourly precipitation scan | JTBD-02.1, JTBD-02.3 | 5 |
| JRN-02.2 | PER-02 David Park | Full keyboard-only weather check on desktop | JTBD-02.2 | 5 |
| JRN-03.1 | PER-03 Priya Nair | 7-day forecast review to plan weekend cycling ride | JTBD-03.1, JTBD-03.3 | 6 |
| JRN-03.2 | PER-03 Priya Nair | Secondary metrics check before garden work | JTBD-03.2, JTBD-03.3 | 5 |

---

## 2. JRN-01.1: Maya's Morning Umbrella Check

**Persona:** PER-01 (Maya Torres — Casual Checker)

**Scenario:** It's 7:15 AM. Maya is in her kitchen making coffee, phone in one hand. She opens the app to decide what to wear and whether to pack an umbrella before her 8 AM departure. She has about 30 seconds to get an answer before she needs to start getting ready.

**Related Jobs:** JTBD-01.1 (get umbrella answer instantly), JTBD-01.2 (switch temperature units)

---

### Journey Stages

| Stage | Action | Touchpoint | Thinking | Feeling | Pain Point | Opportunity |
|---|---|---|---|---|---|---|
| Open app | Taps app icon on phone home screen | App launch / Vite static bundle | "Please load fast, I don't have time" | Neutral, slightly impatient | Every second waiting is a second not getting ready | Sub-2-second load via Vite static bundle + HTTPS CDN; skeleton visible immediately |
| Search | Taps the `usa-search` input, types "Austin" | F0 — Location search | "Is my city already saved? Let me check the chips" | Slightly hopeful | Sometimes misremembers if she typed it before | Recent location chip for Austin appears immediately if previously searched |
| Select | Taps the autocomplete suggestion "Austin, Texas, US" | F0 — Autocomplete suggestion list | "Yes, that's the one" | Relieved, ready | If 5 suggestions appear, she might not scan all of them | Bold the best match or put exact city match first in suggestion list |
| Read data | Scans hero section above the fold | F1 — Current conditions hero | "72°F, feels like 70°F, 30% chance of rain. No umbrella." | Satisfied, confident | Nothing in view if hero doesn't load above fold at 375px | Large integer temperature + condition label + precipitation % always above fold at 375px |
| Decide & close | Puts phone down, gets ready | App backgrounded | "Got what I needed, done in 20 seconds" | Delighted | If unit is wrong (°C instead of °F), she has to toggle now | Unit preference persisted in `localStorage`; no re-toggling on repeat visits |

---

### Key Moments

- **Decision Point — Read data stage:** If precipitation probability is hard to find or not above the fold, Maya will miss it and make the wrong outfit decision. This is the moment the app either wins or loses her.
- **Delight Opportunity — Select stage:** If a recent location chip for Austin is already visible, she never has to type. Zero-effort repeat checks are the most frequent case.
- **Risk of Abandonment — Open app stage:** If the app shows a spinner for more than 2 seconds, Maya closes it and uses her phone's built-in widget instead.

### Success Outcome

Maya reads current temperature, condition, and precipitation probability within 3 seconds of opening the app on mobile, without scrolling. (JTBD-01.1 success measure)

### Feature Touchpoints

| Stage | Features |
|---|---|
| Open app | F9 (Deployment — HTTPS, CDN, static bundle) |
| Search | F0 (Location Search — usa-search, recent chips) |
| Select | F0 (Autocomplete, usa-tag chips) |
| Read data | F1 (Current Conditions — temp, condition, precipitation), F4 (Icons, gradient background) |
| Decide & close | F1 (Unit toggle — localStorage persistence) |

---

## 3. JRN-01.2: Maya Encounters an Error State

**Persona:** PER-01 (Maya Torres — Casual Checker)

**Scenario:** Maya opens the app on a morning when she has spotty cellular coverage near her building's elevator. The API call fails mid-load. She has no idea why the weather isn't showing up and starts to question whether the app is broken.

**Related Jobs:** JTBD-01.3 (trust the app to never show a blank screen)

---

### Journey Stages

| Stage | Action | Touchpoint | Thinking | Feeling | Pain Point | Opportunity |
|---|---|---|---|---|---|---|
| Open app | Taps app icon, sees skeleton loading state | F1 — Skeleton hero | "Loading, OK, fine, just be quick" | Neutral, waiting | Skeleton could look exactly like the blank screen she fears | Skeleton must be visually distinct from a blank screen — shimmer animation or "Loading weather..." label |
| Wait | Watches skeleton persist for 5+ seconds | F1 — Skeleton + F7 — Freshness | "Something's wrong. Why isn't it loading?" | Anxious, frustrated | No signal that something failed — just perpetual skeleton | After 8 seconds, auto-transition from skeleton to error alert — never perpetual spinner |
| See error | Error alert appears: "Weather data unavailable. Please try again shortly." | F7 — usa-alert--error | "OK so it's broken, not me. I can retry." | Annoyed but not confused | Alert text could feel alarming if too formal | Friendly, plain-language error copy with a visible "Try again" button |
| Retry | Taps "Try again" button in the alert | F7 — Retry CTA | "Let me see if my signal is better now" | Hopeful | If retry also fails, she needs to know to give up | Second failure shows the alert again + "Check your connection" guidance; no infinite retry loop |

---

### Key Moments

- **Risk of Abandonment — Wait stage:** If Maya sees a perpetual loading state with no feedback, she interprets it as "this app is broken" and switches to her phone widget. This is the highest risk moment.
- **Trust Recovery — See error stage:** A clear, friendly error message with a retry option converts a frustration moment into a trust moment. "The app knows it failed and told me" is better than silence.

### Success Outcome

Zero blank-screen occurrences. Error is communicated with a USWDS `usa-alert--error` component within 8 seconds of a failed API call. (JTBD-01.3 success measure)

### Feature Touchpoints

| Stage | Features |
|---|---|
| Open app | F1 (Skeleton loading state) |
| Wait | F1 (Skeleton), F7 (Stale state handling — timeout) |
| See error | F7 (usa-alert--error), F8 (aria-live="assertive" announcement) |
| Retry | F7 (Retry CTA, TanStack Query retry logic) |

---

## 4. JRN-02.1: David's Pre-Commute Hourly Scan

**Persona:** PER-02 (David Park — Commuter)

**Scenario:** It's 7:45 AM and David is at his kitchen table with his phone, planning his bike commute. He knows there's a chance of rain this morning and wants to see whether it's better to bike at 8 AM or wait until 9 AM when conditions might improve. He's checking the hourly forecast specifically for precipitation windows.

**Related Jobs:** JTBD-02.1 (plan commute using hourly precipitation), JTBD-02.3 (trust hourly times in city's timezone)

---

### Journey Stages

| Stage | Action | Touchpoint | Thinking | Feeling | Pain Point | Opportunity |
|---|---|---|---|---|---|---|
| Open app | Taps app, sees Austin (saved from yesterday) already loaded | F0 — Recent location chip, F1 — Hero | "Good, Austin is already there" | Efficient, ready | If city needs to be re-searched every time, that's extra friction | Recent location chips eliminate re-typing for repeat-city users |
| Scan hero | Glances at current conditions — 62°F, cloudy, 55% precip | F1 — Current conditions hero | "55% right now. That's borderline. Let me check the hourly." | Slightly concerned | Hero alone isn't enough for timing decisions | Clear visual link from hero to hourly row; hourly is immediately below |
| Find hourly row | Scrolls down slightly (or it's visible without scroll on desktop) | F2 — Hourly forecast row | "Where's the hourly? It better not be behind a paywall." | Scanning, slightly impatient | Other apps put the hourly behind a tab or paywall | Hourly row is a first-class main-screen component — visible on load |
| Read hourly cards | Scrolls horizontally through 8 AM, 9 AM, 10 AM cards | F2 — HourlyCard × N | "8 AM: 60%, 9 AM: 35%, 10 AM: 20%. OK, I should leave at 9." | Focused, analytical | If hourly times are in the wrong timezone, this whole analysis is wrong | timezone=auto on every API request; hourly labels derived from API timezone string |
| Decide & act | Puts phone down, adjusts departure time | App backgrounded | "Leave at 9:15. 35% is fine with my rain jacket." | Confident, decisive | If data seems stale, he won't trust the decision | Freshness indicator shows "Updated 4 minutes ago" — within acceptable window |

---

### Key Moments

- **Decision Point — Read hourly cards stage:** This is the most critical analytical moment in David's journey. Wrong timezone labels here lead to a wrong commute decision — a real, consequential error. `timezone=auto` is non-negotiable.
- **Risk of Abandonment — Find hourly row stage:** If the hourly row is behind a "Show more" button or a tab, David loses trust and switches to a different app. The hourly row must be immediately visible.
- **Delight Opportunity — Open app stage:** If Austin is pre-loaded from yesterday's session (recent chips) and the data is less than 10 minutes old (still fresh), David gets his answer without any interaction — maximum efficiency.

### Success Outcome

David identifies the first dry commute window by scanning the hourly row in under 15 seconds from app open. (JTBD-02.1 success measure)

### Feature Touchpoints

| Stage | Features |
|---|---|
| Open app | F0 (Recent location chips), F1 (Cached hero data) |
| Scan hero | F1 (Current conditions — precipitation probability) |
| Find hourly row | F2 (Hourly forecast — main screen first-class component) |
| Read hourly cards | F2 (HourlyCards with precipitation + timezone labels), F4 (Day/night icons), F7 (Freshness indicator) |
| Decide & act | F7 (Data freshness — staleTime 10 min) |

---

## 5. JRN-02.2: David Navigates by Keyboard on Desktop

**Persona:** PER-02 (David Park — Commuter)

**Scenario:** David is at his work desk checking the weather for a client city he's traveling to tomorrow. He's in the middle of writing a document and doesn't want to reach for the mouse. He expects to complete the full weather check — search, hourly scan, details panel — without leaving his keyboard.

**Related Jobs:** JTBD-02.2 (operate full app by keyboard)

---

### Journey Stages

| Stage | Action | Touchpoint | Thinking | Feeling | Pain Point | Opportunity |
|---|---|---|---|---|---|---|
| Navigate to search | Presses Tab until focus lands on the search input | F0 — usa-search, USWDS focus indicator | "I can see the focus ring. Good. I know where I am." | Efficient, in control | If there's no visible focus indicator, he loses track of his position | USWDS 2px solid focus ring with offset on every interactive element |
| Search by keyboard | Types "Chicago", watches suggestions appear | F0 — Autocomplete combo-box | "Suggestions appeared. Let me arrow down." | Focused | If arrow keys don't work in the dropdown, he's stuck — must reach for mouse | Full arrow-key navigation in suggestion list (role="listbox", role="option") |
| Select suggestion | Presses Down arrow twice, Enter to select "Chicago, Illinois, US" | F0 — Suggestion list | "Chicago selected. Data loading." | Smooth, satisfied | If Enter doesn't confirm the selection, the whole interaction is broken | Enter selects highlighted option; Escape dismisses list; both standard keyboard patterns |
| Scan forecast | Tabs through current conditions, reaches hourly row, reads precipitation | F1, F2 — Hourly row | "The hourly row is a region. I can read it. 8 AM, 15%, clear." | Analytical | Hourly scroll container must be reachable by Tab and announce as a region | role="region" aria-label="Hourly forecast" on the scroll container |
| Expand details | Tabs to Details accordion trigger, presses Enter to expand | F6 — usa-accordion | "I want to check wind speed for tomorrow's outdoor meeting." | Curious | If Enter doesn't trigger the accordion, he's stuck | USWDS accordion trigger responds to Enter and Space; aria-expanded updates |

---

### Key Moments

- **Risk of Abandonment — Search by keyboard stage:** If the autocomplete dropdown is not keyboard-navigable, David reaches for his mouse, breaks his flow, and mentally downgrades the app to "not keyboard-friendly." He will still use it but will prefer other tools.
- **Trust moment — Navigate to search stage:** Seeing the USWDS 2px focus ring immediately on page load communicates "this app was built with keyboard users in mind." This positive signal is established in the first Tab keypress.
- **Delight Opportunity — Expand details stage:** Successfully expanding the details panel by keyboard without any help from the mouse is the moment David fully trusts the app for desktop keyboard use.

### Success Outcome

David completes a full weather check session (search → read hourly → expand details) using only keyboard, with no forced mouse interaction. (JTBD-02.2 success measure)

### Feature Touchpoints

| Stage | Features |
|---|---|
| Navigate to search | F8 (Accessibility — focus indicators, skip nav, tab order) |
| Search by keyboard | F0 (Keyboard autocomplete — listbox pattern) |
| Select suggestion | F0 (Enter/Escape keyboard handling) |
| Scan forecast | F1 (Current conditions), F2 (Hourly — role="region") |
| Expand details | F6 (Details panel — usa-accordion keyboard support) |

---

## 6. JRN-03.1: Priya Plans a Weekend Ride

**Persona:** PER-03 (Priya Nair — Outdoor Enthusiast)

**Scenario:** It's Wednesday evening and Priya is deciding whether to do her 40-mile cycling route on Saturday or Sunday. She opens the app on her laptop, zoomed to 125%, and needs to compare the 7-day forecast to identify the better day — looking at precipitation probability, temperature range, and the temperature trend chart.

**Related Jobs:** JTBD-03.1 (choose best day using 7-day forecast), JTBD-03.3 (distinguish conditions without color)

---

### Journey Stages

| Stage | Action | Touchpoint | Thinking | Feeling | Pain Point | Opportunity |
|---|---|---|---|---|---|---|
| Open app | Navigates to app in browser at 125% zoom | App shell — F5 responsive layout | "I hope it doesn't overflow at my zoom level like that other app did." | Cautiously optimistic | Many apps break at 125% zoom — text truncates, cards overflow | USWDS grid with USWDS breakpoints reflows correctly at 125%; tested explicitly |
| Search | Types "Portland, OR", selects from autocomplete | F0 — Location search | "I know this city — just confirming it picks the right Portland." | Efficient | Multiple Portlands in the US could cause ambiguity | Suggestion shows city + state/region + country; "Portland, Oregon, US" is unambiguous |
| Scan current | Glances at hero — 58°F, light rain, 65% precip | F1 — Current conditions | "Okay, rainy today. What about the weekend?" | Not concerned about today | Hero takes up a lot of vertical space — she wants to get to the 7-day view quickly | Compact hero at desktop breakpoint; 7-day section visible with minimal scroll |
| Compare 7 days | Scrolls to 7-day forecast section, reads Saturday and Sunday rows | F3 — Daily forecast | "Saturday: 55°F/42°F, 20% rain. Sunday: 62°F/48°F, 45% rain. Saturday wins." | Analytical, decisive | If condition icons are color-coded without text labels, she might misread rainy vs. drizzly | Every daily row shows condition text label alongside icon — WCAG 1.4.1 compliance |
| Read temperature trend | Glances at Recharts AreaChart below the daily list | F3 — TemperatureTrendChart | "Temperatures peak Saturday afternoon. Perfect for a ride." | Satisfied, confident | Chart must render correctly at 125% zoom and 1280px desktop width | AreaChart constrained within its container; tested at all viewport widths |
| Decide | Puts "Saturday cycling" in her calendar app | App backgrounded | "Got my answer. Saturday, leave by 10 AM." | Delighted | None at this stage | "Today" label on day 0 makes the day offsets clear; no confusion about which day is Saturday |

---

### Key Moments

- **Risk of Abandonment — Open app stage:** If the layout overflows or truncates at 125% zoom, Priya closes the app and uses her usual cluttered alternative. The first visual impression must demonstrate zoom-safe design.
- **Decision Point — Compare 7 days stage:** Saturday vs. Sunday comparison is the core analytical task. The icon + text label pairing is critical here — Priya relies on text to distinguish "20% rain" (Light Drizzle) from "45% rain" (Moderate Rain). Icon-only display would undermine this.
- **Delight Opportunity — Read temperature trend stage:** The AreaChart is the unexpected payoff. Priya didn't expect a chart; seeing the temperature arc confirms Saturday's peak is the right choice. Exceeds expectations.

### Success Outcome

Priya compares Saturday and Sunday conditions and identifies the better cycling day in under 30 seconds, using only the 7-day view and temperature trend chart. (JTBD-03.1 success measure)

### Feature Touchpoints

| Stage | Features |
|---|---|
| Open app | F5 (Responsive layout — 125% zoom, USWDS grid) |
| Search | F0 (Location search — city disambiguation) |
| Scan current | F1 (Current conditions hero), F4 (Gradient background — overcast state) |
| Compare 7 days | F3 (7-day daily forecast), F4 (Icons + text labels — WCAG 1.4.1) |
| Read temperature trend | F3 (Recharts AreaChart — temperature trend) |
| Decide | F1 (Today label), F3 (Day labels) |

---

## 7. JRN-03.2: Priya Checks Secondary Metrics for Garden Work

**Persona:** PER-03 (Priya Nair — Outdoor Enthusiast)

**Scenario:** It's Friday afternoon and Priya is deciding whether to transplant seedlings in her garden tomorrow morning. She needs to know the UV index (to decide whether morning sun exposure is safe for new plants) and wind direction/speed (to position a shade cloth). She opens the app, expects her recent location to be loaded, and wants to access secondary metrics quickly.

**Related Jobs:** JTBD-03.2 (access secondary metrics without cluttering main view), JTBD-03.3 (distinguish conditions without color)

---

### Journey Stages

| Stage | Action | Touchpoint | Thinking | Feeling | Pain Point | Opportunity |
|---|---|---|---|---|---|---|
| Open app | Opens app on tablet, city already loaded from last visit | F0 — Recent location, F1 — Hero | "Good, Portland is still there. UV and wind — where's that?" | Ready, scanning | She doesn't remember where the secondary data is on first return visit | Details panel is visually distinct with a clear trigger label (e.g., "Expand for UV, wind, visibility") |
| Find Details panel | Spots the collapsed USWDS accordion trigger below main sections | F6 — usa-accordion (collapsed) | "Ah, there's the Details section. Collapsed by default, good." | Mildly relieved | If details are always visible, the main view is cluttered for quick checks | Collapsed default protects casual use; Priya's enthusiast use case is served by one tap |
| Expand panel | Taps accordion trigger; panel expands smoothly | F6 — usa-accordion (expanded) | "UV 4, Wind 12 mph SSE. Perfect — no harsh UV, gentle tailwind." | Satisfied, informed | If the accordion doesn't have keyboard support, desktop users can't expand via keyboard | USWDS accordion responds to Enter/Space; aria-expanded updates on toggle |
| Read secondary data | Reads UV index (4 — moderate), wind direction (SSE), visibility (8 km), sunrise (6:18 AM) | F6 — Details panel content | "SSE wind means I should position the shade cloth on the north side. Sunrise at 6:18 — I'll start at 7." | Engaged, planning | If sunrise/sunset is in the wrong timezone, her timing plans are off | All times in Details panel use API-returned timezone (timezone=auto) |
| Close panel | Taps accordion trigger to collapse panel | F6 — usa-accordion (collapsed) | "Done, don't need that anymore." | Clean, efficient | None — collapsing is the expected behavior | Panel snaps closed without content jumping; layout stable |

---

### Key Moments

- **Discovery moment — Find Details panel stage:** Priya hasn't visited the app in 2 days. She needs to rediscover where the secondary metrics are. A clearly labeled accordion trigger with a descriptive label ("UV · Wind · Sunrise · Sunset") eliminates this friction.
- **Critical data accuracy — Read secondary data stage:** Sunrise time is used to plan literal activity start time. A timezone error here (showing browser timezone instead of Portland's) produces wrong guidance with real outdoor consequences.
- **Delight Opportunity — Expand panel stage:** The one-tap progressive disclosure — main view is clean, details appear instantly — validates the USWDS accordion choice. Priya gets depth without noise.

### Success Outcome

Priya accesses UV index and wind direction within 5 seconds of opening the Details panel, with a single tap. (JTBD-03.2 success measure)

### Feature Touchpoints

| Stage | Features |
|---|---|
| Open app | F0 (Recent location), F1 (Current conditions) |
| Find Details panel | F6 (usa-accordion — collapsed default), F5 (Responsive layout) |
| Expand panel | F6 (usa-accordion — keyboard + touch activation, aria-expanded) |
| Read secondary data | F6 (UV, wind, visibility, sunrise/sunset — timezone=auto), F8 (Accessibility) |
| Close panel | F6 (usa-accordion — collapse) |

---

## 8. Cross-Journey Patterns

### Common Pain Points Across Journeys

- **Timezone accuracy is the #1 risk in three journeys** (JRN-02.1, JRN-02.2, JRN-03.2): David's commute decision depends on correct hourly timezone labels; Priya's gardening schedule depends on correct sunrise timezone. `timezone=auto` on every Open-Meteo request is the single mitigation that resolves all three.

- **Blank / stuck screen anxiety appears in JRN-01.2 and JRN-02.1**: Both Maya and David have prior experience with apps that show spinners that never resolve. The perpetual skeleton is more trust-destroying than a clear error message. The never-blank-screen contract (skeleton → error or cached data) must be implemented from Phase 1.

- **Repeat-visit friction**: All three personas benefit from recent location chips (JRN-01.1, JRN-02.1, JRN-03.2). The city search is the most common first action — reducing it to a single tap for repeat users is a high-leverage delight opportunity shared across all journeys.

- **Progressive disclosure design tension resolved by USWDS accordion**: JRN-01.1 (Maya) depends on the main view being clean and simple. JRN-03.2 (Priya) depends on secondary data being accessible. The collapsed-by-default accordion serves both without compromise. This is a cross-journey design decision, not just a feature choice.

### Shared Opportunities

| Opportunity | Relevant Journeys | Mechanism |
|---|---|---|
| Zero-re-type repeat visits | JRN-01.1, JRN-02.1, JRN-03.2 | Recent location chips (localStorage) |
| Never-blank-screen guarantee | JRN-01.2, JRN-02.1 | Skeleton + error + cached-data states (TanStack Query) |
| Timezone correctness everywhere | JRN-02.1, JRN-02.2, JRN-03.1, JRN-03.2 | timezone=auto on all Open-Meteo requests |
| Icon + text label pairings | JRN-03.1, JRN-03.2 | WCAG 1.4.1 compliance (covers all personas) |
| USWDS focus indicators | JRN-02.2 | All interactive elements — also benefits JRN-01.1 (touch targets) |

---

## 9. Journey-to-JTBD Traceability

| Journey | Stage | JTBD-ID | Expected Outcome |
|---|---|---|---|
| JRN-01.1 | Read data | JTBD-01.1 | Temp + condition + precipitation % above fold in < 3s on 375px mobile |
| JRN-01.1 | Decide & close | JTBD-01.2 | Unit preference persisted; no re-toggle on return visit |
| JRN-01.2 | Wait → See error | JTBD-01.3 | usa-alert--error displayed within 8s of API failure; never blank screen |
| JRN-01.2 | Retry | JTBD-01.3 | Retry button functional; second failure shows guidance, not infinite spinner |
| JRN-02.1 | Read hourly cards | JTBD-02.1 | First dry window identifiable in < 15s from app open; precipitation on every card |
| JRN-02.1 | Read hourly cards | JTBD-02.3 | Hourly labels in searched city's local timezone (verified against known timezone offset) |
| JRN-02.2 | Search by keyboard | JTBD-02.2 | Full autocomplete keyboard nav: Down/Up arrows, Enter to select, Escape to dismiss |
| JRN-02.2 | Expand details | JTBD-02.2 | Details accordion responds to Enter/Space; aria-expanded updates; focus preserved |
| JRN-02.2 | Navigate to search | JTBD-02.2 | USWDS 2px focus ring visible on all interactive elements during keyboard nav |
| JRN-03.1 | Compare 7 days | JTBD-03.1 | All 7 days visible without paywall; every row has icon + text label + high/low + precipitation |
| JRN-03.1 | Read temperature trend | JTBD-03.1 | AreaChart renders at 1280px desktop, 125% zoom, without overflow |
| JRN-03.1 | Compare 7 days | JTBD-03.3 | Every condition: icon + text label; no color-only condition indicators |
| JRN-03.2 | Expand panel | JTBD-03.2 | Single tap/click expands Details; all secondary metrics (UV, wind, visibility, sunrise, sunset) visible |
| JRN-03.2 | Read secondary data | JTBD-03.2 | Sunrise/sunset reflect searched city's local time (not browser timezone) |
| JRN-03.2 | Read secondary data | JTBD-03.3 | Secondary metric labels are text-based; not color-coded only |

---

*JOURNEYS version 1.0 — generated 2026-05-01*
*Sources: PERSONAS-SimpleWeatherApp.md, JTBD-SimpleWeatherApp.md, PRD-SimpleWeatherApp.md v1.1, .planning/PROJECT.md*
*Next documents: STORY-MAP, UX-Mockup*
