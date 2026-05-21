# Reverse Engineering — Validation Spec

## 입력

- Artifacts (always-on): `components.md`, `component-methods.md`, `component-dependencies.md`, `services.md`, `cross-cutting.md`
- Artifacts (conditional, if present): `data-models.md`, `api-contracts.md`, `event-catalog.md`, `external-dependencies.md`
- Artifacts (RE-specific): `technology-stack.md`, `code-structure.md`, `code-quality-assessment.md`
- Artifacts (per-chunk, if applicable): `chunks/<chunk-name>.md`
- Answered question file: `reverse-engineering-questions.md`
- Upstream: `requirements.md` (가능한 경우), 대상 codebase(파일 시스템 접근)

## 규칙

1. forward-flow에 대응하는 모든 artifact(`components.md`, `component-methods.md`, `component-dependencies.md`, `services.md`, `cross-cutting.md`)가 존재해야 하며 비어 있지 않아야 합니다.
2. 조건부 artifact는 해당될 때 존재해야 합니다: 영속성이 있다면 `data-models.md`, API가 존재한다면 `api-contracts.md`, event-driven이라면 `event-catalog.md`, 외부 통합이 있다면 `external-dependencies.md`.
3. RE-specific artifact(`technology-stack.md`, `code-structure.md`, `code-quality-assessment.md`)는 존재해야 하며 비어 있지 않아야 합니다.
4. `components.md`의 모든 컴포넌트는 실제 소스 파일 경로로 추적 가능해야 합니다. 환각으로 만들어낸 컴포넌트는 허용되지 않습니다.
5. `component-dependencies.md`의 모든 의존성은 `components.md`에 존재하는 컴포넌트를 참조해야 합니다.
6. `data-models.md`의 모든 엔티티는 `components.md`에 정확히 하나의 소유 컴포넌트를 가져야 합니다.
7. 중대형 codebase의 경우, per-chunk artifact가 `chunks/`에 존재해야 하며 통합 artifact는 이들과 일관되어야 합니다.
8. RE artifact는 존재하는 것을 기술합니다 — 설계 권장, 미래 상태 제안, 리팩토링 제안은 허용되지 않습니다.
9. `technology-stack.md`는 빌드 파일(package.json, pom.xml, go.mod 등)에서 발견 가능한 실제 버전을 나열해야 하며, 추측한 버전이 아니어야 합니다.
10. 어떠한 소스 코드도 수정되어서는 안 됩니다. RE는 read-only 분석입니다.
