# KindCircle — Project Plan

Humanitarian social networking and public-health awareness platform (CSE 3422 Software Engineering Lab).
Source: project slides (16 slides) + *Software Engineering Lab Project Requirements.pdf*.

## 1. Team and ownership (from slide 14)

| # | Member | Role | Owns |
|---|--------|------|------|
| M1 | Bahar Shahriyar | Lead · DevOps | Git workflow, code review, API contract, CI, Postman/Newman, E2E tests, deployment, final report |
| M2 | Sumaiya Sadika | Frontend A | React + Vite setup, auth/feed/profile, comments/posts/reports UI, activity submission UI, campaigns UI |
| M3 | Urbashi Biswas | Frontend B | Blood request/donor screens, Leaflet maps, trend charts, chat + notifications UI, moderation/verifier/admin UI |
| M4 | Safyal Muznabin Ratul | Backend | FastAPI, auth/RBAC, post/blood/activity APIs, WebSocket, external-API integration, security hardening |
| M5 | Sadek Hossain Rifat | Database | MySQL schema, ER diagram, migrations, indexes, impact ledger, health-data import, seeds |

Every member must be able to explain their part **and** the whole system at the demo (Req. 9). Each also owns at least one test suite in their layer (Req. 8).

## 2. Requirement coverage (PDF checklist)

| # | Requirement | How we meet it | Owner |
|---|-------------|----------------|-------|
| 1 | Frontend + backend | React 18 + Vite, FastAPI | M2/M3, M4 |
| 2 | 2–5 members, all contribute | 5 members, layer split above | all |
| 3 | Git, remote, branches, merges | GitHub, trunk + feature branches, PR review (section 5) | M1 |
| 4 | Jira board | Epic per phase, stories assigned per member (`jira-backlog.csv`) | M1 |
| 5 | External API | Nominatim geocoding (OpenStreetMap) for addresses; Open-Meteo weather/heat index for heatstroke campaign; Leaflet map tiles | M4, M3 |
| 6 | API testing tool | Postman collection, run in CI via Newman | M1 |
| 7 | Free deployment | Vercel (frontend), Render (backend), free MySQL host | M1 |
| 8 | App testing + evidence | pytest (backend), Vitest (frontend), Selenium (E2E); test-case table + results in `docs/test-report.md` | M4, M2, M1 |
| 9 | Individual contribution | Per-member notes in `docs/contributions.md`, rehearsed demo | all |

## 3. Architecture (slide 11)

React 18 + Vite → REST + authenticated WebSocket → FastAPI modular monolith → MySQL 8 + media storage; Leaflet for maps; validated public-health data imported by admins.

```
frontend/   React + Vite
backend/    FastAPI (modules: auth, social, messaging, blood, impact, health, campaigns, admin)
db/         schema, migrations, seeds, ER diagram
docs/       SRS, API contract, test report, contributions
postman/    collection + environments
```

## 4. Phases (slide 13) → Jira epics

Order is fixed by dependency: foundation → social → humanitarian → health → hardening. Dates are left to the team; treat each phase as roughly one sprint.

| Phase / Epic | Goal | Done when |
|--------------|------|-----------|
| 1 Repo Audit & Requirements | Repo, SRS, wireframes, ER and API drafts | Reviewed and merged to `main` |
| 2 Foundation, API & DB | Running skeleton end to end, CI green | `/health` works from UI → API → DB locally |
| 3 Identity & Profiles | Register/login/roles/profile | Six roles enforced server-side |
| 4 Social Networking | Feed, votes, comments, media, reports, moderation | A user can post, vote, comment; moderator can act on a report |
| 5 Messaging & Notifications | 1:1 chat, in-app notifications | Real-time delivery over WebSocket |
| 6 Blood Coordination | Request → match → confirm → complete with status history | Full state machine works; location only shared with consent |
| 7 Verification & Impact Ledger | Evidence review, one-time points, audit ledger | Points awarded once, only after verification |
| 8 Health Intelligence MVP | Dengue data import, aggregated map, trend chart | Admin-validated data only; external weather API used |
| 9 Campaigns & Community Reports | Campaigns, hazard reports, admin dashboard | Reports moderated; approximate locations only |
| 10 Testing, Security & Deployment | Test evidence, hardening, live deploy | Frontend and backend reachable on free hosting |

## 5. Git and review rules

- `main` is always deployable; no direct pushes. Work on `feature/<jira-key>-<slug>`, open a PR, one reviewer (M1 reviews cross-layer PRs).
- Commit messages: `KC-12: add login endpoint` (Jira key first, imperative). Commit small and often so history shows each member's work.
- Backend ↔ frontend changes start from the agreed OpenAPI contract in `docs/api-contract.yaml`; change the contract in its own PR first.
- DB changes only through migrations authored by M5.

## 6. Jira setup

1. Create a Jira project (Scrum, key `KC`), invite all five members.
2. Import `jira-backlog.csv` (Jira → Settings → System → External System Import → CSV). Map `Parent` to the epic relation and `Assignee` by name or email.
3. Create one sprint per phase; board columns: To Do → In Progress → In Review → Done.
4. Labels `model-opus` / `model-sonnet` mark which model drafted the work (section 7).

## 7. AI workflow

- **Opus**: planning, requirement analysis, UI/UX design (wireframes, design system, ER and API design, review of risky logic such as the points ledger).
- **Sonnet**: implementation, tests, refactors, CI/deploy config.
- Humans own the result: review every AI-written change, and be able to explain it at the demo.

## 8. Risks

| Risk | Mitigation |
|------|-----------|
| Free hosts sleep or limit resources (Render cold start, free MySQL quotas) | Pre-warm before demo; seeded data; screenshots/recorded fallback |
| External API limits (Nominatim 1 req/s, needs a User-Agent) | Cache geocoding results in DB; debounce on UI |
| Location and health data privacy | Consent-based location, auto-expiry, aggregated public maps, disclaimers (slide 12) |
| Scope too large for the course timeline | Build the MVP slice first (dengue only; skip email/push, use in-app notifications) |
| Unequal workload | Jira assignee report each phase; rebalance at sprint planning |

## 9. Open decisions for the team

- Free MySQL provider (TiDB Cloud Serverless or Aiven free tier; verify current limits before choosing).
- Final deadline and sprint length.
- Whether to keep the email/push notifications from slide 5 or defer them.
