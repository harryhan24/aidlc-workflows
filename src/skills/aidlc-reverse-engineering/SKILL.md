---
name: aidlc-reverse-engineering
description: |
  AI-DLC reverse-engineering skill. 기존 코드베이스를 분석하고 현존하는 것을 기술하는 design artifact를 생성합니다 — Application Design 형식을 그대로 따르므로 downstream stage들이 RE artifact를 동일하게 소비할 수 있습니다. 추가적인 RE 전용 artifact는 기술 실태와 code health를 담습니다.

  전체 AI-DLC flow를 실행하지 않고 기존 코드베이스를 분석해야 할 때 `/skill aidlc-reverse-engineering`으로 명시적으로 호출하십시오. 신규 개발 intent의 경우 대신 `aidlc-orchestrator`를 활성화하십시오.
metadata:
  phase: inception
  stage: reverse-engineering
  per-unit: "false"
  human-clarification: "true"
  plan-creation: "true"
  plan-verification: "true"
  artefact-verification: "true"
---

# Reverse Engineering

기존 코드베이스를 분석하고 현존하는 것을 기술하는 design artifact를 생성합니다. 출력은 Application Design 형식을 그대로 따르므로 downstream stage들이 RE artifact를 동일하게 소비할 수 있습니다. 추가적인 RE 전용 artifact는 기술 실태와 code health를 담습니다.

## Prerequisites

- 대상 코드베이스가 접근 가능해야 합니다(로컬 clone 또는 읽기 가능한 workspace)
- Requirements Analysis가 brownfield 분류를 시사해야 합니다
- 이 skill은 항상 `--scope <repo-name>`과 함께 호출됩니다 — 단일 repo여도 repo당 한 번 호출

## Input

- 사람의 intent 진술(어떤 영역에 집중할지 범위를 정하기 위해)
- 대상 코드베이스(파일 시스템 접근)
- `requirements.md`(사용 가능한 경우 — RE를 관련 영역에 집중시키기 위해)

artifact는 `inception/reverse-engineering/<repo-name>/`에 작성됩니다. downstream skill들은 `bootstrap-context.md`의 repo 이름을 사용해 거기서 RE artifact를 읽습니다.

## Chunking Strategy

전체 코드베이스를 한 번에 분석하지 마십시오. 하이브리드 방식을 사용하십시오:

1. **구조 스캔** — top-level package/module을 빠르게 훑어 지도를 작성
2. **비즈니스 flow별 심층 분석** — 한 번에 하나의 flow/feature를 분석하며 필요에 따라 package 경계를 넘나듦
3. **조립** — chunk별 결과를 통합된 artifact로 합침

Chunk 경계:
- 소형 코드베이스(< 50 파일): 단일 chunk, 전수 분석
- 중형 코드베이스(50–200 파일): 주요 package 또는 bounded context당 chunk 하나
- 대형 코드베이스(200+ 파일): intent와 관련된 비즈니스 flow당 chunk 하나

심층 분석에 들어가기 전에 구조 스캔과 제안된 chunk를 사람에게 제시해 승인받으십시오.

## Question Guidance

clarification 질문은 다음에 집중하십시오:
- 현재 intent와 관련된 코드베이스 영역은 어디인가?
- 어느 정도의 깊이가 필요한가? (전수 분석 vs. 특정 flow에 집중)
- AI가 알아야 할 비문서화된 컨벤션이나 부족 지식(tribal knowledge)이 있는가?
- 어떤 비즈니스 flow를 가장 먼저 이해하는 것이 중요한가?

미래의 design 결정에 대해서는 묻지 마십시오 — RE는 무엇이 되어야 하는지가 아니라 무엇이 존재하는지를 문서화합니다.

## Output

### Forward-flow에 대응하는 artifact (Application Design과 동일 형식)

- **components.md** — 발견된 component와 그 purpose, responsibility, state, ownership
- **component-methods.md** — component별 주요 method와 input/output
- **component-dependencies.md** — 통신 패턴을 포함한 의존성 매트릭스
- **services.md** — 발견된 service 오케스트레이션과 비즈니스 workflow
- **cross-cutting.md** — 기존 error handling, auth model, logging, validation 패턴
- **data-models.md** — 발견된 엔티티, 필드, 관계, 제약(persistence가 있는 경우)
- **api-contracts.md** — 기존 API 표면(API가 있는 경우)
- **event-catalog.md** — 기존 event(event-driven인 경우)
- **external-dependencies.md** — 코드베이스가 통합하는 외부 시스템

### RE 전용 artifact

- **technology-stack.md** — 언어, framework, 버전, 빌드 도구, runtime 의존성
- **code-structure.md** — 파일/폴더 레이아웃, 명명 규칙, module 경계
- **code-quality-assessment.md** — 테스트 커버리지, 패턴, 안티패턴, 기술 부채 지표

### chunk별 artifact (중형/대형 코드베이스용)

- **chunks/<chunk-name>.md** — 한 chunk의 상세 분석. 조립 단계에서 위 artifact들로 통합됨

## Assembly

모든 chunk가 분석된 후, chunk별 결과를 통합된 artifact로 합칩니다. 다중 agent 실행에서는 chunk들이 병렬로 실행될 수 있으며, 모두 완료된 뒤에 조립이 실행됩니다. 단일 agent에서는 chunk들이 순차적으로 실행되고 마지막에 조립이 일어납니다.

## Downstream Consumption

이후의 stage는 다음 두 곳 모두에서 artifact를 찾아야 합니다:
1. `inception/reverse-engineering/` — RE가 발견한 design
2. `inception/application-design/` — 사람이 승인한 design(RE를 확장하거나 재정의할 수 있음)

둘 다 존재한다면 Application Design이 우선합니다. RE artifact는 Application Design이 다듬어 나가는 기준선(baseline) 역할을 합니다.

## Validation

이 skill 출력에 대한 validation 규칙은 skill 루트의 `validation-spec.md`에 있습니다. 적용 방식은 `aidlc-common/protocols/aidlc-validator-protocol.md`를 참조하십시오.
