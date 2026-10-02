# Member Event Operations Platform

A private member-facing event platform for handling registrations, capacity-limited booking, waitlists, ticket distribution and staff administration with database-enforced business rules.

> The source repository is private because the application works with real member and event operations. This case study excludes personal data, private endpoints, credentials, event-specific records and internal operational details.

## Problem

A limited-capacity member event is more than a registration form. The system has to answer operational questions consistently:

- who gets a confirmed place when many people register at the same time;
- how waitlisted members are promoted after cancellations;
- who is allowed to see or change each record;
- how digital tickets are assigned without duplicates;
- how staff can import, review and export operational data safely;
- how the codebase stays maintainable as booking, ticketing, administration and authentication grow together.

## My Role

I implemented and refactored major parts of the application, including the modular architecture, booking/ticket/admin flows, shared infrastructure and automated verification. The repository history includes a substantial architecture migration authored from my account that introduced domain modules, tests and CI around these boundaries.

## Architecture

```text
React + TypeScript + React Router
             |
             v
        domain modules
 auth | profile | event | booking | ticket | admin
             |
             v
       Supabase client
             |
             v
 PostgreSQL + Auth + Storage
       |             |
       |             +--> ticket/document assets
       +--> RPCs + RLS business rules
```

The frontend is built with React, TypeScript and Vite. TanStack Query manages server state, while Supabase provides PostgreSQL, authentication and storage.

The application evolved into a modular-monolith structure rather than keeping booking, administration and data access mixed into page components. Modules own their API layer, hooks, UI and types, with automated checks to detect unwanted architectural drift.

## Engineering Decisions

### Put capacity decisions in the database

Joining an event is handled through a PostgreSQL RPC rather than calculating remaining capacity in the browser. The transaction locks the relevant event state while deciding whether the member belongs in the confirmed or waitlist queue.

This makes the database responsible for the race-sensitive decision instead of trusting two clients to independently observe the same remaining capacity.

### Enforce duplicate and ownership rules centrally

Bookings are constrained so the same member cannot independently create multiple active registrations for the same event. Cancellation and administrative operations are also designed around authenticated identity and authorization rules rather than UI visibility alone.

### Treat ticket assignment as an operation

Digital tickets are managed as a pool rather than as arbitrary file links. Assignment is performed through a controlled operation that selects an available ticket and associates it with the correct booking.

### Separate domain modules

The codebase was reorganized around domains such as authentication, booking, ticketing, events, profiles and administration. Shared infrastructure is kept outside those domain modules to reduce accidental cross-module coupling.

### Detect architecture drift

In addition to ordinary linting and tests, the project includes scripts for dependency-boundary checks, circular-dependency detection, architecture baselines and drift reporting. This was useful because the project had already gone through a substantial structural migration and needed a way to prevent gradually returning to the previous mixed layout.

## Verification

The repository includes several verification layers:

- Vitest unit and integration tests;
- React Testing Library for UI/hook behavior;
- Playwright browser E2E flows;
- GitHub Actions test execution;
- architecture-boundary tests;
- circular-dependency and drift checks;
- linting and design-system rules.

The CI workflow collects coverage information, although the historical workflow did not enforce a hard coverage percentage gate. I therefore describe the project as having automated test/coverage reporting rather than claiming a coverage threshold that was not actually enforced.

## Operational Features

Implemented areas include:

- member authentication/profile flows;
- active-event presentation;
- confirmed/waitlist booking;
- cancellation and queue management;
- administrative booking views;
- ticket-pool management and digital ticket access;
- bulk spreadsheet-oriented administration workflows;
- browser and automated regression verification.

Some additional modules existed as thinner scaffolding during development, so this case study focuses on the areas with clear implementation evidence rather than presenting every planned module as complete.

## Privacy Boundary

The underlying system can process identifying member information and operational event data. Those records and organization-specific procedures are intentionally not reproduced here. The public artifact is limited to architecture, engineering decisions and generalized workflow descriptions.

## Technologies

TypeScript · React 19 · Vite · React Router · TanStack Query · Supabase · PostgreSQL · Row-Level Security · Vitest · Playwright · GitHub Actions

## Status

Archived private operational project. It remains useful as an engineering case study because the repository preserves the architecture migration, database-driven concurrency decisions and layered verification work without requiring the underlying member data to be public.
