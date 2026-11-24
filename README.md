# SME Compliance Deadline Engine

> A lightweight, affordable SaaS tool that tells South African micro-SMEs exactly which compliance deadlines are approaching, what happens if they miss them, and sends escalating reminders — so no small business is deregistered or penalised for forgetting a date.

## The Problem

South African micro-SMEs face a compliance burden that is disproportionate to their size. Studies cited by the Department of Small Business Development estimate that the regulatory burden costs firms between **4% and 8% of turnover**, and that it takes a business around **210 hours a year** just to meet its tax-compliance requirements[reference:2]. For a business with no compliance department, those hours are stolen from the work that actually generates revenue — and a missed CIPC annual return or SARS filing can mean deregistration or penalties. This project exists to make those deadlines impossible to forget.

## The Solution

The MVP is a web application that does four things:

1. A business owner registers and creates a profile (entity type, turnover band, industry).
2. A rules engine maps that profile to the compliance obligations that actually apply to them — starting with CIPC annual return, SARS income tax, and UIF.
3. A dashboard and calendar show upcoming deadlines, with overdue items highlighted.
4. An automated email reminder schedule fires at 14 days, 7 days, and 1 day before each deadline.

A "Pro" upgrade via Yoco Checkout is included in the portfolio build in test mode. Direct filing with CIPC or SARS is **out of scope** — this is a deadline engine, not a filing tool.

## Tech Stack & Architecture Decisions

- **Frontend: React (plain JavaScript) + Vite** — Vite gives a fast dev server and a production build with almost zero configuration. Plain JavaScript keeps the bundle small and the code readable for a solo developer; there is no need for TypeScript's overhead in a four-week MVP.

- **Backend: FastAPI (Python)** — FastAPI was chosen over Express for three concrete reasons. First, it generates Swagger/OpenAPI documentation automatically, which means every endpoint is documented and manually testable from day one — no separate docs to maintain[reference:3]. Second, Pydantic validation catches malformed request data before it reaches the database, which matters when the rules engine depends on clean profile fields. Third, FastAPI is async by default, so the reminder-scheduling endpoints and database queries do not block each other[reference:4]. Express is a fine framework, but its advantages — full-stack JavaScript, a massive middleware ecosystem — do not outweigh FastAPI's built-in documentation and validation for this project.

- **Database: MySQL on Aiven (Free Tier)** — MySQL is a relational database, and the core data model here is relational: users own businesses, businesses map to obligations, obligations have deadlines, deadlines have reminders. A relational database with foreign keys is the correct fit. Aiven's free tier gives a fully managed MySQL instance with no time limit — but **it powers off after a period of inactivity** and must be woken manually before a demo[reference:5]. This is a known limitation, documented here so it is not a surprise during a Sprint Review. The database must be woken before any demo or recruiter visit.

- **Payments: Yoco Checkout** — Yoco is the most practical payment gateway for a South African SME product in 2026. It is card-strong, popular with smaller merchants, and its Checkout API is deliberately simple: POST to a checkout endpoint, receive a redirect URL, send the user there[reference:6]. That is exactly the level of integration a four-week MVP can absorb. The integration is built in **test mode** for the portfolio demo; production approval is out of scope.

## Scrum Process

This project is managed with Scrum, following the SBOK™ Guide. The process artifacts are visible, not decorative:

- [Prioritised Product Backlog](https://github.com/users/leratoyapi/projects/3/views/1) — MoSCoW-prioritised user stories with story points and acceptance criteria.
- [Sprint 1 Backlog](https://github.com/users/leratoyapi/projects/3/views/1) — Committed stories US-01, US-02, US-03.
- [Sprint 2 Backlog](none yet ) — Committed stories US-04, US-05, US-06.
- [Definition of Done](none yet ) — The quality gate every item must pass before it enters "Done."
- [Retrospectives](none yet) — Notes from Sprint 1 and Sprint 2 retrospectives.

The board is the single source of truth for what is being worked on: [Project Board](LINK_TO_PROJECT_BOARD).

## Getting Started

*(TODO — fill in Week 4. This section will contain the exact commands to clone the repo, install dependencies, configure environment variables, run the FastAPI backend, and run the React frontend. It is intentionally blank until the stack is actually deployed.)*

## Roadmap

- [ ] **Sprint 1: Walking skeleton** — Register → create profile → view obligations. Deployed to Render (API) and Cloudflare Pages (frontend).
- [ ] **Sprint 2: Reminders, payments, polish** — Calendar view, escalating email reminders, Yoco test checkout, UI polish.

## Known Limitations

- Aiven MySQL free tier powers off after inactivity; the database must be woken before demos.
- Yoco integration is in test mode only; no production payment processing.
- No direct filing with CIPC or SARS — the tool surfaces deadlines, it does not submit returns.