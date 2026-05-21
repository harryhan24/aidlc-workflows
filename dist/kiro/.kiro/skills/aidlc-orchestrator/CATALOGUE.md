# Skill Catalogue

AI-DLC skill들의 카탈로그입니다. 각 skill은 `skills/<skill-name>/` 아래의 디렉터리이며 [Agent Skills 명세](https://agentskills.io/specification)를 따릅니다. 이 카탈로그는 workflow를 구성할 때 orchestrator가 참조하는 원천 자료입니다.

skill들은 조합 가능합니다(composable). phase와 stage는 orchestrator가 skill을 묶고 제시하는 데 사용하는 구성 개념일 뿐이며 — 경직된 pipeline이 아닙니다. 고객은 workflow를 구성할 때 어떤 skill이든 순서를 바꾸거나, 삽입하거나, 생략할 수 있습니다.

## Skill 폴더 레이아웃

```
skills/<skill-name>/
├── SKILL.md              ← frontmatter (name, description, AI-DLC metadata) + instructions
├── validation-spec.md    ← validator를 위한 validation 규칙 + upstream 입력
└── scripts/              ← 선택 사항; script가 없으면 생략
```

공통 builder와 validator 동작은 `aidlc-common/protocols/aidlc-builder-protocol.md`와 `aidlc-common/protocols/aidlc-validator-protocol.md`에 정의되어 있습니다. skill별 폴더에는 skill마다 달라지는 내용만 담깁니다.

## Frontmatter metadata

모든 skill의 `SKILL.md` frontmatter는 `metadata` 아래에 orchestrator가 읽는 다음 필드를 가집니다.

- `phase` — `bootstrap`, `inception`, `construction`, `operations` 중 하나
- `stage` — 구성용 태그(예: `requirements-analysis`, `user-stories`); fan-out이 일어나는 경우 여러 skill이 동일한 stage를 공유할 수 있습니다
- `per-unit` — 해당 skill이 construction phase에서 unit마다 한 번씩 실행될 때 `"true"`
- `human-clarification` — 사람이 clarification 질문에 답할 경우 `"true"`(기본값); builder가 질문을 기록하고 자체 추천 답변을 선택해 자율적으로 진행하는 경우 `"false"`
- `plan-creation` — builder가 실행 전에 plan 파일을 작성하면 `"true"`(기본값); skill이 planning을 완전히 건너뛰고 clarification에서 바로 실행으로 가면 `"false"`
- `plan-verification` — 실행 전에 사람이 plan을 승인하면 `"true"`(기본값). `plan-creation: "false"`일 때는 무시됨. 잘못된 조합: `plan-creation: "false"` + `plan-verification: "true"`.
- `artefact-verification` — validator가 통과한 후 사람이 artifact를 검토하면 `"true"`(기본값)

값은 agentskills.io 명세에 따라 문자열입니다. orchestrator는 `"true"`/`"false"`를 boolean으로 파싱합니다.

## Flag 의미

- `human-clarification: "true"`(기본값) — builder가 clarification 질문을 작성하고, orchestrator가 사람에게 질문을 제시하고, 사람이 chat 또는 파일에서 답변하면, orchestrator가 그 답변을 builder에게 다시 넘깁니다. `"false"` — builder가 질문을 작성하고 파일에 자체 추천 답변(근거 기록 포함)을 선택해 채워 넣어, clarification 상태를 단일 builder pass 안에서 `pending`부터 `answered`를 거쳐 `complete`까지 한 번에 전이시키고 진행합니다. 사람의 의견은 묻지 않습니다.
- `plan-creation: "true"`(기본값) — builder가 명시적 단계로 plan 파일을 작성합니다. `"false"` — skill이 planning 단계를 완전히 건너뛰고, 상태는 `clarification:complete`에서 곧바로 `execution:pending`으로 갑니다. plan 파일은 생성되지 않습니다.
- `plan-verification: "true"`(기본값) — builder가 실행하기 전에 사람이 plan을 승인합니다. `"false"` — builder가 plan 승인 없이 진행합니다. `plan-creation: "false"`일 때는 무시됩니다.
- `artefact-verification: "true"`(기본값) — validator 통과 후 사람이 artifact를 검토합니다. `"false"` — validator 통과 = skill 완료; 사람은 중단 조건(halting condition)에 대해서만 의견을 묻습니다.

clarification은 항상 *시도*됩니다 — 추적성을 위해 질문 파일은 늘 존재합니다 — 단, `human-clarification` flag는 사람이 그 루프에 들어가는지 여부만 제어합니다.

## 명명 규칙

우리가 제공하는 모든 skill에는 고객 기여나 서드파티 skill과 구분하기 위해 `aidlc-` prefix가 붙습니다. `stage` 태그는 prefix가 없는 형식(예: `requirements-analysis`)이며, 사람이 사용하는 어휘로 그대로 남아 있습니다.

## 사용 가능한 skill

범례: ✅ 구현됨(`skills/` 아래 폴더 존재), 🚧 미구현.

기본 flag(명시되지 않은 경우): `human-clarification: true`, `plan-creation: true`, `plan-verification: true`, `artefact-verification: true`.

### Bootstrap phase

bootstrap phase는 intent당 한 번 실행됩니다. orchestrator는 `intent-bootstrap`을 `process_checker` 바깥에서 구동합니다(필연적으로 그래야 합니다 — `process_checker`가 읽는 파일이 아직 존재하지 않기 때문). `intent-bootstrap`이 완료되면 intent 스켈레톤이 생성되며, stub `workflow.md`에는 `workflow-composition` 한 줄만 들어 있습니다. 그 후 `workflow-composition`이 표준 루프를 통해 실행되고, 선택된 downstream skill들로 `workflow.md`를 다시 작성합니다. bootstrap skill들은 `workflow.md`에 절대 나타나지 않습니다.

두 bootstrap skill 모두 `human-clarification: false`와 `plan-creation: false`를 설정합니다. 그들의 결정은 정형화된 패턴을 따르기 때문입니다: 질문 파일은 builder의 추천(감사용으로 기록)으로 자동 답변되고, planning은 건너뛰며, 실행이 곧바로 진행됩니다. `workflow-composition`은 사람이 구성된 workflow를 여전히 승인하도록 `artefact-verification: true`를 유지합니다.

| Skill | Stage | Per-Unit | Human-Clar | Plan-Create | Plan-Verify | Artefact-Verify | Status |
|---|---|---|---|---|---|---|---|
| aidlc-intent-bootstrap       | intent-bootstrap       | No  | false | false | n/a   | false | ✅ |
| aidlc-workflow-composition   | workflow-composition   | No  | false | false | n/a   | true  | ✅ |

### Inception phase

| Skill | Stage | Per-Unit | Human-Clar | Plan-Create | Plan-Verify | Artefact-Verify | Status |
|---|---|---|---|---|---|---|---|
| aidlc-reverse-engineering    | reverse-engineering    | No  | true | true | true | true | ✅ |
| aidlc-requirements-analysis  | requirements-analysis  | No  | true | true | true | true | ✅ |
| aidlc-user-stories           | user-stories           | No  | true | true | true | true | ✅ |
| aidlc-wireframes             | wireframes             | No  | true | true | true | true | ✅ |
| aidlc-application-design     | application-design     | No  | true | true | true | true | ✅ |
| aidlc-units-generation       | units-generation       | No  | true | true | true | true | ✅ |

### Construction phase

| Skill | Stage | Per-Unit | Human-Clar | Plan-Create | Plan-Verify | Artefact-Verify | Status |
|---|---|---|---|---|---|---|---|
| aidlc-functional-design      | functional-design      | Yes | true | true | true | true | ✅ |
| aidlc-nfr-assessment         | nfr-assessment         | Yes | true | true | true | true | ✅ |
| aidlc-nfr-design             | nfr-design             | Yes | true | true | true | true | ✅ |
| aidlc-infrastructure-design  | infrastructure-design  | Yes | true | true | true | true | ✅ |
| aidlc-code-generation        | code-generation        | Yes | true | true | true | true | ✅ |
| aidlc-build-and-test         | build-and-test         | No  | true | true | true | true | 🚧 |

orchestrator 또는 사람은 workflow를 구성할 때 intent별로 flag를 재정의할 수 있습니다.

skill이 구현되면 Status를 ✅로 바꾸고, 폴더에 최소한 `SKILL.md`와 `validation-spec.md`가 들어 있는지 확인하십시오.
