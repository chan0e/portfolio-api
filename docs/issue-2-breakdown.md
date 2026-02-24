# Issue #2 Breakdown

This document breaks down issue `#2` into implementation-sized tasks.

## Scope
- Build backend foundations for portfolio data delivery.
- Provide a single response endpoint: `GET /api/v1/portfolio`.
- Define schema, migration, and error handling baseline.

## Tasks
1. Define API contract
- Write endpoint request/response contract.
- Define validation and standard error format.
- Freeze v1 fields before implementation.

2. Design database schema
- Design tables: `profile`, `skills`, `projects`, `experience`, `contact`.
- Define foreign keys, indexes, and display order columns.
- Produce ERD and SQL-level constraints.

3. Configure migration tool
- Select Flyway or Liquibase.
- Add initial migration scripts.
- Verify clean bootstrap on empty PostgreSQL.

4. Build application layers
- Create controller/service/repository packages.
- Add entity and DTO classes.
- Add mapper strategy between entity and API model.

5. Implement `GET /api/v1/portfolio`
- Query all sections needed by frontend.
- Aggregate into one response payload.
- Guarantee deterministic list ordering.

6. Add exception handling standard
- Implement global exception handler.
- Map domain/validation failures to `400/404/500`.
- Return a common error payload format.

7. Add tests
- Unit tests for service and mapper logic.
- Integration test for endpoint and DB access.
- Smoke test for app startup and basic retrieval.

8. Add runbook basics
- Document local run instructions.
- Document required env vars and DB setup.
- Add sample response and troubleshooting notes.

## Definition of Done
- API contract is accepted and unchanged in implementation.
- Local PostgreSQL + application run is verified.
- `GET /api/v1/portfolio` returns the agreed schema.
- Core tests pass for normal and error cases.
