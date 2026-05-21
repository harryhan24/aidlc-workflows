---
name: aidlc-workflow-composition
description: |
  AI-DLC workflow-composition skill. 이 intent에 대해 실행할 skill을 catalogue에서 선택하고, skill별 flag를 설정하며, 결과 라인을 `workflow.md`에 추가합니다.

  `aidlc-intent-bootstrap` 직후에 `aidlc-orchestrator`에 의해 호출됩니다. 일반적으로 직접 호출하지 않습니다.
metadata:
  phase: bootstrap
  stage: workflow-composition
  per-unit: "false"
  human-clarification: "false"
  plan-creation: "false"
  plan-verification: "false"
  artefact-verification: "true"
---

# Workflow Composition

`skills/aidlc-orchestrator/CATALOGUE.md`에서 skill을 선택하고 `workflow.md`를 다시 작성하여 이 intent에 대한 적응형 workflow를 구성합니다. artefact-verification 단계가 workflow-approval gate입니다 — inception phase의 어떤 skill도 실행되기 전에 사람이 구성된 workflow를 승인합니다.

## Prerequisites

- intent 루트의 `intent.md`
- `bootstrap/intent-bootstrap/`의 `bootstrap-context.md`

## Input

- `intent.md`
- `bootstrap-context.md`
- `skills/aidlc-orchestrator/CATALOGUE.md`

## Question Guidance

§1을 적용하여 모든 catalogue skill을 intent와 대조해 평가합니다. 도출 가능한 것은 도출하고, 진정한 모호성이 있을 때만 질문합니다. 예:

- reverse-engineering을 실행해야 하는가?(codekb가 hydrate되어 있다면 건너뛴다)
- 사소한 버그 수정인 경우, user-stories나 application-design을 건너뛰어야 하는가?
- skill별 flag 오버라이드(`plan-verification`, `artefact-verification`)는 있는가?

## 1. Composition Rules

1. catalogue에서 시작하여 각 skill을 intent와 대조해 평가합니다. 고정된 pipeline을 가정하지 마세요.
2. **Right-sizing 원칙.** 본질적으로 항상 켜져 있는 세 가지 skill이 있습니다: `requirements-analysis`, `code-generation`, `build-and-test`. 그 외 모든 것은 조건부입니다. intent의 scope가 좁거나, 신규성이 낮거나, 단일 액터, 단일 component, 또는 순수 구현인 경우 과감하게 건너뜁니다. 해당 skill의 출력이 이후 단계를 의미 있게 형성할 때에만 포함합니다. 모든 skill을 포함하는 workflow는 거의 옳은 답이 아닙니다.
3. right-sizing 원칙으로도 어떤 skill에 대해 진정으로 확신이 서지 않는다면, 포함하는 쪽으로 기웁니다.
4. `per-unit: "true"`인 construction skill은 unit당 한 번 실행됩니다. unit이 하나라면 단일 pass로 합칩니다.
5. 구성된 workflow는 권고이지 계약이 아닙니다. 필요하다면 orchestrator는 실행 중간에 멈추고 건너뛴 skill을 삽입할 수 있습니다.
6. workflow를 사람에게 제시할 때 §3의 예제를 이름으로 언급하지 마세요 — 그것들은 내부 추론 보조 자료입니다.
7. 실행될 skill만 나열하세요. 건너뛴 skill은 나열하지 마세요.
8. Reverse engineering:
   - 영향받는 repo에 대해 codekb가 hydrate됨 → RE 건너뜀.
   - 그 외 → 분석이 필요한 repo당 하나의 `reverse-engineering` 호출.
   - 통합 대상이 있는 greenfield → 해당 대상에 대해서만 RE.
   - Brownfield → 영향받는 repo에 대해 기본적으로 RE 포함.
9. **사람을 향한 어휘.** workflow를 사람에게 제시할 때 각 skill을 catalogue의 `stage`와 `phase`로 지칭합니다(예: "user stories stage", "inception phase"). skill 이름은 내부 추론과, 한 stage가 여러 skill로 매핑될 때의 구분자로만 사용합니다.

## 2. Presentation

phase → stage 순서로 실행 순으로 구성을 묶어 제시합니다. 한 stage에 하나의 skill만 매핑된다면 stage만으로 충분합니다. 여러 skill이 한 stage를 공유한다면 구분자로 skill 이름을 적은 하위 bullet으로 각각 나열합니다.

```
Proposed workflow

Inception phase
  1. Requirements analysis stage
  2. User stories stage
  3. Application design stage
  4. Units generation stage

Construction phase (per unit: <unit-name>)
  5. Functional design stage
  6. NFR assessment stage
  ...
```

## 3. Examples

예제는 최소에서 최대로 배열되어 있습니다. 각각 무엇이 **건너뛰어졌는지**와 그 이유를 적고 있습니다 — right-sizing 추론이 거기에 들어있습니다.

### A. 사소한 버그 수정 — 오타, off-by-one, null 체크 누락(greenfield 또는 brownfield)

**Workflow:** requirements-analysis → code-generation → build-and-test.

(brownfield인 경우: codekb가 hydrate되어 있지 않다면 영향받는 repo에 대한 reverse-engineering을 앞에 추가합니다.)

**Skipped:** user-stories(신규 사용자 노출 동작 없음), application-design(component 변경 없음), units-generation(하나의 사소한 변경), functional-design(신규 도메인 로직 없음), nfr-assessment(NFR 변동 없음), nfr-design, infrastructure-design.

### B. 단순한 단일 component 유틸리티 — 계산기, 문자열 parser, CSV exporter, CLI tool

**Workflow:** requirements-analysis → code-generation → build-and-test.

**Skipped:** user-stories(명백한 단일 액터, 단일 happy path), application-design(단일 component, orchestration 없음), units-generation(자명하게 unit 하나), functional-design(로직이 곧 requirements), nfr-assessment(기본값으로 충분), nfr-design, infrastructure-design.

계산기에는 story map과 domain model이 필요 없습니다. requirements 문서가 operation을 포착하고, build-and-test skill이 실수를 잡아냅니다. code-generation 중에 실제 모호성(반올림 규칙, 에러 의미)이 드러나면 잠시 멈추고 functional-design을 삽입합니다 — 그게 규칙 5의 용도입니다.

### C. 동작 변경이 없는 리팩토링 — 이름 변경, 추출, 재구조화(brownfield)

**Workflow:** reverse-engineering → requirements-analysis → application-design → code-generation → build-and-test.

**Skipped:** user-stories(동작 변경이 없으므로 신규 story 없음), units-generation(리팩토링이 거대하지 않다면 단일 unit), functional-design(비즈니스 로직이 그대로 보존됨), nfr-assessment(NFR이 변하지 않음), nfr-design, infrastructure-design.

application design은 포함됩니다 — 리팩토링의 핵심이 바로 component 경계를 변경하는 것이기 때문입니다.

### D. 기존 service에 작은 기능 추가 — 신규 endpoint, 신규 필드, 신규 검증 규칙(brownfield)

**Workflow:** reverse-engineering → requirements-analysis → user-stories → functional-design → code-generation → build-and-test.

**Skipped:** application-design(기존 component 경계 재사용), units-generation(unit 하나 — 기존 service), nfr-assessment(기능이 NFR을 바꾸지 않는 한 service에서 상속), nfr-design, infrastructure-design.

functional-design은 실행되지만 `--unit <existing-service-name>`으로, 신규 비즈니스 규칙만 정교화합니다.

### E. 기존 시스템에 신규 component가 필요한 신규 기능(brownfield)

**Workflow:** reverse-engineering → requirements-analysis → user-stories → application-design → functional-design → nfr-assessment → code-generation → build-and-test.

**Skipped:** units-generation(신규 component가 곧 unit), nfr-design 및 infrastructure-design(기존 인프라가 변경 없이 신규 component를 흡수하는 경우에만 — 그렇지 않다면 포함).

### F. 마이그레이션 — 언어, 프레임워크, 데이터베이스, 플랫폼 변경(brownfield)

**Workflow:** reverse-engineering → requirements-analysis → application-design → functional-design → nfr-assessment → nfr-design → infrastructure-design → code-generation → build-and-test.

**Skipped:** user-stories(신규 동작 없음, 다른 기반일 뿐), units-generation(기존 component 경계가 대체로 그대로 이어짐).

NFR design과 infrastructure design은 마이그레이션에서 필수입니다 — 마이그레이션이 그 안에서 일어나기 때문입니다.

### G. greenfield 단일 service 시스템 — 작거나 중간 scope

**Workflow:** requirements-analysis → user-stories → application-design → functional-design → nfr-assessment → nfr-design → infrastructure-design → code-generation → build-and-test.

**Skipped:** reverse-engineering(greenfield, 통합 대상 없음), units-generation(unit 하나).

per-unit construction skill은 `--unit <service-name>`으로 한 번 실행됩니다.

### H. greenfield 다중 service 시스템 — 전체 pipeline

**Workflow:** requirements-analysis → user-stories → application-design → units-generation → functional-design (per unit) → nfr-assessment (per unit) → nfr-design (per unit) → infrastructure-design (per unit) → code-generation (per unit) → build-and-test.

**Skipped:** reverse-engineering — 단, 시스템이 codekb에 없는 기존 repo와 통합되는 경우에는 해당 repo부터 RE합니다.

이것이 교과서적인 전체 pipeline입니다. 대부분의 intent는 이렇지 않으며, units-generation이 실제로 여러 unit을 만들어 낼 때에만 이쪽으로 손을 뻗습니다.

### I. cross-repo brownfield 변경 — 두 개 이상의 기존 repo에 영향

**Workflow:** reverse-engineering(영향받는 repo당 하나) → requirements-analysis → user-stories(사용자 노출이 있다면) → application-design → functional-design (per unit) → nfr-assessment (per unit) → code-generation (per unit) → build-and-test.

**Skipped:** units-generation(영향받는 repo가 곧 unit; 직접 매핑), nfr-design 및 infrastructure-design(cross-repo NFR이나 공유 인프라가 변경되는 경우에만 포함).

변경이 repo 경계를 가로지를 때 user-stories와 application-design이 제 자리를 얻습니다 — 거기서 변경의 솔기들이 고정됩니다.

## Output

### workflow.md (intent 루트에서 다시 작성)

이 skill이 실행될 때 `workflow.md`는 이 skill을 호출한 라인만 담고 있는 stub입니다. `aidlc-workflow-format.md`에 따라 실행 순서대로 선택된 downstream skill당 한 줄씩 처음부터 다시 작성합니다. bootstrap stub 라인은 유지하지 마세요. 첫 번째 줄은 실제 downstream skill(보통 `requirements-analysis` 또는 `reverse-engineering`)이어야 합니다.

`aidlc-workflow-format.md`에 따라 inception이 아닌 skill에는 routing flag가 필수입니다: construction skill은 `--unit <unit>`(per-unit) 또는 `--phase construction`(단일 pass)을 사용하고, operations skill은 `--phase operations`를 사용하며, inception skill은 둘 다 생략합니다.

### workflow-rationale.md (이 skill의 output 디렉터리)

skill당 포함 또는 건너뜀 이유를 짧은 bullet으로, phase별로 그룹화하여 작성합니다.

## Validation

`validation-spec.md`를 참조하세요.
