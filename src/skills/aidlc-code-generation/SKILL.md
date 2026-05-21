---
name: aidlc-code-generation
description: |
  AI-DLC code-generation skill. 작고 테스트 가능한 layer 단위로 운영 수준의 코드를 생성합니다 — model, business logic, API, integration. layer마다 빌드 검증과 checkpoint 추적이 이루어집니다. 재호출을 전제로 설계되어 있으며, 상태는 plan의 layer별 checkbox로 추적됩니다.

  모든 upstream 설계 artifact가 존재하고 이 단계만 필요할 때 `/skill aidlc-code-generation --unit <unit>`으로 명시적으로 호출합니다. 새로운 개발 intent의 경우에는 `aidlc-orchestrator`를 활성화합니다.
metadata:
  phase: construction
  stage: code-generation
  per-unit: "true"
  human-clarification: "true"
  plan-creation: "true"
  plan-verification: "true"
  artefact-verification: "true"
---

# Code Generation

layer 기반 접근법에 따라 작고 테스트 가능한 단위로 운영 수준의 코드를 생성합니다. 각 layer는 빌드되고, 테스트되고, 검증된 후에야 다음 layer로 진행됩니다. 재호출을 전제로 설계되어 있으며 — 상태는 plan의 layer별 checkbox로 추적됩니다.

## Prerequisites

- application design artifact가 승인되어 있어야 합니다(`inception/application-design/` 또는 `inception/reverse-engineering/`에서)
- (해당하는 경우) unit design / functional design이 승인되어 있어야 합니다
- brownfield의 경우: 코딩 컨벤션 추출을 위해 reverse-engineering artifact가 사용 가능해야 합니다

## Input

- `components.md`, `component-methods.md`, `component-dependencies.md`, `services.md`
- (해당하는 경우) `cross-cutting.md`, `data-models.md`, `api-contracts.md`
- `units-of-work.md`와 unit 할당(per-unit 실행인 경우)
- (해당하는 경우) `functional-design/` artifact — 구현할 비즈니스 로직과 규칙
- brownfield의 경우: reverse-engineering의 `technology-stack.md`, `code-structure.md`와 대상 codebase

## Layered Chunking Strategy

code generation은 layer 단위로 진행됩니다. 각 layer는 독립적으로 빌드되고 테스트될 수 있는 관련 파일들의 작은 묶음입니다.

### Layer ordering (아키텍처 스타일에 맞게 조정)

**Layered / MVC:**
1. Models — entity, DTO, enum, migration
2. Business Logic — service, validator, domain logic + 유닛 테스트
3. API — controller, route, middleware + API 테스트
4. Integration — 외부 client, event publisher, 구성

**Hexagonal / Clean / Ports-and-Adapters:**
1. Domain Core — entity, value object, domain service, port interface
2. Application — use case, DTO, application service
3. Inbound Adapters — controller, event listener, CLI handler
4. Outbound Adapters — repository 구현, 외부 client
5. Configuration — DI wiring, env 구성, migration

**Frontend:**
1. Foundation — layout, navigation, routing, design system 셋업
2. Pages — 데이터 fetching을 가진 page component
3. Features — feature component, 상태 관리, form
4. Shared — 재사용 component, 유틸리티, API client hook

### Layer rules

- 각 layer: 최대 5–8개 파일. 더 많다면 sub-layer로 분할(2a, 2b).
- 유닛 테스트는 layer와 함께 배치합니다 — 마지막으로 미루지 않습니다.
- 각 layer는 독립적으로 컴파일되어야 합니다 — 아직 생성되지 않은 layer에 대한 forward reference 금지.

## Question Guidance

다음 항목을 중심으로 명확화 질문을 집중합니다:
- component 순서가 모호한 경우 구현 우선순위
- `cross-cutting.md`에 명시되지 않은 경우의 에러 처리 접근
- 필요한 로깅 및 observability 수준
- brownfield의 경우: 추출된 코딩 컨벤션이 정확한지 확인

아키텍처, 기술 스택, component 구조, 비즈니스 로직에 대해서는 묻지 마세요 — 이들은 앞선 artifact에서 결정됩니다.

## Execution Model

### 매 호출마다:
1. plan을 읽습니다. ✅ 표시가 없는 첫 번째 layer를 찾습니다.
2. 부분적으로 완료된 layer의 경우, 체크된 파일이 디스크에 존재하는지 확인합니다. 누락된 것은 재생성합니다.
3. 현재 layer에 대해 체크되지 않은 파일을 생성합니다.
4. 현재 layer에 대해 빌드 및 테스트를 실행합니다.
5. 통과 시: layer를 ✅로 표시하고 사람에게 제시합니다.
6. 컴파일 실패 시: 최대 3회까지 자가 교정을 시도한 뒤 멈추고 에러를 제시합니다.
7. 테스트/로직 실패 시: 즉시 멈추고 사람에게 제시합니다.

### Brownfield-specific:
- 어떤 코드도 생성하기 전에 기존 파일에서 코딩 컨벤션을 추출합니다.
- 수정 중인 각 파일에 대해: 현재 내용을 읽고, diff 요약을 제시하고, 작성 전에 승인을 받습니다.
- 수정 전 기준선을 확보하기 위해 기존 테스트 스위트를 실행합니다.

## Output

### Plan artifact
- **code-generation-plan.md** — layer별로 그룹화된 파일별 checkbox를 가진 layer 기반 plan. 각 layer는 다음을 가집니다: 이름, 파일 목록(액션, 목적, story traceability), 검증 기준, checkpoint 마커.

### Code artifacts
- workspace 내 애플리케이션 소스 코드(aidlc-docs/ 안이 아님)
- layer별로 함께 배치된 테스트

### Documentation artifacts
- **code-generation-plan.md** — 진행 상태가 반영된 plan
- **CODE_SUMMARY.md** — 생성된 내용, 내려진 결정, 따른 컨벤션의 요약

## Validation

이 skill의 출력에 대한 검증 규칙은 skill 루트의 `validation-spec.md`에 있습니다. 적용 방식은 `aidlc-common/protocols/aidlc-validator-protocol.md`를 참조하세요.
