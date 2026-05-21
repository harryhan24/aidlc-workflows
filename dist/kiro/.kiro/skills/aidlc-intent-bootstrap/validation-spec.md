# Intent Bootstrap — Validation Spec

## 입력

- Artifacts: `intent.md` (intent 루트), `bootstrap-context.md` (`bootstrap/intent-bootstrap/` 내)
- Skeleton files: `intent-prompt.md`, `state/intent-state.md`, `audit/intent-audit.md`, `workflow.md` (모두 intent 루트)
- Answered question file: `intent-bootstrap-questions.md` (`bootstrap/intent-bootstrap/` 내)
- Upstream: 없음 — 이 skill은 orchestrator로부터 intent 진술을 직접 받아 나머지 모든 것을 생성합니다

## 규칙

1. intent 디렉터리는 `org-ai-kb/aidlc-docs/` 아래에 존재해야 하며, `intent-<nnn>-<slug>/` 패턴을 따라야 합니다. 여기서 `<nnn>`은 0으로 패딩된 3자리 숫자이고 `<slug>`은 kebab-case입니다.
2. `intent-prompt.md`는 intent 루트에 존재해야 하며 사용자 프롬프트를 그대로 포함해야 합니다.
3. `state/intent-state.md`는 존재해야 하며 `aidlc-common/conventions/aidlc-state-schema.md`에 정의된 헤더 형식과 일치해야 합니다(intent 이름, created/updated 타임스탬프, Workflow Progress 표 헤더).
4. `audit/intent-audit.md`는 intent 루트에 존재해야 합니다.
5. `workflow.md`는 intent 루트에 존재해야 하며, 정확히 하나의 주석이 아닌, 비어 있지 않은 라인을 포함해야 합니다: `--phase bootstrap`을 호출하는 `workflow-composition` 라인입니다. `intent-bootstrap` 라인을 포함해서는 안 됩니다.
6. `intent.md`는 intent 루트에 존재해야 하며 다음을 포함해야 합니다: 사용자 프롬프트 원문, 요약, slug, type.
7. `bootstrap-context.md`는 `bootstrap/intent-bootstrap/` 내에 존재해야 하며 다음을 기재해야 합니다: classification(greenfield, brownfield, 또는 mixed), 범위 내 repo(또는 "none"), codekb 상태, reverse-engineering 결정.
8. `intent.md`의 slug는 intent 디렉터리 이름의 slug와 일치해야 합니다.
9. `bootstrap-context.md`의 classification, repo, reverse-engineering 결정은 `intent-bootstrap-questions.md`의 답변과 일관되어야 합니다.
