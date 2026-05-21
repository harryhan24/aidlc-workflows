# NFR Assessment — Validation Spec

## 입력

- Artifacts: `nfr-requirements.md`, `tech-stack-decisions.md`
- Answered question file: `nfr-assessment-questions.md`
- Upstream: `requirements.md`, `business-logic-model.md`, `domain-entities.md`, `business-rules.md`, `components.md`, `component-methods.md`, `component-dependencies.md`, `cross-cutting.md`, `units-of-work.md`, `units-of-work-story-map.md`

## 규칙

1. 두 artifact가 모두 존재해야 하며 비어 있지 않아야 합니다.
2. `nfr-requirements.md`는 unit 범위를 선언해야 합니다 — unit 이름, 소유 컴포넌트, 기능적 복잡도 요약을 포함합니다.
3. `requirements.md`에 정의된 NFR 중 이 unit과 관련된 모든 항목은 `nfr-requirements.md`에서 다루어져야 합니다. 다루지 않은 NFR은 사유와 함께 플래그 처리해야 합니다(예: 이 unit에 적용되지 않음).
4. 성능 요구사항은 upstream `business-logic-model.md`의 구체적인 비즈니스 워크플로를 참조해야 합니다. 임의로 만들어낸 작업은 허용되지 않습니다.
5. 신뢰성 요구사항은 upstream `business-logic-model.md`에 선언된 모든 통합 접점(integration touchpoint)에 대해 의존성 장애 처리 방안을 다루어야 합니다.
6. 보안 요구사항은 upstream `domain-entities.md`의 모든 엔티티에 대해 데이터 분류(data classification)를 다루어야 합니다.
7. 관측성(observability) 요구사항은 upstream `cross-cutting.md`에 정의된 로깅 taxonomy를 확장해야 하며, 모순되어서는 안 됩니다.
8. 모든 NFR 요구사항은 `requirements.md`의 식별자를 역추적할 수 있는 NFR traceability 참조를 포함해야 합니다.
9. 모든 tech stack 결정은 고유한 `TSD-<n>` ID, 카테고리, 선택, 검토한 대안, 특정 NFR 요구사항을 참조하는 근거, 문서화된 trade-off를 가져야 합니다.
10. tech stack 결정 간에 서로 모순되어서는 안 됩니다. 두 결정이 긴장 관계를 만든다면(예: 지연 시간 요구사항과 충돌하는 데이터베이스 선택), 해당 긴장을 해결 방안과 함께 문서화해야 합니다.
11. tech stack 결정은 upstream `cross-cutting.md`에 정의된 제약(constraint)이나 표준(standard)과 모순되어서는 안 됩니다.
