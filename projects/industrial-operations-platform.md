# Industrial Operations & Inventory Platform

A private business-software project for bringing inventory, purchasing, invoicing and operational reporting into a single, consistent system.

> The implementation repository is private and contains real operational material. This case study intentionally omits company identifiers, customer/supplier records, financial values, production infrastructure and credentials.

## Problem

Small industrial-distribution teams often operate across spreadsheets, e-mail, invoices, stock counts and separate reporting workflows. The difficult part is not building another dashboard; it is deciding which data source is authoritative and making stock-changing operations safe when several workflows can affect the same product.

The platform was built around three goals:

- keep stock movements consistent across manual operations and invoice-driven automation;
- turn purchasing and incoming supply into structured workflows rather than spreadsheet-only state;
- make operational metrics come from shared database definitions instead of slightly different calculations in each screen.

## My Role

I worked on the architecture, implementation, database rules, integration workflows and verification of the system. Development was AI-assisted, but requirements, architecture choices, review, integration and acceptance decisions remained human-gated.

A representative refactor moved conflicting frontend metric calculations into shared PostgreSQL views/RPCs so the UI became a consumer of canonical definitions rather than another source of business logic.

## Architecture

```text
React + TypeScript client
        |
        v
Supabase / PostgreSQL
  |     |       |
  |     |       +--> views / reporting queries
  |     +----------> RPC business operations
  +----------------> RLS / role-aware access
        |
        +--> external invoice integration worker
        +--> scheduled forecasting / operational jobs
```

The application uses React and TypeScript on the frontend with PostgreSQL/Supabase as the main persistence and business-rule layer. The repository also contains an external invoice-processing worker, scheduled jobs, database migrations and verification workflows.

## Engineering Decisions

### Atomic stock movements

Manual inventory changes are handled through database-side RPC operations rather than a read-modify-write sequence in the browser. A stock update, movement record and audit information can therefore be treated as one operation.

### Optimistic locking

Products carry a version used when applying stock updates. A mutation is rejected when the caller is operating on a stale version, reducing the risk of silent lost updates when more than one workflow touches the same product.

### One source of truth for metrics

As the system grew, several business metrics had accumulated overlapping definitions. I refactored these toward database-side views/RPCs for values such as stock valuation, reorder state and customer sales aggregation. The frontend now focuses on presentation instead of redefining the same metric independently.

### Invoice-to-stock automation

Outgoing electronic invoices can be imported by a background integration process and reconciled with products before stock movements are applied. This separates external-service polling from the user-facing application and allows failed or unmatched work to be handled explicitly rather than silently changing inventory.

### Demand and replenishment support

The project includes intermittent-demand forecasting based on Croston/SBA-style logic, reorder suggestions and incoming-supply visibility. These features are used as planning support rather than as an autonomous purchasing system.

### Role-aware data access

The database uses row-level access rules and role-aware operations. Administrative, management, warehouse and read-only responsibilities are separated rather than relying only on hidden frontend controls.

## Verification and Operational Discipline

The repository includes:

- Vitest-based application tests;
- TypeScript type checking and linting;
- database migrations and schema documentation;
- CI checks for database consistency;
- secret-scanning configuration;
- explicit architecture and database decision records;
- verification before production-affecting changes.

The project also exposed an important engineering lesson: a database can drift away from its migration history if operational changes are applied without preserving their migration source. Later cleanup work brought live definitions back under version control and added checks around that boundary.

## Privacy Boundary

The private repository contains operational spreadsheets, invoices and internal documentation, so publishing the source repository would expose information unrelated to evaluating the engineering work. This public case study therefore describes only generalized architecture and design decisions.

## Technologies

TypeScript · React · Vite · Supabase · PostgreSQL · SQL/RPC · Row-Level Security · Vitest · GitHub Actions · External API/SOAP Integration

## Status

Private operational system under continued iteration. This page documents engineering patterns and responsibilities without presenting the private repository as publicly inspectable code.
