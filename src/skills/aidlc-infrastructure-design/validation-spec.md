# Infrastructure Design — Validation Spec

## 입력

- Artifacts: `infrastructure-design.md`, `deployment-architecture.md`
- Answered question file: `infrastructure-design-questions.md`
- Upstream: `nfr-requirements.md`, `tech-stack-decisions.md`, `components.md`, `component-dependencies.md`, `services.md`, `cross-cutting.md`, `units-of-work.md`, `units-of-work-dependency.md`
- Upstream (if present): `nfr-design-patterns.md`, `logical-components.md`, `business-logic-model.md`, `domain-entities.md`

## 규칙

1. 두 artifact(`infrastructure-design.md`, `deployment-architecture.md`)가 모두 존재해야 하며 비어 있지 않아야 합니다.
2. 인프라가 필요한 모든 logical component(이 unit 범위로 한정된 `logical-components.md` 또는 `components.md`로부터)는 `infrastructure-design.md`에 항목이 있어야 합니다. 매핑되지 않은 컴포넌트가 있어서는 안 됩니다.
3. 선택된 모든 인프라 서비스는 `tech-stack-decisions.md`와 일관되어야 합니다. 결정이 "PostgreSQL"이라면 인프라가 다른 데이터베이스 엔진으로 매핑되어서는 안 됩니다.
4. `nfr-requirements.md`의 NFR 요구사항 중 인프라에 영향을 주는 모든 항목(성능 목표, 가용성, 확장성)은 `infrastructure-design.md` 또는 `deployment-architecture.md`의 구체적 메커니즘으로 다루어져야 합니다. 다루지 않은 NFR은 플래그 처리해야 합니다.
5. `deployment-architecture.md`의 scaling 전략은 `nfr-requirements.md`의 확장성 요구사항을 참조해야 하며, 구체적인 trigger, threshold, capacity 한계를 포함해야 합니다.
6. failover와 recovery 메커니즘은 `nfr-requirements.md`에 정의된 RTO/RPO 목표에 매핑되어야 합니다. 메커니즘이 목표를 충족할 수 없다면 해당 갭이 문서화되어야 합니다.
7. 보안 인프라(암호화, IAM, secrets)는 `nfr-requirements.md`의 보안 요구사항 및 `cross-cutting.md`의 패턴과 일관되어야 합니다.
8. unit 간 연결성은 `units-of-work-dependency.md`와 일관되어야 합니다 — 이 unit과 다른 unit 간의 모든 의존성은 그에 상응하는 인프라 수준의 연결 메커니즘을 가져야 합니다.
9. 플랫폼 가정은 명시적으로 기술되어야 합니다. 문서화되지 않은 인프라에 대한 암묵적 의존은 허용되지 않습니다.
10. 모든 인프라 선택은 구체적이고 배포 가능해야 합니다 — "어떤 데이터베이스" 또는 "어떤 큐"와 같은 추상적 placeholder는 허용되지 않습니다. 구체적인 서비스 이름, 버전, 설정이 필요합니다.
11. 각 인프라 서비스에 대한 비용 추정이 존재해야 합니다. 추정값은 근사치여도 되지만 `nfr-requirements.md`의 예상 부하를 참조해야 합니다.
12. `deployment-architecture.md`의 infrastructure-as-code 노트는 특정 IaC 도구를 권장해야 하며 module/stack 경계를 기술해야 합니다. 이 권장은 `tech-stack-decisions.md`와 일관되어야 합니다.
