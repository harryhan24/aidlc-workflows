# Workflow 파일 포맷

workflow 파일은 intent 디렉터리 내의 `workflow.md`입니다. 한 줄에 하나의 skill 호출을 기록하는 단순한 텍스트 파일입니다. 각 줄은 skill 이름 뒤에 공백으로 구분된 입력 파일 경로들이 이어집니다.

```
<skill-name> <input-file-1> [<input-file-2> ...]
```

`per-unit: "true"`로 설정되어 unit별로 실행되는 construction phase skill의 경우, skill 이름 뒤에 `--unit <unit-name>`을 추가합니다.

```
<skill-name> --unit <unit-name> <input-file-1> [<input-file-2> ...]
```

`--unit`이 지정된 경우:
- artifact는 `inception/<skill-name>/` 대신 `construction/<unit-name>/<skill-name>/`에 작성됩니다.
- state key가 `<skill-name>:<unit-name>` 형식이 되어 동일한 skill을 unit별로 독립적으로 실행할 수 있습니다.

inception phase에 속하지 않으면서 `--unit`을 사용하지 않는 skill의 경우, artifact를 올바른 하위 트리로 보내기 위해 skill 이름 뒤에 `--phase <phase-name>`을 추가합니다.

```
<skill-name> --phase <phase-name> <input-file-1> [<input-file-2> ...]
```

`--phase`가 지정된 경우:
- artifact는 기본값인 `inception/<skill-name>/` 대신 `<phase-name>/<skill-name>/`에 작성됩니다.
- 이 옵션은 `bootstrap` 및 `operations` skill에 사용합니다.

동일한 phase 내에서 서로 다른 scope를 대상으로 여러 번 실행되는 skill(예: 한 번에 하나의 repo를 대상으로 하는 reverse-engineering)의 경우, skill 이름 뒤에 `--scope <scope-name>`을 추가합니다.

```
<skill-name> --scope <scope-name> <input-file-1> [<input-file-2> ...]
```

`--scope`가 지정된 경우:
- artifact는 `<phase>/<skill-name>/` 대신 `<phase>/<skill-name>/<scope-name>/`에 작성됩니다.
- state key는 `<skill-name>:<scope-name>` 형식이 되어 동일한 skill을 scope별로 독립적으로 실행할 수 있습니다.
- `--scope`는 phase에 무관하게 동작합니다 — skill이 속한 phase가 무엇이든 그 안에 하위 디렉터리를 하나 추가합니다.

`--scope`는 `reverse-engineering`에 필수입니다(repo가 하나뿐인 경우에도 항상 repo 이름으로 scope가 지정됩니다).

`--phase`, `--unit`, `--scope`는 상호 배타적입니다. `--unit`은 `construction`을 함의합니다. `--scope`는 skill에 선언된 phase를 그대로 유지합니다.

`#`으로 시작하는 줄은 주석입니다. 빈 줄은 무시됩니다.

## Bootstrap skill은 workflow.md에 포함되지 않습니다

`workflow.md`에는 downstream skill(inception, construction, operations)만 나열됩니다. 두 개의 bootstrap skill(`intent-bootstrap`과 `workflow-composition`)은 orchestrator의 bootstrap pre-loop를 통해 실행되며 `workflow.md`에는 절대 포함되지 않습니다.

- `intent-bootstrap`은 `workflow.md`가 존재하기 전에 실행되며, `workflow-composition`을 위한 stub 한 줄을 포함한 파일을 생성합니다.
- `workflow-composition`은 그 stub을 읽은 뒤, 선택된 downstream skill들로 `workflow.md`를 처음부터 다시 작성합니다.

`process_checker`가 표준 skill 루프를 구동하기 위해 `workflow.md`를 읽는 시점에는, 두 bootstrap skill 모두 이미 완료되어 있고 파일에는 downstream skill 줄만 남아 있습니다.

## 파일 확장자

이 파일의 이름은 **반드시** `workflow.md`여야 합니다. `process_checker`는 이 정확한 파일명을 찾습니다.

## 예시

```
# Inception phase
reverse-engineering --scope payments-api org-ai-kb/aidlc-docs/intent-001-ideation-portal/intent.md
reverse-engineering --scope shared-auth org-ai-kb/aidlc-docs/intent-001-ideation-portal/intent.md
requirements-analysis org-ai-kb/aidlc-docs/intent-001-ideation-portal/intent.md
user-stories org-ai-kb/aidlc-docs/intent-001-ideation-portal/inception/requirements-analysis/requirements.md
application-design org-ai-kb/aidlc-docs/intent-001-ideation-portal/inception/requirements-analysis/requirements.md org-ai-kb/aidlc-docs/intent-001-ideation-portal/inception/user-stories/stories.md org-ai-kb/aidlc-docs/intent-001-ideation-portal/inception/user-stories/personas.md

# Construction phase — per-unit skills
functional-design --unit recommendation-engine org-ai-kb/aidlc-docs/intent-001-ideation-portal/inception/units-generation/units-of-work.md org-ai-kb/aidlc-docs/intent-001-ideation-portal/inception/user-stories/stories.md org-ai-kb/aidlc-docs/intent-001-ideation-portal/inception/application-design/components.md
functional-design --unit data-collection org-ai-kb/aidlc-docs/intent-001-ideation-portal/inception/units-generation/units-of-work.md org-ai-kb/aidlc-docs/intent-001-ideation-portal/inception/user-stories/stories.md org-ai-kb/aidlc-docs/intent-001-ideation-portal/inception/application-design/components.md
```

markdown 테이블을 사용하지 마십시오. 헤더나 별도의 서식을 추가하지 마십시오. 스크립트는 이 파일을 한 줄씩 파싱합니다.
