# Portfolio API DB 컨텍스트

## 스택 요약
- 런타임: Java 21, Spring Boot `4.0.3`, Spring Data JPA
- DB: PostgreSQL (JDBC 드라이버 설정됨)
- 현재 스키마 동기화: `spring.jpa.hibernate.ddl-auto=update`
- 타임존 처리: `hibernate.jdbc.time_zone=UTC`

## 우선 확인 파일
- `docs/api-spec.md`
- `docs/issue-5-db-schema-design.md`
- `src/main/resources/application.properties`
- `src/main/java/com/devchan/portfolio/api/domain/entity/*.java`

## 현재 관계형 모델 요약
- 루트 집합: `portfolio`
- 1:1
- `portfolio -> profile`
- `portfolio -> contact`
- `project -> project_link`
- 1:N
- `portfolio -> nav_item`
- `portfolio -> skill_group -> skill_item`
- `portfolio -> project -> project_stack`
- `portfolio -> project -> project_detail`
- `portfolio -> experience -> experience_achievement`
- `profile -> profile_bio`

## 현재 제약 패턴
- 하위 테이블은 부모 FK 기반으로 연결되어 있다.
- 배열 순서는 `display_order`로 관리한다.
- `CHECK(display_order >= 0)`
- `UNIQUE(parent_id, display_order)`
- 도메인 유니크 제약 예시:
- `uk_portfolio_key`
- `uk_nav_item_portfolio_anchor`
- `uk_project_portfolio_title`
- 민감정보 컬럼은 암호화 페이로드 패턴을 사용한다.
- `*_enc`, `*_iv`, `*_tag`, `*_key_version`
- IV/Tag 길이는 `@Check`로 강제된다.
- 공통 시간 컬럼은 `BaseTimeEntity`에서 관리한다.
- `created_at`, `updated_at` (`timestamptz`)

## 아키텍처 리스크
- Flyway/Liquibase가 아직 설정되지 않았다.
- `ddl-auto=update`는 로컬 개발에는 편하지만 운영 드리프트 리스크가 크다.
- 인덱스는 현재 PK/UNIQUE 중심이며, 실쿼리 기반 보강이 필요하다.

## 기본 권장 결정
- 계약 우선: API 계약 변경과 스키마 변경을 함께 관리한다.
- 운영 환경은 JPA 자동 변경보다 명시적 마이그레이션을 우선한다.
- 제약조건 이름을 고정해 운영/롤백 추적성을 높인다.
- 민감정보 신규 필드는 키 버전 컬럼을 필수로 둔다.
