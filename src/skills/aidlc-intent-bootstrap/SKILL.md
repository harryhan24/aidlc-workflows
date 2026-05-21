---
name: aidlc-intent-bootstrap
description: |
  AI-DLC intent-bootstrap skill. intent를 부트스트래핑하는 전체 작업을 담당합니다: org-ai-kb 위치를 확정하고, intent slug를 생성·확인하며, 다음 intent 번호를 선택하고, intent 디렉터리와 그 스켈레톤 파일(`intent-prompt.md`, `state/intent-state.md`, `audit/intent-audit.md`, stub `workflow.md`)을 생성하고, greenfield/brownfield를 분류하고, downstream skill들을 위한 구조화된 `intent.md`와 `bootstrap-context.md`를 작성합니다.

  모든 intent의 첫 사전 루프 단계로서 `aidlc-orchestrator`가 호출합니다. 일반적으로 직접 호출되지 않습니다.
metadata:
  phase: bootstrap
  stage: intent-bootstrap
  per-unit: "false"
  human-clarification: "false"
  plan-creation: "false"
  plan-verification: "false"
  artefact-verification: "false"
---

# Intent Bootstrap

intent를 처음부터 끝까지 부트스트래핑합니다: 이후 모든 skill이 의존하는 디렉터리와 스켈레톤 파일을 생성하고, intent를 분류하고, `intent.md`와 `bootstrap-context.md`를 생성합니다. 이 skill은 `workflow.md`가 존재하기 전에 실행되므로, intent를 존재하게 하는 데 필요한 모든 것을 직접 담당합니다.

## Inputs

orchestrator가 다음을 전달합니다:

- 원문 그대로의 intent 진술
- workspace root 경로
- 사람이 자발적으로 제공한 모든 context(보통 없음)

입력 파일은 없습니다 — `intent-prompt.md`는 아직 존재하지 않으며, 이 skill이 그것을 생성합니다.

## Question Guidance

추론할 수 없는 것만 물어보십시오. 순서대로:

- **org-ai-kb location** — 아직 존재하지 않고 사람이 자발적으로 제공하지 않은 경우에만. 기본값은 `<workspace-root>/org-ai-kb/`.
- **Slug** — 자동 생성된 kebab-case slug를 제시하고, 재정의 옵션을 제공.
- **Classification** — greenfield, brownfield, 또는 mixed. 자명하지 않을 때만 확인.
- **Repos in scope** — brownfield/mixed인 경우에만.
- **Codekb hydration / reverse-engineering 필요 여부** — brownfield/mixed인 경우에만.
- **Intent type** — feature, bug fix, migration, refactor, prototype 등.

codekb가 없는 greenfield의 경우 slug + type만 묻는 것으로 충분할 수 있습니다.

## Plan

`plan-creation: "false"` — plan 파일은 생성하지 않습니다. 실행은 아래 단계를 그대로 따릅니다.

## Execution

1. `org-ai-kb/aidlc-docs/`가 존재하는지 확인합니다.
2. 기존 `intent-*` 디렉터리들을 나열해 다음 `<nnn>`(0으로 zero-padded)을 선택합니다.
3. `intent-<nnn>-<slug>/`를 하위 폴더 `state/`, `audit/`, `bootstrap/intent-bootstrap/`와 함께 생성합니다.
4. intent 루트에 `intent-prompt.md`(원문 prompt)를 작성합니다.
5. `aidlc-state-schema.md`의 헤더로 `state/intent-state.md`를 초기화합니다.
6. 헤더와 함께 `audit/intent-audit.md`를 초기화합니다.
7. `workflow.md`를 정확히 한 줄로 작성합니다:
   ```
   workflow-composition --phase bootstrap intent.md bootstrap/intent-bootstrap/bootstrap-context.md
   ```
   `intent-bootstrap` 줄은 없습니다 — `process_checker`가 `workflow.md`를 읽는 시점에는 이 skill이 이미 완료되어 있기 때문입니다.
8. intent 루트에 `intent.md`를 작성합니다.
9. `bootstrap/intent-bootstrap/` 안에 `bootstrap-context.md`를 작성합니다.

## Output

### intent.md (intent 루트)

- **Prompt** — 원문 그대로
- **Summary** — 한 문단
- **Slug**
- **Type** — feature, bug fix, migration, refactor, prototype 등

### bootstrap-context.md (`bootstrap/intent-bootstrap/` 내)

- **Classification** — greenfield / brownfield / mixed (근거 포함)
- **Repos in scope** — 목록 또는 "none"
- **Codekb status** — repo별 hydrated / partial / missing, 또는 "n/a"
- **Reverse-engineering** — 필요(repo별) 또는 불필요

## Return value

orchestrator에 다음을 반환합니다: `status: complete`, `intent_dir_path`, 생성된 artefact 목록.

## Validation

`validation-spec.md`를 참조하십시오.
