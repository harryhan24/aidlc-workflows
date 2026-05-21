# AI-DLC 폴더 구조

```
org-ai-kb/
│
├── codekb/
│   ├── <repo-a>/
│   │   ├── summary.md                     tech stack, purpose, ownership (RE-generated)
│   │   ├── architecture.md                (RE-generated)
│   │   ├── integration-map.md             (RE-generated)
│   │   ├── intent-history.md              which intents touched this repo (last entry = latest)
│   │   └── engineering/
│   │       ├── intent-<nnn>/
│   │       │   ├── domain-entities.md
│   │       │   ├── business-rules.md
│   │       │   ├── nfr-design.md
│   │       │   ├── infrastructure-design.md
│   │       │   └── deployment-architecture.md
│   │       └── intent-<nnn>/
│   │           └── ...
│   ├── <repo-b>/
│   │   └── ...
│   └── <repo-n>/
│       └── ...
│
└── aidlc-docs/
    └── intent-<nnn>-<intent-name>/
        │
        ├── intent-prompt.md                 raw user prompt (seeded by orchestrator)
        ├── intent.md                        structured intent (produced by intent-bootstrap)
        ├── workflow.md                      approved workflow (seeded by orchestrator, appended by workflow-composition)
        │
        ├── state/
        │   ├── intent-state.md             overall intent + inception progress
        │   ├── process-checkpoint.json     process_checker's own state
        │   ├── <unit-name>-state.md
        │   └── <unit-name>-state.md
        │
        ├── audit/
        │   ├── intent-audit.md
        │   ├── <unit-name>-audit.md
        │   └── <unit-name>-audit.md
        │
        ├── bootstrap/
        │   ├── intent-bootstrap/                (artifacts produced by the intent-bootstrap skill)
        │   │   ├── intent-bootstrap-questions.md
        │   │   ├── intent-bootstrap-plan.md
        │   │   └── bootstrap-context.md
        │   └── workflow-composition/            (artifacts produced by the workflow-composition skill)
        │       ├── workflow-composition-questions.md
        │       ├── workflow-composition-plan.md
        │       └── workflow-rationale.md
        │
        ├── inception/
        │   ├── reverse-engineering/            (one subdirectory per repo, always scoped)
        │   │   └── <repo-name>/
        │   │       ├── reverse-engineering-questions.md
        │   │       ├── reverse-engineering-plan.md
        │   │       ├── components.md
        │   │       ├── component-methods.md
        │   │       ├── component-dependencies.md
        │   │       ├── services.md
        │   │       ├── cross-cutting.md
        │   │       ├── data-models.md             (if persistence)
        │   │       ├── api-contracts.md           (if APIs)
        │   │       ├── event-catalog.md           (if event-driven)
        │   │       ├── external-dependencies.md   (if external integrations)
        │   │       ├── technology-stack.md
        │   │       ├── code-structure.md
        │   │       ├── code-quality-assessment.md
        │   │       └── chunks/                    (for medium/large codebases)
        │   │           └── <chunk-name>.md
        │   ├── requirements-analysis/          (artifacts produced by the requirements-analysis skill)
        │   │   ├── requirements-analysis-questions.md
        │   │   ├── requirements-analysis-plan.md
        │   │   └── requirements.md
        │   ├── user-stories/                   (artifacts produced by the user-stories skill)
        │   │   ├── user-stories-questions.md
        │   │   ├── user-stories-plan.md
        │   │   ├── stories.md
        │   │   └── personas.md
        │   ├── wireframes/                     (artifacts produced by the wireframes skill, if UI intent)
        │   │   ├── wireframes-questions.md
        │   │   ├── wireframes-plan.md
        │   │   ├── screen-data-map.md
        │   │   ├── screen-structure.md
        │   │   ├── wireframe-guidance.md
        │   │   └── wireframes/                 (visual files — SVG or HTML per screen)
        │   │       └── <screen-name>.svg|html
        │   ├── application-design/             (artifacts produced by the application-design skill)
        │   │   ├── application-design-questions.md
        │   │   ├── application-design-plan.md
        │   │   ├── components.md
        │   │   ├── component-methods.md
        │   │   ├── component-dependencies.md
        │   │   ├── services.md
        │   │   ├── cross-cutting.md
        │   │   ├── data-models.md                 (if persistence)
        │   │   ├── api-contracts.md               (if system exposes APIs)
        │   │   ├── event-catalog.md               (if event-driven)
        │   │   └── external-dependencies.md       (if external integrations)
        │   └── units-generation/                (artifacts produced by the units-generation skill)
        │       ├── units-generation-questions.md
        │       ├── units-generation-plan.md
        │       ├── units-of-work.md
        │       ├── units-of-work-dependency.md
        │       └── units-of-work-story-map.md
        │
        ├── construction/
        │   ├── <unit-name>/                     (one subdirectory per unit; per-unit skills write here)
        │   │   ├── unit-summary.md
        │   │   ├── adr.md
        │   │   ├── functional-design/
        │   │   │   ├── functional-design-questions.md
        │   │   │   ├── functional-design-plan.md
        │   │   │   ├── business-logic-model.md
        │   │   │   ├── domain-entities.md
        │   │   │   └── business-rules.md
        │   │   ├── nfr-assessment/
        │   │   │   ├── nfr-assessment-questions.md
        │   │   │   ├── nfr-assessment-plan.md
        │   │   │   ├── nfr-requirements.md
        │   │   │   └── tech-stack-decisions.md
        │   │   ├── nfr-design/
        │   │   │   ├── nfr-design-questions.md
        │   │   │   ├── nfr-design-plan.md
        │   │   │   ├── nfr-design-patterns.md
        │   │   │   └── logical-components.md
        │   │   ├── infrastructure-design/
        │   │   │   ├── infrastructure-design-questions.md
        │   │   │   ├── infrastructure-design-plan.md
        │   │   │   ├── infrastructure-design.md
        │   │   │   └── deployment-architecture.md
        │   │   ├── code/
        │   │   │   ├── code-generation-questions.md
        │   │   │   ├── code-generation-plan.md
        │   │   │   └── CODE_SUMMARY.md
        │   │   └── ...
        │   ├── <unit-name>/
        │   │   └── ...
        │   └── build-and-test/
        │
        └── operations/
            └── (skills to be defined)
```

## 문서 라이프사이클

codekb의 문서는 두 가지 범주로 나뉩니다.

**Category 1: Reverse-engineering 문서**는 `codekb/<repo>/` 바로 아래에 평탄하게 위치합니다. repo가 reverse engineering을 통해 처음 온보딩될 때 생성되며, 현재 시점의 repo 상태를 기술합니다.

**Category 2: Engineering 문서**는 construction 중에 생성되며 `codekb/<repo>/engineering/intent-<nnn>/` 아래에 위치합니다. construction이 진행되는 동안에는 `aidlc-docs/intent-<nnn>/construction/<unit>/` 안에 있습니다. unit이 배포된 후에는 `codekb/<repo>/engineering/intent-<nnn>/`로 이동됩니다. 각 intent는 자신만의 폴더를 갖습니다.

`intent-history.md`는 어떤 intent들이 어떤 순서로 repo에 영향을 주었는지를 추적합니다. 마지막 항목이 가장 최신 상태를 나타냅니다.

## Design Knowledge 분리

intent 폴더는 작업이 어떻게 수행되었는지에 관한 이야기를 담습니다 — 어떤 질문을 했고, 어떤 plan을 세웠고, 어떤 결정이 기록되었고, 어떤 진척이 추적되었는지. intent가 완료되면 이 폴더는 변경 불가능한 역사 기록이 됩니다. "무엇이, 왜 일어났는가"에 답하는 자료입니다.

codekb는 각 repository에 대한 현재의 진실을 담습니다 — 도메인 모델, business rule, non-functional 설계, infrastructure, 그리고 다른 시스템과의 통합 방식 등. intent 폴더와 달리 codekb 문서는 살아 있는 문서입니다. 절대 덮어쓰지 않으며, 오직 확장만 합니다 — 다만 과거의 결정이 시간이 지나면서 무효화될 수 있고, 그 결과로 더 이상 유효하지 않은 섹션이 제거될 수는 있습니다.

## Workspace 설정

construction 단계에서 각 unit 팀은 다음을 엽니다.

```
<workspace>/
├── org-ai-kb/                      (cloned — shared across teams)
└── <target-repo>/                  (the code being worked on)
```
