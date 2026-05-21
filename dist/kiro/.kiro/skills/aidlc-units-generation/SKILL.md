---
name: aidlc-units-generation
description: |
  AI-DLC units-generation skill. 시스템을 관리 가능한 unit으로 분해합니다 — 개발 목적의 component와 story의 논리적 그룹입니다. microservice의 경우 각 unit은 독립적으로 배포 가능한 service가 되며, monolith의 경우 단일 unit이 논리적 모듈을 가진 전체 애플리케이션을 나타냅니다.

  모든 upstream 설계 artifact가 존재하고 이 단계만 수행하면 될 때 `/skill aidlc-units-generation`으로 명시적으로 호출합니다. 새로운 개발 intent의 경우에는 `aidlc-orchestrator`를 활성화합니다.
metadata:
  phase: inception
  stage: units-generation
  per-unit: "false"
  human-clarification: "true"
  plan-creation: "true"
  plan-verification: "true"
  artefact-verification: "true"
---

# Units Generation

시스템을 관리 가능한 unit으로 분해합니다. unit은 개발 목적의 story의 논리적 그룹입니다. microservice의 경우 각 unit은 독립적으로 배포 가능한 service가 되며, monolith의 경우 단일 unit이 논리적 모듈을 가진 전체 애플리케이션을 나타냅니다. cross-cutting 관심사(인증, 로깅, 에러 처리)는 자체적인 unit을 형성하며, 다른 unit들이 이에 의존할 수 있도록 가장 먼저 구축됩니다.

## Prerequisites

- `requirements.md`가 승인되어야 합니다
- `stories.md`와 `personas.md`가 승인되어야 합니다
- `components.md`, `component-methods.md`, `component-dependencies.md`, `services.md`, `cross-cutting.md`가 승인되어야 합니다(application-design은 필수입니다 — unit으로 묶일 component, method, service를 결정합니다)
- wireframe 단계가 실행되었다면: `screen-structure.md`가 사용 가능해야 합니다(frontend unit 구성에 정보를 제공합니다)
- brownfield인 경우 brownfield 컨텍스트가 사용 가능해야 합니다

## Input

- `requirements.md`
- `stories.md`, `personas.md`
- `components.md`, `component-methods.md`, `component-dependencies.md`, `services.md`, `cross-cutting.md`(application-design으로부터)
- `screen-structure.md`(존재한다면 — wireframe에서 가져오며, frontend unit 경계에 정보를 제공)
- brownfield 컨텍스트(해당하는 경우)

## Question Guidance

다음 항목을 중심으로 명확화 질문을 집중합니다:

- story를 unit으로 어떻게 그룹화해야 하는가 — 도메인 경계, 배포 독립성, 팀 소유권, 또는 feature 영역 기준 중 어느 쪽인가?
- unit 간 통합 패턴은 무엇인가 — 동기 호출, 비동기 이벤트, 공유 데이터 저장소?
- unit 경계에 영향을 주어야 할 팀 구조나 소유권 제약이 있는가?
- 시스템의 서로 다른 부분에 별도 unit으로 나눌 만한 확장성 또는 배포 요구사항 차이가 있는가?
- 비즈니스 도메인에 자연스러운 unit 경계로 매핑되는 명확한 bounded context가 있는가?
- cross-cutting 관심사(`cross-cutting.md` 기반)는 전용 shared/platform unit으로 분리해야 하는가, 아니면 각 unit에 내장해야 하는가?(권장: 전용 unit을 먼저 구축하고 다른 unit이 의존하도록 함.)
- (greenfield 다중 unit에만 해당) 배포 모델은 무엇인가 — 독립 배포 가능한 microservice인가, 논리 모듈을 가진 monolith인가? 선호하는 디렉터리 구조는 어떤 것인가?
- (`screen-structure.md`가 존재할 때) frontend 화면 그룹은 별도 unit으로 매핑해야 하는가, 아니면 모든 frontend를 한 unit에 두어야 하는가?

## Output

세 가지 필수 artifact입니다:

### units-of-work.md

unit당 한 섹션. 각각 다음을 포함합니다:

- **Name** — unit 식별자
- **Type** — service(독립 배포 가능) 또는 module(service 내부의 논리적 그룹)
- **Purpose** — 이 unit이 하는 일
- **Responsibilities** — 책임지는 항목
- **Components** — `components.md`의 어떤 component가 이 unit에 속하는지
- **Stories** — 이 unit이 구현하는 story ID(`S-<n>`)(주 소유 및 기여 story 모두 포함)
- **Code organization strategy** (greenfield 전용) — 이 unit의 디렉터리 구조

### units-of-work-dependency.md

unit 간 의존성 매트릭스:

- Unit A → Unit B: 통신 패턴(동기 호출, 비동기 이벤트, 공유 데이터)
- 각 의존성에 대한 근거
- 빌드/배포 순서에 미치는 영향
- 순환 의존성은 명시적인 정당화와 함께 나열되어야 함

### units-of-work-story-map.md

모든 story를 unit에 매핑한 완전한 매핑. 형식:

- **Story ID** — `S-<n>`
- **Primary unit** — 해당 story의 인수 기준을 소유하고 완료에 책임을 지는 unit
- **Contributing units** — story 충족에 참여하는 다른 unit(없을 수 있음)
- **Rationale** — 이렇게 할당한 이유

`stories.md`의 모든 story가 등장해야 합니다. 모든 story는 정확히 하나의 primary unit을 가져야 합니다. story는 0개 이상의 contributing unit을 가질 수 있습니다. cross-cutting story(예: 시스템 전반의 에러 처리)는 일반적으로 cross-cutting/platform unit이 primary가 되고 다른 모든 unit이 contributing이 됩니다.

## Validation

이 skill의 출력에 대한 검증 규칙은 skill 루트의 `validation-spec.md`에 있습니다. 적용 방식은 `aidlc-common/protocols/aidlc-validator-protocol.md`를 참조하세요.
