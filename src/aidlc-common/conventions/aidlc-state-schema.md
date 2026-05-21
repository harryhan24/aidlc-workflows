# State Schema

`intent-state.md` 파일 포맷과 그 파일이 추적하는 state machine에 대한 단일 진실 공급원(single source of truth)입니다.

## 파일 포맷

```markdown
# Intent State

intent: <intent-name>
created: <timestamp>
updated: <timestamp>

## Workflow Progress

| Skill | Step | Status | Attempt | Artifacts |
|---|---|---|---|---|
| <skill-name> | <step> | <status> | <n> | <comma-separated bare filenames or —> |
```

## 규칙

### 1. skill 하나당 한 행

각 skill은 정확히 하나의 행을 가집니다. state를 업데이트할 때는 해당 skill 이름의 기존 행을 찾아 그 행의 값을 교체합니다. 새 행을 추가하지 마십시오 — 중복된 행은 스크립트 파싱을 깨뜨립니다.

### 2. state key

- Inception skill: Skill 열에 skill 이름이 들어갑니다(예: `requirements-analysis`).
- Construction skill(per-unit): Skill 열에 `<skill-name>:<unit-name>` 형식이 들어갑니다(예: `functional-design:auth-service`).
- Scoped skill: Skill 열에 `<skill-name>:<scope-name>` 형식이 들어갑니다(예: `reverse-engineering:payments-api`).

### 3. Artifacts 열

전체 경로가 아닌 파일명만 적습니다(예: `inception/requirements-analysis/requirements.md`가 아니라 `requirements.md`).

`process_checker`는 다음 기준으로 상대 경로를 해석합니다.
- inception: `inception/<skill>/`
- inception(scoped): `inception/<skill>/<scope>/`
- construction: `construction/<unit>/<skill>/`

쉼표로 구분하며, 없을 경우 `—`로 표기합니다.

### 4. 쓰기 책임

`aidlc-common/protocols/aidlc-orchestrator-protocol.md`의 "State write responsibilities"를 참조하십시오.

## 스크립트 파싱 계약

스크립트는 다음과 같은 패턴으로 테이블을 파싱합니다.

```bash
grep "<skill-name>" intent-state.md | awk -F'|' '{print $3, $4}'
```

정확한 열 레이아웃을 유지해야 합니다.

---

## 유효한 state

| Step | Status | 의미 |
|---|---|---|
| — | not-started | Skill이 아직 시작되지 않음 |
| clarification | pending | Builder가 질문을 생성해야 함 |
| clarification | awaiting-human | 질문 작성 완료, 답변 대기 중 |
| clarification | answered | 사용자가 답변함, 모호성 검토 중 |
| clarification | follow-up | 답변이 모호하여 후속 질문 생성됨 |
| clarification | complete | 답변이 명확함, plan 단계 진행 가능 |
| planning | pending | Builder가 plan을 작성해야 함 |
| planning | awaiting-human | Plan 작성 완료, 승인 대기 중 |
| planning | revision-requested | 사용자가 수정을 요청함 |
| planning | approved | Plan 승인됨, 실행 준비 완료 |
| execution | pending | Builder가 artifact를 생성해야 함 |
| execution | complete | Artifact 작성 완료 |
| validation | pending | Validator 실행 필요 |
| validation | pass | 모든 검증 통과 |
| validation | fail | 하나 이상의 검증 실패 |
| verification | awaiting-human | 사용자 검토를 위해 artifact 제출됨 |
| verification | approved | 사용자가 승인함 |
| verification | rejected | 사용자가 거절함, 재작업 필요 |
| — | halting | 재시도 횟수 소진, 사용자에게 에스컬레이션됨 |
| — | complete | Skill 종료 |

## 유효한 전이

아래 전이들은 모든 flag가 `"true"`(기본값)인 경우의 전체 경로를 설명합니다. 두 개의 flag는 이 경로를 단축시킬 수 있습니다.

- `human-clarification: "false"` — builder가 한 번에 전체 clarification 경로(`pending → awaiting-human → answered → complete`)를 작성하며, 사용자에게 묻지 않습니다.
- `plan-creation: "false"` — planning 단계 자체가 생략되며, `clarification:complete → execution:pending`으로 곧바로 전이됩니다.

```
— : not-started                   → clarification : pending

clarification : pending            → clarification : awaiting-human
clarification : awaiting-human     → clarification : answered
clarification : answered           → clarification : follow-up
clarification : answered           → clarification : complete
clarification : follow-up          → clarification : awaiting-human
clarification : complete           → planning : pending           (when plan-creation is true)
clarification : complete           → execution : pending          (when plan-creation is false)

planning : pending                 → planning : awaiting-human
planning : awaiting-human          → planning : approved
planning : awaiting-human          → planning : revision-requested
planning : revision-requested      → planning : awaiting-human
planning : approved                → execution : pending

execution : pending                → execution : complete
execution : complete               → validation : pending

validation : pending               → validation : pass
validation : pending               → validation : fail
validation : pass                  → verification : awaiting-human   (if artefact-verification flag)
validation : pass                  → — : complete                    (if no artefact-verification flag)
validation : fail                  → execution : pending             (if retries left)
validation : fail                  → — : halting                     (if no retries)

verification : awaiting-human      → verification : approved
verification : awaiting-human      → verification : rejected
verification : approved            → — : complete
verification : rejected            → execution : pending             (increment attempt)
```

## Attempt 카운터

- 1에서 시작합니다.
- validation 실패 후 재시도 시, 그리고 verification 거절 시 증가합니다.
- 절대 감소하지 않습니다.
- 최대값은 설정으로 정의됩니다(기본값: 3).
- 최대값에 도달한 상태에서 validation이 실패하면 halting으로 진입합니다.
