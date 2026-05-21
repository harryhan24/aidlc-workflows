# Functional Design — Validation Spec

## 입력

- Artifacts: `business-logic-model.md`, `domain-entities.md`, `business-rules.md`
- Answered question file: `functional-design-questions.md`
- Upstream: `stories.md`, `units-of-work.md`, `units-of-work-story-map.md`, `components.md`, `component-methods.md`, `component-dependencies.md`
- Upstream (if present): `data-models.md`, `event-catalog.md`

## 규칙

1. 세 artifact가 모두 존재해야 하며 비어 있지 않아야 합니다.
2. `business-logic-model.md`는 unit 범위를 선언해야 합니다 — unit 이름, 이 unit에 매핑된 story(`units-of-work-story-map.md` 기준), 소유 컴포넌트를 포함합니다. 이 unit에 매핑된 모든 story는 unit 범위에 나타나야 합니다.
3. 이 unit에 매핑된 모든 story는 최소 하나의 비즈니스 워크플로로 다루어져야 합니다. 다루지 않은 story는 사유와 함께 플래그 처리해야 합니다.
4. 비즈니스 워크플로에서 참조하는 모든 컴포넌트 메서드는 upstream `component-methods.md`에 존재해야 합니다. 임의로 만들어낸 메서드는 허용되지 않습니다.
5. `business-logic-model.md`의 모든 도메인 이벤트는 upstream `event-catalog.md`(있는 경우)와 일관되어야 합니다 — 이름, producer, consumer가 일치해야 합니다. 카탈로그에 없는 신규 이벤트는 추가 항목으로 플래그 처리해야 합니다.
6. `domain-entities.md`의 모든 엔티티는 upstream `data-models.md`(있는 경우)의 동일한 소유 컴포넌트를 가진 엔티티로 추적되어야 합니다. upstream에 없는 신규 엔티티는 추가 항목으로 플래그 처리해야 합니다.
7. 모든 엔티티는 최소 하나의 attribute, 정의된 lifecycle(또는 stateful하지 않음을 명시), 최소 하나의 invariant(또는 적용되지 않음을 명시)를 가져야 합니다.
8. 복잡한 상태 lifecycle을 가진 엔티티는 state machine 정의를 포함해야 합니다 — 모든 상태, 유효한 전이, guard 조건, entry/exit 동작을 포함합니다. 암묵적 전이는 허용되지 않습니다.
9. 모든 business rule은 고유한 `BR-<n>` ID, 정의된 type, trigger, 구현 가능할 만큼 정밀한 선언적 로직, 위반 동작을 가져야 합니다.
10. 모든 business rule은 `Stories` 필드를 통해 최소 하나의 story로 추적되어야 합니다. 이 unit에 매핑된 모든 story는 최소 하나의 business rule로 커버되어야 합니다.
11. business rule은 서로 모순되어서는 안 됩니다. 두 규칙이 동일한 trigger에 적용되어 충돌하는 결과를 만든다면 그 충돌은 해결 전략과 함께 문서화되어야 합니다.
12. 다수의 변수와 결과가 얽힌 복잡한 조건 로직을 가진 규칙은 산문 대신 decision table 형식을 사용해야 합니다.
13. 모든 artifact는 비즈니스 로직만 기술해야 합니다 — 언어, 프레임워크, 데이터베이스, 프로토콜, broker, 벤더 특정 사항은 허용되지 않습니다.
14. `business-logic-model.md`의 통합 접점은 upstream `component-dependencies.md`에 선언된 의존성을 참조해야 합니다. 선언되지 않은 cross-boundary 상호작용은 허용되지 않습니다.
