# Phase 1: Foundation - Context

**Gathered:** 2026-05-01
**Status:** Ready for planning

<domain>
## Phase Boundary

Users can find any city and see current weather conditions immediately — no blank screens, no stalled states. Delivers: location search with autocomplete + GPS opt-in, current conditions hero (temperature, condition, feels-like, high/low, precipitation, humidity, wind), unit toggle (°C/°F), skeleton loading state, and error recovery. Hourly forecast, 7-day forecast, and secondary details are Phase 2 and 3 respectively.

</domain>

<decisions>
## Implementation Decisions

### Search bar — Initial empty state
- App opens with a large **centered search prompt** — full-screen, no weather area visible yet
- Clean, focused: just the search input (usa-search) and a brief prompt like "Enter a city to get started"
- GPS icon button sits inline at the **right edge** of the search input — visually connected to location finding
- First-time users (no localStorage) see the centered prompt only

### Search bar — Persistent state (city loaded)
- Once a city is loaded, the search bar moves to a **fixed top header** — always visible, never scrolls away
- User can re-search at any time without scrolling back up
- GPS icon remains inline in the search input at all times

### Recent location chips
- Chips are **hidden by default** — they only appear when the user taps/clicks into the search input
- This keeps the main weather view clean; chips surface on demand as part of the search interaction
- Chips appear below the search input when focused, alongside (or above) the autocomplete suggestions

### Claude's Discretion
- Exact visual treatment of the centered empty state (illustration vs plain text vs subtle background)
- Search bar transition animation from centered → fixed header after first city selection
- Chip appearance/styling (pill shape, USWDS usa-tag variant, truncation for long city names)
- Exact wording of the centered prompt
- Skeleton loading design and shimmer behavior

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Project requirements and vision
- `.planning/PROJECT.md` — Core value, constraints, key decisions (timezone=auto, integer temps, USWDS, geolocation opt-in)
- `.planning/REQUIREMENTS.md` — F0-01 through F0-05, F1-01 through F1-06 — all Phase 1 requirements with acceptance criteria

### Feature specifications
- `project_specs/PRD-SimpleWeatherApp.md` §5 (Technical Architecture), §6 F0 and F1 — Feature requirements with USWDS component notes
- `project_specs/FRD-SimpleWeatherApp.md` §2 (F0 — Location Search & Detection), §3 (F1 — Current Conditions Display) — Detailed functional specs including USWDS components, ARIA structure, process flows, error states
- `project_specs/TechArch-SimpleWeatherApp.md` §3 (USWDS Integration Architecture), §4 (Component Architecture), §6 (API Design & Integration), §8 (Data Transformation Layer) — TypeScript interfaces, API URLs, transformation functions

### UX and design
- `project_specs/UX-Mockup-SimpleWeatherApp.md` — Screen designs for main view (mobile + desktop), autocomplete suggestion list, interaction patterns (unit toggle, skeleton, USWDS alert states)
- `project_specs/PERSONAS-SimpleWeatherApp.md` — PER-01 (Maya Torres) and PER-02 (David Park) — primary Phase 1 personas with top tasks and success criteria

### API integration
- Open-Meteo Forecast API: `https://api.open-meteo.com/v1/forecast` — CRITICAL: `timezone=auto` is mandatory on every request
- Open-Meteo Geocoding API: `https://geocoding-api.open-meteo.com/v1/search` — city name → lat/lon, up to 5 results
- Nominatim Reverse Geocoding: `https://nominatim.openstreetmap.org/reverse` — GPS coords → city name

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- None yet — this is a greenfield project. Phase 1 establishes all foundational components.

### Established Patterns
- **USWDS design tokens via Tailwind CSS v4**: All color, spacing, and typography must use USWDS CSS custom properties mapped to Tailwind theme (see TechArch §3)
- **TanStack Query v5**: All API calls use `useQuery` with `staleTime: 10min`, `networkMode: 'offlineFirst'`, `retry: 2` — established in TechArch §5
- **Integer temperatures**: All temperature values go through `roundTemp()` and `toFahrenheit()` from TechArch §8 before display — never raw API floats
- **timezone=auto**: Every Open-Meteo request must include this parameter — all time display uses `Intl.DateTimeFormat` with the API-returned `timezone` string

### Integration Points
- Phase 1 establishes `App`, `AppShell`, `SearchBar`, `CurrentConditions`, `UnitToggle`, `FreshnessIndicator` — all subsequent phases add components beneath these
- `QueryClientProvider` wraps the entire app — established in Phase 1, consumed by all future phases
- `localStorage` schema (`swa:unit`, `swa:recent`) established in Phase 1 — Phase 3+ reads these

</code_context>

<specifics>
## Specific Ideas

- The initial empty state should feel **instant and focused** — not like a landing page, but like the app is ready and waiting. Think of how a good command palette feels: open, cursor blinking, ready.
- The transition from centered search → persistent header after first city selection is a meaningful UX moment — the app "comes alive" with the first weather load.
- Recent chips appear **within the search interaction** (on focus), not as permanent UI. This respects Maya's desire for a clean main view while giving David quick repeat-access to his regular cities.

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within Phase 1 scope.

</deferred>

---

*Phase: 01-foundation*
*Context gathered: 2026-05-01*
