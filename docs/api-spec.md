# Portfolio API Specification (v1)

## Base
- Base path: `/api/v1`
- Content type: `application/json; charset=utf-8`
- Time format: ISO-8601 UTC, example: `2026-02-24T04:21:22Z`

## Endpoint
### `GET /api/v1/portfolio`

Returns a full portfolio payload for frontend rendering.

## Success Response
### HTTP 200

```json
{
  "profile": {
    "name": "string",
    "headline": "string",
    "bio": "string",
    "location": "string",
    "email": "string",
    "phone": "string",
    "avatarUrl": "string",
    "resumeUrl": "string",
    "updatedAt": "2026-02-24T04:21:22Z"
  },
  "skills": [
    {
      "id": 1,
      "name": "Java",
      "category": "backend",
      "level": "advanced",
      "displayOrder": 1
    }
  ],
  "projects": [
    {
      "id": 1,
      "title": "portfolio-api",
      "summary": "Portfolio backend service",
      "description": "Detailed project description",
      "techStack": ["Java", "Spring Boot", "PostgreSQL"],
      "repoUrl": "https://github.com/example/repo",
      "demoUrl": "https://example.com",
      "startedAt": "2025-01-01",
      "endedAt": "2025-04-30",
      "isOngoing": false,
      "displayOrder": 1
    }
  ],
  "experience": [
    {
      "id": 1,
      "organization": "Company",
      "role": "Backend Engineer",
      "summary": "Built core services",
      "startedAt": "2024-01-01",
      "endedAt": "2025-01-01",
      "isCurrent": false,
      "displayOrder": 1
    }
  ],
  "contact": {
    "githubUrl": "https://github.com/example",
    "linkedinUrl": "https://linkedin.com/in/example",
    "blogUrl": "https://example.dev",
    "notionUrl": "https://notion.so/example",
    "updatedAt": "2026-02-24T04:21:22Z"
  },
  "meta": {
    "version": "v1",
    "generatedAt": "2026-02-24T04:21:22Z"
  }
}
```

## Ordering Rules
- `skills`, `projects`, `experience` are sorted by `displayOrder` ascending.
- If `displayOrder` is the same, sort by `id` ascending.

## Nullability Rules
- Empty list sections must return `[]`, not `null`.
- Optional string fields may return `null`.
- Top-level objects must always exist (`profile`, `contact`, `meta`).

## Error Response
Common error payload:

```json
{
  "timestamp": "2026-02-24T04:21:22Z",
  "status": 400,
  "error": "Bad Request",
  "code": "PORTFOLIO_BAD_REQUEST",
  "message": "Validation failed",
  "path": "/api/v1/portfolio"
}
```

### HTTP 400
- Invalid request parameters.

### HTTP 404
- Portfolio data not found.

### HTTP 500
- Internal server error during retrieval or mapping.

## Validation Notes
- `email` should be a valid email format when present.
- `repoUrl`, `demoUrl`, and social URLs should be absolute URLs when present.
- `displayOrder` should be a non-negative integer.

## Versioning Notes
- Breaking changes require a new path version (example: `/api/v2`).
- Additive fields can be introduced in `v1` if backward compatible.
