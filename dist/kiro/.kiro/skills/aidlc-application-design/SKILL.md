---
name: aidlc-application-design
description: |
  AI-DLC application-design skill. 승인된 requirements와 story로부터 논리적 component 구조 — component, method, dependency, service, cross-cutting 표준, 그리고 persistence·API·event·외부 통합을 위한 조건부 artifact — 를 생성합니다.

  requirements와 story가 이미 작성되어 있고 이 단계만 필요할 때 `/skill aidlc-application-design`으로 명시적으로 호출하십시오. 신규 개발 intent의 경우 대신 `aidlc-orchestrator`를 활성화하십시오.
metadata:
  phase: inception
  stage: application-design
  per-unit: "false"
  human-clarification: "true"
  plan-creation: "true"
  plan-verification: "true"
  artefact-verification: "true"
---

# Application Design

시스템의 논리적 component 구조 — component, 그 method, 그 dependency, 그리고 이들을 오케스트레이션하는 service — 를 설계합니다. 기술 선택과 무관하게 component 수준에서 무엇이 빌드될지를 포착합니다.

## Prerequisites

- `requirements.md`가 승인되어 있어야 합니다
- `stories.md`와 `personas.md`가 승인되어 있어야 합니다
- wireframe이 실행된 경우: `screen-data-map.md`가 사용 가능해야 합니다(API 표면과 BFF 결정에 정보를 제공)

## Input

- `requirements.md`
- `stories.md`, `personas.md`
- `screen-data-map.md`(존재하는 경우 — wireframes skill로부터)

## Question Guidance

먼저 `requirements.md`와 `stories.md`를 분석하십시오. 도출 가능한 것은 도출하고, 진짜 모호함이 있을 때만 질문하십시오. 다음에 집중하십시오:

- Component 경계: 시스템을 논리적 component로 어떻게 분할할 것인가(도메인별, 기능별, 레이어별)
- Component 타입: stateful vs stateless, service vs library
- 통신 패턴(논리적 수준): 동기(request/response) vs 비동기(event/message)
- Cross-cutting 관심사: authorisation, logging, validation, error handling이 개념적으로 어디에 위치하는가
- 데이터 소유권: 각 도메인 엔티티를 어느 component가 소유하는가
- 통합 지점: 이 design이 인터페이스해야 할 외부 시스템, 논리적 수준에서
- Brownfield 영향(해당되는 경우): 어떤 기존 component가 영향을 받거나 확장되거나 대체되는가
- Frontend aggregation(`screen-data-map.md`가 존재하는 경우): 여러 component에서 데이터를 끌어오는 화면들은 BFF(Backend-for-Frontend) 레이어를 정당화할 수 있습니다. 선택지를 제시하십시오: frontend에서 직접 호출, BFF aggregation, 또는 client-side composition. 사람이 결정하게 하십시오 — 가정하지 마십시오.

## Output

항상 생성되는 4개의 artifact에 시스템 특성에 따른 조건부 artifact가 더해집니다.

### components.md

component당 한 섹션. 각 섹션에는 다음이 포함됩니다:

- **Name**
- **Purpose** — 이 component가 하는 일
- **Responsibilities** — 책임지는 사항
- **State** — stateful 또는 stateless
- **Owns** — 자신이 소유하는 도메인 엔티티(authoritative source of truth)

### component-methods.md

component별로 노출하는 method. 각 method에는 다음이 포함됩니다:

- **Name**
- **Inputs** — 언어 특화가 아닌 논리적 타입
- **Outputs** — 언어 특화가 아닌 논리적 타입
- **Preconditions** — 호출 전에 참이어야 하는 것
- **Postconditions** — 호출 후에 참이어야 하는 것

### component-dependencies.md

component 간 의존성 매트릭스:

- Component A → Component B: 통신 패턴(sync call, async event, shared state)
- 각 의존성의 근거
- 순환 의존성이 있다면 명시적인 정당화와 함께 모두 나열되어야 합니다

### services.md

component를 비즈니스 workflow로 조합하는 service 레이어 오케스트레이션. 각 service에는 다음이 포함됩니다:

- **Name**
- **Purpose** — 이 service가 오케스트레이션하는 비즈니스 workflow
- **Components used** — `components.md`에서 참조
- **Operations** — 이 service가 노출하는 workflow 단계
- **Stories addressed** — `stories.md`의 `S-<n>` ID들

### cross-cutting.md (항상)

이후의 모든 unit이 상속하는 시스템 전체 표준:

- **Error format** — 논리적 수준에서의 에러 응답/반환 형태
- **Authorisation model** — role, permission, policy 스타일(구현 아님)
- **Logging taxonomy** — 무엇이 로깅되는가, event 이름, severity 수준
- **Validation approach** — 입력 validation이 어디에서 일어나는가(edge, service, component)

### data-models.md (persistence가 있는 경우)

시스템이 소유하는 도메인 엔티티. 각 엔티티에는 다음이 포함됩니다:

- **Name**
- **Owning component** — `components.md`에서
- **Fields** — DB 특화가 아닌 논리적 타입
- **Relationships** — 다른 엔티티와의 관계(one-to-one, one-to-many, many-to-many)
- **Constraints** — 비즈니스 규칙, 유일성, 참조 무결성
- **Lifecycle** — 생성, 갱신, 삭제 규칙

### api-contracts.md (시스템이 API를 노출하는 경우)

논리적 API 표면. 각 API에는 다음이 포함됩니다:

- **Name** — 오퍼레이션 또는 리소스 이름
- **Purpose**
- **Inputs** — request 형태(논리적 타입)
- **Outputs** — response 형태(논리적 타입)
- **Errors** — 에러 코드와 조건(cross-cutting error format에서)
- **Consumers** — 이 API를 호출하는 주체

### event-catalog.md (event-driven인 경우)

시스템이 생산하거나 소비하는 event. 각 event에는 다음이 포함됩니다:

- **Name**
- **Purpose**
- **Producer** — component 또는 service
- **Consumers** — component, service, 또는 외부 시스템
- **Payload** — 논리적 타입
- **Delivery semantics** — at-least-once, at-most-once, exactly-once
- **Ordering requirements** — strict, partitioned, none

### external-dependencies.md (외부 통합이 있는 경우)

이 design이 의존하는 외부 시스템. 각각에 다음이 포함됩니다:

- **Name** — 논리적 이름
- **Purpose** — 이 시스템이 우리에게 해주는 것
- **Contract** — request/response 형태 또는 event 스키마(알려진 경우)
- **Failure mode** — timeout, 가용 불가, 또는 에러 시 예상 동작
- **Consumers** — 이 외부 시스템에 의존하는 component/service

## Validation

이 skill 출력에 대한 validation 규칙은 skill 루트의 `validation-spec.md`에 있습니다. 적용 방식은 `aidlc-common/protocols/aidlc-validator-protocol.md`를 참조하십시오.
