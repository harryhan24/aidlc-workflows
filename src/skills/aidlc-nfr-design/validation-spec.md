# NFR Design — Validation Spec

## 입력

- Artifacts: `nfr-design-patterns.md`, `logical-components.md`
- Answered question file: `nfr-design-questions.md`
- Upstream: `nfr-requirements.md`, `tech-stack-decisions.md`, `business-logic-model.md`, `domain-entities.md`, `components.md`, `component-methods.md`, `component-dependencies.md`, `cross-cutting.md`
- Upstream (if present): `api-contracts.md`, `event-catalog.md`, `external-dependencies.md`

## 규칙

1. 두 artifact(`nfr-design-patterns.md`, `logical-components.md`)가 모두 존재해야 하며 비어 있지 않아야 합니다.
2. `nfr-requirements.md`의 NFR 요구사항 중 design pattern 또는 logical component를 함의하는 모든 항목은 `nfr-design-patterns.md`의 최소 하나의 패턴 또는 `logical-components.md`의 최소 하나의 컴포넌트로 다루어져야 합니다. 다루지 않은 NFR은 사유와 함께 플래그 처리해야 합니다.
3. 모든 패턴은 `nfr-requirements.md`의 특정 NFR 요구사항 ID를 참조해야 합니다. traceability가 없는 패턴은 허용되지 않습니다.
4. 모든 패턴은 upstream `components.md` 또는 `business-logic-model.md`에 존재하는 특정 컴포넌트, 서비스, 또는 통합 접점에 적용되어야 합니다. 임의로 만들어낸 적용 지점은 허용되지 않습니다.
5. 동일 컴포넌트에 적용된 패턴은 상호 작용과 순서를 문서화해야 합니다(예: retry가 circuit breaker 앞인지 뒤인지). 해결 전략 없이 동일 컴포넌트에 충돌하는 패턴이 적용되면 실패입니다.
6. `logical-components.md`의 모든 logical component는 upstream `components.md` 또는 `component-dependencies.md`를 참조하여 컴포넌트 topology 상에서 명확한 위치를 가져야 합니다.
7. 모든 logical component는 failure mode를 명시해야 합니다. failure mode 문서화가 없는 컴포넌트는 실패입니다.
8. 패턴의 configuration 파라미터는 NFR 목표에서 도출된 권장 기본값을 포함해야 합니다(예: "timeout: 3s based on p95 target of 5s"). 근거 없는 파라미터는 실패입니다.
9. 모든 artifact는 logical design만 기술해야 합니다 — 벤더 특정 서비스 이름, cloud provider 참조, infrastructure-tier 세부사항은 허용되지 않습니다. 이는 infrastructure-design의 영역입니다.
10. 패턴과 컴포넌트는 upstream `cross-cutting.md`에 정의된 표준과 모순되어서는 안 됩니다. 확장은 허용되지만 모순은 허용되지 않습니다.
11. design은 `nfr-requirements.md`로 추적 가능한 범위를 넘어서는 패턴이나 컴포넌트를 도입해서는 안 됩니다. NFR 근거가 없는 over-engineering은 실패입니다.
