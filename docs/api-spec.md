# 포트폴리오 API 명세서 (v1)

## 문서 목적
- `react-portfolio` 프론트엔드가 현재 사용하는 필드 기준으로 API 계약을 확정합니다.
- 1차 전환에서는 프론트 변경을 최소화하기 위해 응답 shape를 최대한 동일하게 유지합니다.

## 참고 문서
- `react-portfolio/docs/portfolio-api-field-reference.md`
- `react-portfolio/src/types/portfolio.ts`
- `react-portfolio/src/data/portfolioData.ts`

## 기본 정보
- 기본 경로: `/api/v1`
- 성공 응답 콘텐츠 타입: `application/json; charset=utf-8`
- 에러 응답 콘텐츠 타입: `application/problem+json; charset=utf-8`
- 시간 포맷: ISO-8601 UTC (예: `2026-02-24T04:21:22Z`)

## 엔드포인트
### `GET /api/v1/portfolio`
- 프론트엔드 렌더링에 필요한 전체 포트폴리오 데이터를 단일 응답으로 반환합니다.

## 권장 헤더
### 요청 헤더
- `Accept: application/json`
- `If-None-Match: "<etag>"` (선택, 조건부 조회)
- `X-Request-Id: "<uuid>"` (선택, 추적용)

### 응답 헤더
- `ETag: "<etag>"` (캐시 검증)
- `Cache-Control: public, max-age=60, stale-while-revalidate=300`
- `Vary: Accept, Accept-Encoding`
- `X-Request-Id: "<uuid>"` (서버 생성 또는 요청값 전달)

## 성공 응답
### HTTP 200

```json
{
  "profile": {
    "name": "정찬영",
    "role": "Backend Engineer",
    "headline": "신뢰 가능한 백엔드 서비스를 만듭니다",
    "summary": "Spring Boot 기반 API 설계/개발",
    "location": "Seoul, KR",
    "bio": ["문단 1", "문단 2"],
    "photoSrc": "https://example.com/profile.jpg",
    "photoAlt": "정찬영 프로필 사진"
  },
  "navItems": [
    {
      "id": "hero",
      "label": "Home"
    }
  ],
  "skills": [
    {
      "category": "Backend",
      "items": ["Java", "Spring Boot", "PostgreSQL"]
    }
  ],
  "projects": [
    {
      "title": "portfolio-api",
      "summary": "포트폴리오 백엔드 API",
      "stack": ["Java", "Spring Boot", "PostgreSQL"],
      "role": "백엔드 개발",
      "impact": "응답 속도 안정화",
      "details": ["세부 내용 1", "세부 내용 2"],
      "links": {
        "github": "https://github.com/chan0e/portfolio-api",
        "demo": "https://example.com"
      }
    }
  ],
  "experience": [
    {
      "company": "Example Corp",
      "position": "Backend Engineer",
      "period": "2024.01 - 현재",
      "achievements": ["성과 1", "성과 2"]
    }
  ],
  "contact": {
    "email": "hello@example.com",
    "github": "https://github.com/chan0e",
    "linkedin": "https://linkedin.com/in/example",
    "blog": "https://example.dev",
    "message": "협업 문의는 이메일로 부탁드립니다."
  },
  "meta": {
    "syncedAt": "2026-02-24T04:21:22Z",
    "source": "notion"
  }
}
```

## 필드 계약
### profile
- 필수: `name`, `role`, `headline`, `summary`, `location`, `bio`, `photoSrc`, `photoAlt`
- 타입: `bio`는 `string[]`, 나머지는 `string`

### navItems[]
- 필수: `id`, `label`
- 타입: `id`, `label` 모두 `string`

### skills[]
- 필수: `category`, `items`
- 타입: `category`는 `string`, `items`는 `string[]`

### projects[]
- 필수: `title`, `summary`, `stack`, `role`, `impact`, `links.github`
- 선택: `details`, `links.demo`
- 타입: `stack`/`details`는 `string[]`, 그 외는 `string`

### experience[]
- 필수: `company`, `position`, `period`, `achievements`
- 타입: `achievements`는 `string[]`, 나머지는 `string`

### contact
- 필수: `email`, `github`, `linkedin`, `blog`, `message`
- 타입: 모두 `string`

### meta
- 필수: `syncedAt`, `source`
- 타입: `syncedAt`은 ISO-8601 UTC 문자열, `source`는 `string`

## Null/빈값 규칙
- 배열 필드는 `null` 대신 빈 배열 `[]`를 반환합니다.
- 객체 필드(`profile`, `contact`, `meta`)는 항상 존재해야 합니다.
- 1차 전환 기준 필수 문자열 필드는 `null`을 허용하지 않습니다.

## 정렬 규칙
- `navItems`, `skills`, `projects`, `experience`는 저장 순서를 유지합니다.
- 추후 `order` 필드가 도입되면 `order` 오름차순을 우선합니다.

## 비-200 응답 명세
### 공통 에러 포맷 (Problem Details)
에러 응답은 `application/problem+json`을 사용합니다.

```json
{
  "type": "https://api.devchan.com/problems/portfolio-bad-request",
  "title": "Bad Request",
  "status": 400,
  "detail": "Validation failed for query parameter",
  "instance": "/api/v1/portfolio",
  "code": "PORTFOLIO_BAD_REQUEST",
  "traceId": "9f3c9d52d2e3476ca8e8d10a7b8ed901",
  "errors": [
    {
      "field": "Accept",
      "reason": "Unsupported media type requested"
    }
  ]
}
```

### 상태 코드 정책
| HTTP | 코드명 | 사용 조건 | 응답 본문 |
|---|---|---|---|
| 304 | Not Modified | `If-None-Match` 값이 현재 `ETag`와 동일한 경우 | 없음 |
| 400 | Bad Request | 요청 파라미터/헤더 유효성 검증 실패 | Problem Details |
| 401 | Unauthorized | 인증 필요 모드에서 토큰 누락 또는 무효 | Problem Details |
| 403 | Forbidden | 인증은 되었으나 접근 권한 부족 | Problem Details |
| 404 | Not Found | 포트폴리오 데이터 미존재 또는 비공개 상태 | Problem Details |
| 406 | Not Acceptable | 지원하지 않는 `Accept` 헤더 요청 | Problem Details |
| 422 | Unprocessable Content | 저장 데이터가 API 계약을 충족하지 못함 | Problem Details |
| 429 | Too Many Requests | 레이트 리밋 초과 | Problem Details + `Retry-After` |
| 500 | Internal Server Error | 애플리케이션 내부 예외 | Problem Details |
| 502 | Bad Gateway | 업스트림 연동 실패(프록시/게이트웨이 경유 시) | Problem Details |
| 503 | Service Unavailable | 배포/점검/일시적 장애 | Problem Details + `Retry-After` |
| 504 | Gateway Timeout | 업스트림 응답 타임아웃(게이트웨이 경유 시) | Problem Details |

## 트렌드 반영 점검 항목
- 표준 에러 스펙(`application/problem+json`) 사용
- `X-Request-Id` 기반 추적성 확보
- `ETag` + `If-None-Match` 조건부 조회로 캐시 효율화
- `429` + `Retry-After` 기반 트래픽 제어
- 기계 판독용 에러 코드(`code`)와 인적 판독용 메시지(`detail`) 분리

## 검증 메모
- URL 필드(`photoSrc`, `links.github`, `links.demo`, `github`, `linkedin`, `blog`)는 절대 URL을 권장합니다.
- `email`은 유효한 이메일 형식이어야 합니다.
- `period`는 현재 UI 표기 정책 문자열을 유지합니다. (예: `2024.01 - 현재`)

## 보안/암호화 기준
### 전송 구간
- 모든 API 통신은 HTTPS(TLS 1.2+)를 사용합니다.
- HTTP 요청은 HTTPS로 리다이렉트하고 HSTS 적용을 권장합니다.

### 저장 구간
- PostgreSQL 데이터 파일/디스크는 AES-256 기반 저장 암호화를 사용합니다. (클라우드 관리형 암호화 포함)
- 백업 스냅샷도 동일한 암호화 정책을 적용합니다.

### 필드 단위 암호화
- 현재 `GET /api/v1/portfolio`는 공개 렌더링 데이터가 주 대상이므로 응답 본문 암호화는 적용하지 않습니다.
- 비공개 개인정보 필드(예: private email, phone)가 추가될 경우 DB 컬럼 단위 암호화를 적용합니다.
- 권장 방식: `AES-256-GCM` + 랜덤 IV + 인증 태그 저장
- 검색이 필요한 필드는 평문 검색 대신 salt 기반 해시(SHA-256) 보조 컬럼 사용을 권장합니다.

### 키 관리
- 암호화 키는 KMS/Vault에서 관리하고 애플리케이션에는 키 참조 정보만 전달합니다.
- 키 로테이션 주기를 운영 정책으로 정의합니다. (권장: 90일~180일)
- Notion/API 토큰, DB 비밀번호는 저장소에 커밋하지 않고 Secret Manager 또는 환경 변수로 주입합니다.

### 로그/모니터링
- 로그에는 토큰/비밀번호/개인정보를 남기지 않습니다.
- 불가피한 경우 마스킹 처리(`te***@example.com`) 후 기록합니다.

## 버저닝 메모
- 하위 호환 가능한 필드 추가는 `v1`에서 허용합니다.
- 호환성이 깨지는 변경은 `/api/v2`로 분리합니다.

## 향후 확장(권장)
- 공통 메타: `id`, `order`, `isVisible`, `createdAt`, `updatedAt`
- projects 확장: `slug`, `thumbnailUrl`, `status`
- navItems 확장: `targetSection`, `isExternal`, `externalUrl`
