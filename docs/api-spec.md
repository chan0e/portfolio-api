# 포트폴리오 API 명세서 (v1)

## 기본 정보
- 기본 경로: `/api/v1`
- 콘텐츠 타입: `application/json; charset=utf-8`
- 시간 포맷: ISO-8601 UTC, 예시: `2026-02-24T04:21:22Z`

## 엔드포인트
### `GET /api/v1/portfolio`

프론트엔드 렌더링에 필요한 전체 포트폴리오 데이터를 반환합니다.

## 성공 응답
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

## 정렬 규칙
- `skills`, `projects`, `experience`는 `displayOrder` 오름차순으로 정렬합니다.
- `displayOrder`가 같으면 `id` 오름차순으로 정렬합니다.

## Null 처리 규칙
- 비어 있는 리스트 섹션은 `null`이 아닌 `[]`를 반환해야 합니다.
- 선택 문자열 필드는 필요 시 `null`을 반환할 수 있습니다.
- 최상위 객체(`profile`, `contact`, `meta`)는 항상 존재해야 합니다.

## 에러 응답
공통 에러 페이로드:

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
- 요청 파라미터가 유효하지 않은 경우

### HTTP 404
- 포트폴리오 데이터를 찾지 못한 경우

### HTTP 500
- 조회 또는 매핑 중 내부 서버 오류가 발생한 경우

## 검증 메모
- `email`은 값이 있을 경우 유효한 이메일 형식이어야 합니다.
- `repoUrl`, `demoUrl`, 소셜 URL은 값이 있을 경우 절대 URL이어야 합니다.
- `displayOrder`는 0 이상의 정수여야 합니다.

## 버저닝 메모
- 호환성이 깨지는 변경은 새 경로 버전(예: `/api/v2`)을 사용합니다.
- 하위 호환 가능한 필드 추가는 `v1`에서 허용할 수 있습니다.
