---
name: aidlc-requirements-analysis
description: |
  AI-DLC requirements-analysis skill. intent 진술문으로부터 구조화된 `requirements.md`를 생성하며, greenfield와 brownfield를 분류하고 측정 가능한 기준과 함께 functional/non-functional requirements를 포착합니다.

  이미 intent가 있고 이 단일 단계만 수행하길 원할 때 `/skill aidlc-requirements-analysis`로 명시적으로 호출합니다. AI-DLC의 전체 흐름(requirements → stories → design → code)을 거쳐야 하는 새로운 개발 intent의 경우에는 `aidlc-orchestrator`를 활성화합니다.
metadata:
  phase: inception
  stage: requirements-analysis
  per-unit: "false"
  human-clarification: "true"
  plan-creation: "true"
  plan-verification: "true"
  artefact-verification: "true"
---

# Requirements Analysis

사람이 작성한 intent 진술문으로부터 requirements를 도출하고, 구조화하며, 검증합니다. codekb를 추론하여 해당 intent가 greenfield인지, brownfield인지, 또는 두 가지가 혼재된 경우인지 판단합니다.

## Prerequisites

- org-ai-kb이 셋업되어 있어야 합니다
- 기존 repo가 scope에 포함된다면 codekb가 hydrate되어 있어야 합니다

## Input

- 사람의 intent 진술문
- 모든 repo에 대한 codekb summary, integration map, domain entity

## Question Guidance

다음 항목을 중심으로 명확화 질문을 집중합니다:

- 정확히 무엇이 요청되고 있는가? 기대되는 결과는 무엇인가?
- 요청 유형: 신규 기능, 버그 수정, 리팩토링, 마이그레이션, 개선, 신규 프로젝트인가?
- scope: 단일 component, 다수 component, system 전반, cross-system 중 어느 범위인가?
- codekb를 기반으로 이것이 greenfield인지 brownfield인지 추론합니다. codekb에 관련 repo가 있다면 평가를 제시하고 사람에게 확인을 구합니다: "이것은 repo X, Y에 영향을 주는 brownfield 변경으로 보입니다 — 맞습니까?"
- codekb에 repo가 있다면 영향받는 것으로 보이는 repo를 식별하고 사람과 함께 scope를 확인합니다.
- Functional requirements: 핵심 기능, 사용자 상호작용, 시스템 동작, 데이터 요구사항.
- Non-functional requirements: 성능, 보안, 확장성, 사용성.
- 비즈니스 컨텍스트: 목표, 성공 기준, 이해관계자 니즈, 제약사항.
- 컴플라이언스: 규제, 접근성, 데이터 프라이버시 고려사항.

## Output

다음 내용을 담은 `requirements.md`라는 requirements 문서:

- **Intent summary** — 유형(신규 기능, 버그 수정, 마이그레이션 등), scope(단일 component부터 system 전반까지), 복잡도, greenfield/brownfield 분류, 영향받는 repo
- **Functional requirements** — `FR-<n>` 형식으로 번호를 매기며, 각각은 pass/fail로 검증 가능해야 함
- **Non-functional requirements** — 가능한 한 측정 가능하게 작성(예: "빠르다"가 아니라 "p95 < 200ms")
- **Assumptions** — 사실이 아니라 가정으로 표시
- **Out of scope** — 명시적으로 제외된 항목

모든 섹션은 필수입니다. 비어 있다면 "None identified"라고 명시합니다.

## Validation

이 skill의 출력에 대한 검증 규칙은 skill 루트의 `validation-spec.md`에 있습니다. 결정론적 post-condition 스크립트는 `scripts/`에 있습니다. 적용 방식은 `aidlc-common/protocols/aidlc-validator-protocol.md`를 참조하세요.
