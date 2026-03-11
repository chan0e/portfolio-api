# Issue #5 표준 단어 규칙

기준 문서:
- `docs/issue-5-db-schema-design.md`
- `docs/issue-5-glossary.md`

목적:
- 스키마/ERD/DDL/문서/코멘트에서 같은 대상을 같은 단어로 표기한다.
- 명명 규칙 불일치로 인한 구현/리뷰 혼선을 줄인다.

## 1) 기본 원칙
- 한 개념에는 한 개 표준 용어만 사용한다.
- 문서, ERD, DDL, DBML에서 용어를 동일하게 유지한다.
- 축약어는 이미 합의된 경우만 사용하고, 처음 등장 시 원어를 병기한다.
- 과거 표현(`id`, `period`)은 신규 문서에서 표준 표현으로 대체한다.

## 2) 식별자/명명 규칙
| 대상 | 표준 규칙 | 예시 |
|---|---|---|
| 테이블명 | `snake_case` | `project_link`, `experience_achievement` |
| PK 컬럼명 | `table_name_id` | `portfolio_id`, `project_id` |
| FK 컬럼명 | 참조 부모의 PK명 그대로 사용 | `portfolio_id`, `project_id` |
| 정렬 컬럼명 | `display_order` 고정 | `display_order` |
| 시간 컬럼명 | `created_at`, `updated_at`, `synced_at` | `updated_at` |
| 불리언 컬럼명 | `is_` 접두사 사용 | `is_current` |
| 암호화 컬럼명 | `*_enc/*_iv/*_tag/*_key_version` 세트 | `email_enc`, `email_iv` |

## 3) 용어 표준 매핑
| 비표준/구표현 | 표준 표현 | 비고 |
|---|---|---|
| `id` (PK 단일명) | `table_name_id` | 논리 모델 기본 규칙 |
| `period` | `start_date`, `end_date`, `is_current` | API 응답 시 `period` 파생 가능 |
| `order` | `display_order` | 정렬 의미 충돌 방지 |
| `profile info` | `profile` | 엔티티명과 일치 |
| `links` 테이블 | `project_link` | 실제 테이블명 사용 |

## 4) 문서 표기 규칙
- 테이블/컬럼/제약명은 항상 백틱으로 감싼다.
  - 예: `` `portfolio_id` ``, `` `UNIQUE(portfolio_id, display_order)` ``
- 관계는 IE 표기법 용어를 사용한다.
  - `||` = exactly one
  - `o{` = zero or many
- 제약조건 서술은 SQL 표현 그대로 쓴다.
  - 예: `CHECK (btrim(title) <> '')`
- 애매한 자연어 대신 명시적 규칙어를 사용한다.
  - 권장: `필수`, `선택`, `UNIQUE`, `NOT NULL`, `ON DELETE CASCADE`
  - 비권장: `대충`, `보통`, `가능하면`

## 5) 약어 사용 규칙
| 약어 | 사용 여부 | 규칙 |
|---|---|---|
| `PK`, `FK`, `UK` | 허용 | 문서 전반에서 일관 사용 |
| `PII` | 허용 | 최초 1회 `개인식별정보(PII)` 병기 |
| `DDL`, `ERD`, `DBML` | 허용 | 문서 제목/섹션명/요약에서 사용 |
| 임의 축약 | 금지 | 합의 전 사용 금지 |

## 6) 금지 규칙
- 동일 대상에 대해 두 개 이상의 이름 혼용 금지
  - 예: `project_id`와 `projectId` 동시 사용 금지
- DB 객체명에 `camelCase` 사용 금지
- 공백/대문자/한글 테이블명 사용 금지
- ERD와 DDL 간 컬럼명 불일치 금지

## 7) 예시
### 좋은 예시
- `experience` 테이블 기간 컬럼: `start_date`, `end_date`, `is_current`
- `project_link` 제약: `CHECK ((github_url IS NOT NULL AND btrim(github_url) <> '') OR (demo_url IS NOT NULL AND btrim(demo_url) <> ''))`

### 나쁜 예시
- `experience.period`를 DB에 단일 문자열로 저장하면서 동시에 날짜 컬럼도 병행
- 문서에서는 `id`, DDL에서는 `portfolio_id`를 혼용

## 8) 변경 절차
- 용어 변경 필요 시 먼저 `docs/issue-5-glossary.md`를 갱신한다.
- 이후 `docs/issue-5-db-schema-design.md` ERD/테이블 설계/매핑 섹션을 동기화한다.
- 마지막으로 DDL/DBML/ERD 자산 파일 순으로 반영한다.

