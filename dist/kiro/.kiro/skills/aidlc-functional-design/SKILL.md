---
name: aidlc-functional-design
description: |
  AI-DLC functional-design skill. 승인된 application-design 및 units-of-work artifact를 기반으로 단일 construction unit에 대한 `business-logic-model.md`, `domain-entities.md`, `business-rules.md`를 생성합니다. 기술 중립적입니다.

  모든 upstream artifact가 존재하고 이 단계만 필요할 때 `/skill aidlc-functional-design --unit <unit>`으로 명시적으로 호출합니다. 새로운 개발 intent의 경우에는 `aidlc-orchestrator`를 활성화합니다.
metadata:
  phase: construction
  stage: functional-design
  per-unit: "true"
  human-clarification: "true"
  plan-creation: "true"
  plan-verification: "true"
  artefact-verification: "true"
---

# Functional Design

도메인 주도 설계 원칙을 사용해 unit에 대한 상세한 비즈니스 로직을 설계합니다 — 도메인 엔티티, 비즈니스 규칙, 상태 라이프사이클, 그리고 unit이 매핑된 story를 충족하는 방식을 규율하는 행동 모델을 정의합니다. 엄격하게 기술 중립적입니다. 구현 방법이 아니라 unit이 무엇을 하는지를 비즈니스 용어로 포착합니다.

이 skill은 application design을 정교화하고 심화합니다. application design이 시스템 전반 수준에서 component 경계, method, 의존성을 확립했다면, functional design은 단일 unit으로 줌인하여 추가 명확화 없이 구현 가능할 정도의 정밀도로 내부 비즈니스 로직을 모델링합니다.

## Prerequisites

- application design artifact가 승인되어 있어야 합니다(`components.md`, `component-methods.md`, `component-dependencies.md`, `services.md`, `cross-cutting.md`, 그리고 모든 조건부 artifact)
- units generation이 완료되어야 합니다 — `units-of-work.md`와 `units-of-work-story-map.md`가 사용 가능해야 합니다
- `stories.md`가 승인되어야 합니다

## Input

- Unit 컨텍스트: `units-of-work.md`, `units-of-work-story-map.md`
- application design artifact: `components.md`, `component-methods.md`, `component-dependencies.md`, `services.md`, `cross-cutting.md`
- 존재한다면 조건부 application design artifact: `data-models.md`, `api-contracts.md`, `event-catalog.md`, `external-dependencies.md`
- `stories.md`

## Question Guidance

unit 정의, story 매핑, application design artifact를 먼저 분석합니다. 도출 가능한 것은 도출하고, functional design의 품질에 실질적 영향을 줄 만한 진정한 모호성이 남아 있을 때만 질문합니다. 다음 항목에 집중합니다:

- **Domain entities**: 이 unit의 component에 할당된 entity(`data-models.md` 기준)가 완전한가? application design이 추상적으로 남긴 누락된 속성, 관계, 라이프사이클 규칙이 있는가?
- **Business rules**: 어떤 의사결정 로직, 검증 규칙, 제약이 unit의 동작을 규율하는가? 어떤 규칙이 hard constraint이고 어떤 것이 soft constraint(선호, 기본값)인가?
- **State transitions**: 라이프사이클 상태를 가진 entity가 있는가? 전이 트리거는 무엇이며 각 상태에서 유지되어야 할 불변식은 무엇인가? 종료 상태가 있는가?
- **Business workflows**: 이 unit에 할당된 service와 component method가 어떻게 end-to-end 비즈니스 흐름으로 결합되는가? happy path와 예외 경로는 무엇인가?
- **Edge cases and error scenarios**: 입력이 잘못되거나, 의존성이 사용 불가하거나, 비즈니스 규칙이 충돌할 때 어떻게 되는가? 경계 조건은 무엇인가?
- **Calculations and algorithms**: 정확하게 정의되어야 할 공식, scoring 모델, 랭킹 알고리즘, 변환 로직이 있는가?
- **Domain events**: 이 unit이 생산하거나 소비하는 도메인적으로 의미 있는 이벤트는 무엇인가? 트리거와 결과는 무엇인가?
- **Concurrency and contention**: 여러 액터가 동시에 같은 entity를 수정할 수 있을 때, 충돌 해결 규칙은 무엇인가?
- **Data derivation and aggregation**: 계산된 또는 파생된 필드가 있는가? 원천 필드와 계산 규칙은 무엇인가? 언제 재계산되는가?
- **Temporal rules**: 시간 의존적인 비즈니스 규칙이 있는가 — 만료, 스케줄링, 시간 윈도우, SLA, grace period?
- **Multi-tenancy** (해당하는 경우): 테넌트 격리가 비즈니스 규칙과 entity 경계에 어떻게 영향을 주는가?
- **Brownfield considerations** (해당하는 경우): 보존되어야 할 기존 비즈니스 규칙은 무엇인가? 포착되어야 할 문서화되지 않은 동작이 있는가?

## Output

세 가지 artifact입니다.

### business-logic-model.md

unit의 행동 모델:

- **Unit scope** — unit 이름, 이 unit에 매핑된 story(`units-of-work-story-map.md`의 `S-<n>` ID), 소유 component
- **Business workflows** — 이 unit에 할당된 service operation별로: 단계별 흐름, 의사결정 지점, 분기 로직, 에러 처리. `component-methods.md`의 component method를 참조합니다.
- **Domain events** — 이 unit scope 내에서 생산되고 소비되는 이벤트와, 트리거, payload 설명, downstream 결과. `event-catalog.md`가 존재한다면 일관되어야 합니다.
- **Integration touchpoints** — 이 unit의 workflow가 경계 밖의 component나 service와 상호작용하는 지점. 통신 패턴은 `component-dependencies.md`를 참조하세요.

### domain-entities.md

이 unit의 component가 소유한 모든 entity에 대한 상세 정의. `data-models.md`를 정교화하고 확장합니다:

- **Entity name** — `data-models.md`에서
- **Owning component** — `components.md`에서
- **Attributes** — 논리 타입, 선택성, 기본값을 포함한 완전한 목록. 계산 규칙이 포함된 derived/computed 속성도 포함합니다.
- **Relationships** — 다른 entity와의 관계, cardinality 및 navigation 방향 포함
- **Invariants** — entity에 대해 항상 참이어야 하는 조건
- **Lifecycle** — 상태(stateful이라면), 전이, 트리거, 각 전이의 guard. 복잡한 상태 라이프사이클을 가진 entity의 경우 상태 머신 정의를 포함합니다: 모든 상태, 유효한 전이, guard 조건, entry/exit 액션을 열거합니다. stateful이 아니라면 명시적으로 그렇게 진술합니다.
- **Constraints** — 고유성, 참조 무결성, 비즈니스 규칙에서 파생된 제약
- **Concurrency** — 동시 수정이 가능할 때의 충돌 해결 전략(해당하는 경우)

### business-rules.md

모든 비즈니스 규칙의 명시적 카탈로그:

- **Rule ID** — `BR-<n>`, unit 내에서 순차적으로 부여
- **Name** — 짧은 서술형 이름
- **Description** — 규칙이 강제하거나 결정하는 내용
- **Type** — constraint, validation, calculation, authorisation, state-transition 중 하나
- **Trigger** — 규칙이 평가되는 시점
- **Logic** — 선언적 표현(if-then, 공식, 표, 의사결정 트리). 여러 변수와 결과가 얽힌 복잡한 조건 로직을 가진 규칙은 decision table 형식을 사용해야 합니다.
- **Violation behaviour** — 규칙이 위반되었을 때 어떻게 되는가
- **Stories** — 이 규칙이 뒷받침하는 `S-<n>` ID, `units-of-work-story-map.md`를 통해 추적 가능

## Validation

이 skill의 출력에 대한 검증 규칙은 skill 루트의 `validation-spec.md`에 있습니다. 적용 방식은 `aidlc-common/protocols/aidlc-validator-protocol.md`를 참조하세요.
