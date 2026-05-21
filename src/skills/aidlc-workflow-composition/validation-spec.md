# Workflow Composition — Validation Spec

## 입력

- Artifacts: `workflow.md` (intent 루트), `workflow-rationale.md`
- Answered question file: `workflow-composition-questions.md`
- Upstream: `intent.md`, `bootstrap-context.md`, `skills/aidlc-orchestrator/CATALOGUE.md`

## 규칙

1. `workflow.md`는 intent 루트에 존재해야 하며 최소 하나의 주석이 아닌, 비어 있지 않은 라인을 포함해야 합니다.
2. `workflow.md`는 `intent-bootstrap` 또는 `workflow-composition` 라인을 포함해서는 안 됩니다 — 이 skill이 파일을 쓰는 시점에는 두 bootstrap skill이 모두 완료된 상태입니다. 모든 라인은 downstream skill이어야 합니다.
3. `workflow.md`의 모든 skill 이름은 `CATALOGUE.md`에 존재해야 합니다.
4. 모든 라인은 `aidlc-workflow-format.md` 문법을 따라야 합니다 — skill 이름이 먼저, 선택적 `--phase`/`--unit` 플래그, 그 다음 입력 파일 경로 순입니다.
5. `workflow.md`의 construction-phase skill은 `--phase construction`(단일 실행) 또는 `--unit <unit-name>`(per-unit fan-out, construction을 함의) 중 하나를 포함해야 합니다. operations-phase skill은 `--phase operations`를 포함해야 합니다. inception-phase skill은 두 플래그를 모두 생략해야 합니다. 이는 folder-structure 컨벤션에 따라 artifact를 올바른 subtree로 라우팅합니다.
6. `workflow-rationale.md`는 각 downstream skill에 대해 포함 또는 스킵 사유를 설명하는 bullet을 포함해야 합니다.
