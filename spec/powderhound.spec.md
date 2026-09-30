
# Spec

## 0. Document Info

| Field | Value |
|---|---|
| Product name | Powderhound |
| Version / status | Draft 0.1 |
| Owner | Steven Clarke |
| Last updated | 9/28/2026 |

## 1. Overview

### 1.1 Problem statement
If you are a downhill skier who doesn't live near a local mountain, planning a ski trip means researching weather forecasts across many possible destinations and trying to predict which storm will bring the best snow within the window of time you have available.  Then you have to research flight prices, and lodging options for multiple possible destinations, with no easy way to see which dates/resorts offer the best snow-per-dollar prospect.

### 1.2 Vision / value proposition
With a simple web interface, you can quickly search for and find options for ski trips that maximize your chance of high quality snow for the best available price and link directly to the flight and lodging options for easy booking.For the flexible dates powder chaser, receive notifications when high quality low cost trips options become available.

### 1.3 Goals
- G1: Provide search functionality to find ski trips options based on a set of user defined search criteria
- G2: Display search results in a way that is easy to understand and use
- G3: Combine weather forecasts, flight prices, and lodging options to rank search results based on a "snow per dollar" score

### 1.4 Non-goals (explicitly out of scope for v1)
*Non-goals stop agents (and you) from scope creep. Be aggressive.*
- NG1: Booking flights/lodging in-app; instead, deep-link to providers' websites
- NG2: Backcountry skiing is not in scope for v1, resort skiing only 
- NG3: Allow service providers to offer promotions and discounts to incentivize users to book a trip to their destination, and to track the effectiveness of their promotions
- NG4: Search for trips further in the future than 2 weeks out using historical data and climatology.  Only use forecast data within a window that can be expected to be reasonably accurate.
- NG5: Provide a way to save searches and receive alerts when new options are available that match the search criteria

### 1.5 Success metrics
TODO: Need a way to measure the quality of the search results.  This is in part the accuracy of the snow per dollar score, and should provide results at least as good as a human researching the same set of options.  For v1 this is likely to be achieve through two methods.  1. Using static data from the past to run through the scoring logic and produce options that match expected results.  2. Manual testing by the product owner and other users to ensure the search results are as good as expected.

## 2. Users & Personas

| Persona | Description | Key needs | Constraints |
|---|---|---|---|
| Casual Skier | A skier who is not extremely serious or experienced and doesn't care as much about fresh snowfall.  They are looking for a good time and a good value for the money. | They are looking for a good time and a good value for the money. Likely to be booking trips with a group of friends or family and will be more likely to prefer comfortable weather with reasonable snow quality | May want to book further in advance on average |
| Off-Piste Powder Chaser | A skier who is experienced and cares the most about the snow quality on-mountain experience. More tolerant of harsh weather conditions |  They will place the most value on timing trips around storm events to maximize fresh snowfall while making cost comparisons between possible trips easy to use.  May be traveling solo or with a small group of friends and will be more likely to prefer harsh weather conditions if it means more fresh snowfall | |

## 3. Glossary & Domain Model

### 3.1 Glossary
*Define every term the spec uses, so humans and agents share vocabulary.*

| Term | Definition |
|---|---|
| Resort | A ski destination that offers ski passes with lifts and managed terrain for downhill skiing |
| Trip window | Defined by start and end dates within which a trip can occur, as well as an upper and lower limit for how many days long the trip can be |
| Trip option | A combination of a resort, trip window, and total cost of the lowest cost flight and lodging that meets the user's search criteria |
| Snow score | A numerical score that represents the expected snow quantity and quality at a resort for a given trip option.  Includes expected snowfall, base depth, temperature/snow quality, historical climatology, elevation, and other factors that influence snowfall. |
| Risk flag | A flag that alerts the user that the trip window is close to a storm that might disrupt travel |
| Value score | A numerica score that represents the expected snow-per-dollar for a given trip option.  This is driven by the snow score combined with the cost of the trip option. |
| Trip Scorecard | A summary of the scores and flags for a given trip option |

### 3.2 Trip Scorecard: core product logic
*This is the heart of the product. Specify it as precisely as you can; expect to iterate.*

- **Inputs:** Forecast snowfall, base depth, temperature/snow quality, historical climatology, elevation, flight and lodging options, proximity of trip window to a storm that might disrupt travel
- **Forecast horizon & confidence:** The score confidence is mainly driven by the forecast horizon.  The further out the forecast is, the lower the confidence in the snow score.  The confidence in the snow score is also driven by the availability of historical data and the accuracy of the forecast model.
- **Risk score:** The risk score is driven by the proximity of the trip window to a storm that might disrupt travel.  The closer the trip window is to a storm, the higher the risk score.  The risk score is also driven by the availability of historical data and the accuracy of the forecast model.
- **Output:** A scorecard for a given trip option that includes the snow score, score confidence, risk score, and value score.
- **Worked examples:** TODO. *Give 3+ concrete input → expected output examples. These become tests.*

### 3.3 Value score: combining snow and cost
- **Formula / approach:** Snow score per dollar
- **User-tunable parameters:** Groomed terrain preferred skier vs. off-piste terrain preferred skier
- **Worked examples:** TODO

### 3.4 Core entities (conceptual, not schema)
*Resort, Forecast, TripSearch, FlightOption, LodgingOption, TripPackage, User, SavedSearch... TODO: list and relate.*

## 4. User Stories & Acceptance Criteria

*Use IDs. Write acceptance criteria as Given/When/Then so they translate directly to tests.*

### US-01: Search for trips
**As a** TODO, **I want** TODO, **so that** TODO.

**Acceptance criteria**
- AC-01.1: Given TODO, when TODO, then TODO.
- AC-01.2: TODO

### US-02: View trip detail (snow outlook + cost breakdown)
TODO

### US-03: Compare options
TODO

### US-04: Create account / log in
TODO

### US-05: Save searches / receive alerts
TODO

*Add more. Mark priority: **P0** (v1 must), **P1** (v1 should), **P2** (later).*

| ID | Story | Priority | Milestone |
|---|---|---|---|
| US-01 | Search for trips | P0 | M1 |
| US-02 | Trip detail | P0 | M1 |
| US-03 | Compare | P1 | TODO |
| US-04 | Auth | P0 | M2 |
| US-05 | Saved searches / alerts | P2 | TODO |

## 5. Functional Requirements

*Each requirement: one testable statement, an ID, and a link back to a user story.*

### 5.1 Frontend
- FR-100 (US-01): TODO
- FR-101: TODO (loading, empty, and error states for every data-driven view)

### 5.2 Backend API
- FR-200 (US-01): TODO
- FR-201: TODO

### 5.3 Authentication & accounts
- FR-300 (US-04): TODO *(method: email/password, OAuth, magic link? decide in Plan)*
- FR-301: TODO

### 5.4 Integration layer
- FR-400: Provider access shall go through adapter interfaces so providers can be swapped or mocked.
- FR-401: TODO (caching policy, staleness rules, behavior when a provider is down)

### 5.5 Alerts / background jobs *(if in scope)*
- FR-500: TODO

## 6. Third-Party Integrations

*Complete this section during the feasibility spike, using real docs and real sample responses.*

### 6.1 Provider evaluation

| Category | Candidate | Free tier / pricing | Rate limits | Terms of service constraints | Data we need | Access hurdles | Decision |
|---|---|---|---|---|---|---|---|
| Weather forecast | TODO | | | | | | |
| Historical snow / climatology | TODO | | | | | | |
| Flights | TODO | | | | | | |
| Lodging | TODO | | | | | | |
| Resort metadata (location, elevation) | TODO | | | | | | |

### 6.2 Per-provider details *(repeat for each chosen provider)*
- **Endpoints used:** TODO
- **Auth method:** TODO
- **Request/response samples:** saved at `docs/providers/<name>/`
- **Field mapping to our domain model:** TODO
- **Caching & refresh strategy:** TODO
- **Failure modes & fallback behavior:** TODO
- **Cost per typical user search:** TODO *(critical for flights/lodging APIs that charge per call)*

### 6.3 Normalized internal shapes
*TODO: the provider-agnostic data structures the rest of the app consumes (define in the API contract).*

## 7. Non-Functional Requirements

| Area | Requirement |
|---|---|
| Performance | TODO (e.g., search results p95 < X s, given slow third-party calls, define progressive loading behavior) |
| Reliability | TODO (graceful degradation when a provider fails) |
| Cost control | TODO (per-search API cost budget, caching, rate limiting) |
| Security | TODO (password handling, session/token strategy, secrets management, OWASP basics) |
| Privacy | TODO (what user data is stored; retention; deletion; regional laws that may apply) |
| Accessibility | TODO (e.g., WCAG 2.2 AA target) |
| Browser/device support | TODO |
| Observability | TODO (logging, error tracking, provider call metrics) |
| Data freshness | TODO (max age of forecast / price data shown; how it's labeled) |

## 8. Architecture & Technical Constraints

*Fill this in during the **Plan** phase, not the Specify phase. Ask the agent for options; you decide.*

### 8.1 Constraints (given, not decided)
- Preferred languages/frameworks: TODO
- Hosting / budget constraints: TODO
- Team size / maintenance expectations: TODO

### 8.2 System overview
*TODO: component diagram (Mermaid is fine): Frontend ⇄ Backend API ⇄ {Provider adapters, DB, Cache, Job runner}*

### 8.3 Key decisions
*Record each as a short ADR in `spec/decisions/`. List here.*

| ID | Decision | Status | Link |
|---|---|---|---|
| ADR-001 | Frontend framework | TODO | |
| ADR-002 | Backend framework & language | TODO | |
| ADR-003 | Database | TODO | |
| ADR-004 | Auth approach (build vs. managed service) | TODO | |
| ADR-005 | Caching strategy | TODO | |
| ADR-006 | Hosting/deployment | TODO | |

### 8.4 Data model
*TODO: schema or link to migration files once decided.*

### 8.5 API contract
*TODO: link to `spec/05-api.openapi.yaml`. List endpoints here at a high level.*

| Method | Path | Purpose | Auth | Requirement IDs |
|---|---|---|---|---|
| GET | /api/v1/trips/search | TODO | TODO | FR-200 |
| GET | /api/v1/resorts/{id} | TODO | | |
| POST | /api/v1/auth/... | TODO | | FR-300 |

## 9. UX Requirements

- **Key screens:** TODO (search, results list/map, trip detail, compare, account, saved searches)
- **Wireframes / mockups:** TODO (links; rough sketches are fine)
- **Core interaction flows:** TODO
- **Uncertainty communication:** TODO *(how the UI conveys forecast confidence honestly)*
- **Empty / loading / error states:** TODO

## 10. Testing & Verification Strategy

| Level | Approach | Tooling | Notes |
|---|---|---|---|
| Unit | Snow/value score logic driven by worked examples in §3 | TODO | |
| Integration | Provider adapters against recorded fixtures | TODO | Live-call tests separate & opt-in |
| API / contract | Validate against OpenAPI schema | TODO | |
| End-to-end | Critical user flows (US-01, US-04) | TODO | |
| Score validation | Backtest snow score vs. observed historical snowfall | TODO | Product-quality check, not just code correctness |

**Definition of done (per task)**
- [ ] Acceptance criteria met and covered by tests
- [ ] Tests, lint, type-check pass locally and in CI
- [ ] No new dependencies without approval
- [ ] Spec/docs updated if behavior or decisions changed
- [ ] Diff reviewed against requirement IDs

## 11. Milestones

| Milestone | Scope | Exit criteria |
|---|---|---|
| M0: Feasibility spike | Provider evaluation, sample data, scoring prototype | §6 filled; go/no-go on data sources |
| M1: Walking skeleton | Resort list → weather → snow score → API → basic UI | US-01/02 (weather only) working end-to-end |
| M2: Travel costs | Flight + lodging adapters, value score | Ranked trip packages |
| M3: Accounts | Auth, saved searches | US-04, US-05 |
| M4: Hardening & launch | Caching, rate limits, monitoring, deploy | NFRs met |

## 12. Task Breakdown

*Generated in the Tasks phase; live in `spec/tasks/`. Track here.*

| ID | Title | Requirements | Depends on | Status |
|---|---|---|---|---|
| T-001 | TODO | | | Not started |

**Task template**

```markdown
# T-XXX: <title>
**Requirements:** FR-..., US-...
**Depends on:** T-...
**Goal:** one or two sentences
**Scope:** files/areas expected to change
**Out of scope:** what must NOT change
**Done when:**
- [ ] criterion checkable by a test or command
**Notes for the agent:** gotchas, links to docs/fixtures
```

## 13. Risks & Open Questions

### 13.1 Risks

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Flight/lodging API access is restricted, costly, or ToS-limited | TODO | High | Feasibility spike (M0); deep-link fallback |
| Snowfall forecasts beyond ~1 to 2 weeks are low-skill; product oversells certainty | TODO | High | Explicit horizon policy + uncertainty UI (§3.2, §9) |
| Per-search API costs exceed budget | TODO | Med | Caching, quotas, cost budget (§7) |
| Agent invents nonexistent API behavior | TODO | Med | Real docs + fixtures in repo; live sandbox verification |
| Scope creep | TODO | Med | Non-goals list; P0/P1/P2 discipline |

### 13.2 Open questions

| # | Question | Owner | Needed by | Resolution |
|---|---|---|---|---|
| Q1 | TODO | | | |

## 14. Decision Log

*Append-only. One line per decision that changes the spec or plan.*

| Date | Decision | Rationale | Affects |
|---|---|---|---|
| TODO | | | |

## 15. Appendix
- Links to provider docs, competitor references, research on forecast skill, wireframes