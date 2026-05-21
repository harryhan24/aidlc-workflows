---
name: aidlc-user-stories
description: |
  AI-DLC user-stories skill. 승인된 `requirements.md`를 INVEST 기준을 충족하는 story와 도메인에 근거한 persona로 분해합니다.

  requirements가 이미 작성되어 있고 이 단계만 필요할 때 `/skill aidlc-user-stories`로 명시적으로 호출하십시오. 신규 개발 intent의 경우 대신 `aidlc-orchestrator`를 활성화하십시오.
metadata:
  phase: inception
  stage: user-stories
  per-unit: "false"
  human-clarification: "true"
  plan-creation: "true"
  plan-verification: "true"
  artefact-verification: "true"
---

# User Stories

승인된 requirements를 시스템의 모든 레이어 — 사용자 대상, 시스템/backend, 통합, 운영 — 에 걸친 story로 분해합니다. 사용자 대상 story에 대해서는 persona를 생성합니다. story는 requirements와 design 사이의 계약입니다.

## Prerequisites

- `requirements.md`가 승인되어 있어야 합니다

## Input

- `requirements.md`

## Question Guidance

먼저 `requirements.md`를 분석하십시오. 도출 가능한 것은 도출하고, 사람의 입력이 출력에 의미 있게 영향을 미치는 부분에서만 질문하십시오. 다음에 집중하십시오:

- 구별되는 사람 사용자 유형은 누구인가? 명백한 것 외에 story를 가져야 할 persona(예: 운영자, 지원 인력, 감사자)가 있는가?
- story가 backend/시스템 동작, 통합 flow, 운영 관심사까지 다뤄야 하는가 — 아니면 주로 사용자 대상인가?
- story는 어떻게 조직되어야 하는가 — 사용자 여정별, 기능 영역별, 시스템 레이어별, 또는 도메인별? 이 intent에 가장 적합한 묶음은 무엇인가?
- 어떤 엣지 케이스와 에러 시나리오가 가장 중요한가? 어떤 실패 모드, 경계 조건, 또는 의존성 실패가 명시적 story를 가져야 하는가?
- `requirements.md`의 각 NFR에 대해 — 자체 story로 만들 것인가, 여러 story에 걸친 cross-cutting acceptance criterion으로 만들 것인가, 아니면 둘 다인가?
- (brownfield 전용) 어떤 기존 동작이 변경 없이 보존되어야 하는가? 회귀 보호 story가 필요한가?

## Output

두 가지 artifact.

### personas.md

사람 persona당 한 섹션. 각 persona에는 name, role, goals, context가 포함됩니다. persona는 도메인에 근거해야 하며 — 일반적인 archetype은 안 됩니다. intent에 사람 사용자가 없다면(순수 backend/infra), "No human personas — system-only intent."라고 명시하십시오.

### stories.md

`requirements.md`에서 식별된 모든 레이어를 다루는 story. 두 가지 형식:

- User story: `As a [persona], I want [goal], so that [benefit].`
- System story: `As the [system/service], when [trigger], it must [behavior].`

각 story에는 다음이 포함됩니다:

- **ID** — `S-<n>`, 순차
- **Acceptance criteria** — pass/fail로 검증 가능
- **Requirements** — `Requirements: FR-x, NFR-y, ...` `requirements.md`로 추적 가능

story는 INVEST 기준(Independent, Negotiable, Valuable, Estimable, Small, Testable)을 따라야 합니다.

## Validation

이 skill 출력에 대한 validation 규칙은 skill 루트의 `validation-spec.md`에 있습니다. 적용 방식은 `aidlc-common/protocols/aidlc-validator-protocol.md`를 참조하십시오.
