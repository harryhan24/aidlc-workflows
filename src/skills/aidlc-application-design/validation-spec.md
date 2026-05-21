# Application Design — Validation Spec

## 입력

- Artifacts (always-on): `components.md`, `component-methods.md`, `component-dependencies.md`, `services.md`, `cross-cutting.md`
- Artifacts (conditional, if present): `data-models.md`, `api-contracts.md`, `event-catalog.md`, `external-dependencies.md`
- Answered question file: `application-design-questions.md`
- Upstream: `requirements.md`, `stories.md`, `personas.md`

## 규칙

1. 네 개의 always-on artifact(`components.md`, `component-methods.md`, `component-dependencies.md`, `services.md`)와 `cross-cutting.md`가 모두 존재해야 하며 비어 있지 않아야 합니다.
2. 조건부 artifact는 해당될 때 존재해야 합니다: 영속성이 있다면 `data-models.md`, 시스템이 API를 노출한다면 `api-contracts.md`, 시스템이 event-driven이라면 `event-catalog.md`, 외부 통합이 있다면 `external-dependencies.md`. 조건부 artifact를 생략한다면 그 사유를 `components.md`에 명시해야 합니다.
3. `components.md`의 모든 컴포넌트는 최소 하나의 메서드와 함께 `component-methods.md`에 등장해야 하며, `component-dependencies.md`에도 등장해야 합니다.
4. `services.md`의 모든 서비스는 `components.md`의 최소 하나의 컴포넌트를 참조해야 합니다.
5. `stories.md`의 모든 story는 최소 하나의 서비스, 컴포넌트, API, 또는 이벤트로 다루어질 수 있어야 합니다. 매핑되지 않은 story는 사유와 함께 플래그 처리해야 합니다.
6. `data-models.md`의 모든 엔티티는 `components.md`에 나열된 정확히 하나의 소유 컴포넌트를 가져야 합니다. 두 컴포넌트가 동일한 엔티티를 소유할 수 없습니다.
7. `api-contracts.md`의 모든 API는 `cross-cutting.md`에 정의된 오류 형식을 사용해야 합니다.
8. `event-catalog.md`의 모든 이벤트는 컴포넌트, 서비스, 또는 외부 시스템에 매핑된 최소 하나의 producer와 최소 하나의 consumer를 가져야 합니다.
9. `external-dependencies.md`의 모든 외부 의존성은 컴포넌트 또는 서비스에 매핑된 최소 하나의 consumer를 가져야 합니다.
10. 모든 artifact는 logical 동작을 기술해야 합니다 — 언어, 프레임워크, 데이터베이스, 프로토콜, broker, 벤더 특정 사항은 허용되지 않습니다.
11. 컴포넌트 간 순환 의존성은 `component-dependencies.md`에 명시적인 정당화와 함께 나열되어야 합니다.
