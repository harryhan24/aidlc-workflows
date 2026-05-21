# Builder Protocol

builder의 실행 protocol입니다. 본 파일은 해당 skill의 `SKILL.md` 및 `validation-spec.md`와 함께 모든 builder에 패키징됩니다.

---

## 1. 입력

builder가 orchestrator로부터 받는 항목입니다.

- 입력 파일 경로(하나 이상)
- `aidlc-common/conventions/aidlc-folder-structure.md` 경로

각 호출에는 다음도 포함될 수 있습니다.
- 답변된 질문 파일 경로(clarification 이후)
- 승인된 plan 파일 경로(plan 승인 이후)
- validation report 경로(validation 실패 이후)

builder는 stateless입니다. 각 호출은 독립적입니다. 모든 상태는 디스크 상의 파일에 존재합니다.

## 2. Protocol

builder는 활성 skill의 `SKILL.md` frontmatter를 읽어 흐름을 결정합니다. builder 동작에 영향을 주는 플래그는 두 가지입니다.

- `human-clarification`(기본값 `"true"`) — `"false"`일 때 builder는 질문과 본인의 권장 답변을 모두 채워 질문 파일을 작성하고, clarification 상태를 `pending`에서 `awaiting-human → answered → complete`까지 단번에 전이시킨 뒤 다음으로 진행합니다. orchestrator는 사람에게 질문을 제시하지 않습니다. 질문 파일은 traceability를 위해 여전히 존재합니다.
- `plan-creation`(기본값 `"true"`) — `"false"`일 때 builder는 planning step을 완전히 건너뜁니다. 상태는 `clarification:complete`에서 곧바로 `execution:pending`을 거쳐 `execution:complete`로 전이됩니다. plan 파일은 생성되지 않습니다.

아래 모든 호출 경로에서 이 플래그들을 일관되게 적용하십시오.

### 2.1 입력 파일이 있는 호출

1. skill의 `SKILL.md`(frontmatter 플래그 포함)와 `validation-spec.md`를 읽습니다.
2. 제공된 경로의 모든 입력 파일을 읽습니다.
3. `aidlc-common/conventions/aidlc-folder-structure.md`로부터 출력 경로를 결정합니다.
4. 가용한 컨텍스트와 validation-spec을 바탕으로 clarification이 필요한지 판단합니다.

**clarification이 필요하고 `human-clarification: "true"`인 경우:**
- `aidlc-common/conventions/aidlc-question-format.md`를 사용하여 clarifying 질문을 생성합니다.
- 질문을 질문 파일에 기록합니다. `[Answer]:`는 비워 둡니다.
- state를 `clarification : awaiting-human`으로 전이합니다.
- orchestrator에게 반환: status `clarification-needed`, 질문 파일 경로.
- 중단합니다.

**clarification이 필요하고 `human-clarification: "false"`인 경우:**
- 동일한 형식으로 clarifying 질문을 생성하되, 각 질문의 `[Answer]:` 줄에 **권장 답변을 채워 넣습니다**. 각 답변 옆에 간단한 근거를 함께 기록합니다(기존 Recommendation 필드가 이미 rationale을 담고 있습니다).
- 파일을 기록합니다. 단일 pass로 상태를 `pending → awaiting-human → answered → complete`까지 갱신합니다(orchestrator는 관여하지 않습니다).
- planning으로 진행합니다(또는 `plan-creation: "false"`인 경우 execution으로 진행).

**clarification이 필요하지 않은 경우:**
- clarification을 곧바로 `complete`로 전이합니다(또는 질문 파일이 생성되지 않는다면 clarification 행을 완전히 생략 — 다만 skill 컨벤션이 요구하는 경우, 질문이 불필요했던 이유를 짧게 적은 질문 파일은 생성합니다).
- planning으로 진행합니다(또는 `plan-creation: "false"`인 경우 execution으로 진행).

### 2.2 답변된 질문이 있는 호출

1. 답변된 질문 파일을 읽습니다.
2. 답변에서 모호성(애매한 응답, 모순, 정의되지 않은 용어)을 분석합니다.

**모호성이 발견된 경우:**
- 후속 질문을 질문 파일에 기록합니다.
- orchestrator에게 반환: status `clarification-needed`, 질문 파일 경로.
- 중단합니다.

**답변이 명확한 경우:**
- Planning으로 진행합니다.

### 2.3 Planning

skill의 `plan-creation: "false"`인 경우 이 step을 완전히 건너뜁니다 — 곧바로 Execution으로 가고 `execution:pending → execution:complete`가 다음 전이가 되도록 합니다. 그렇지 않으면 다음과 같이 진행합니다.

1. plan 파일에 체크박스가 있는 plan을 생성합니다.
2. state를 `planning : awaiting-human`으로 전이합니다(`plan-verification`과 무관합니다 — 그 플래그는 orchestrator가 처리합니다).
3. orchestrator에게 반환: status `plan-ready`, plan 파일 경로.
4. 중단합니다.

### 2.4 실행 호출

1. plan을 실행합니다. skill의 `SKILL.md`에 정의된 대로 artifact를 생성합니다.
2. plan 체크박스를 완료 처리합니다.
3. `validation-spec.md`에 대해 자체 validation을 수행하지 마십시오. skill의 `scripts/` 디렉터리에 있는 어떤 것도 실행하지 마십시오. scripts는 오직 validator 전용입니다. 당신의 임무는 artifact를 생성하는 것이고, validation은 validator의 임무입니다.
4. state 파일의 Artifacts 열은 bare 파일명만 사용해 갱신합니다(예: `requirements.md`, `inception/requirements-analysis/requirements.md`가 아님).
5. orchestrator에게 반환: status `complete`, artifact 파일명.

### 2.5 Validation 실패 후 호출

1. 제공된 경로에서 validation report를 읽습니다.
2. 현재 artifact를 읽습니다.
3. 지적된 문제를 수정합니다.
4. artifact를 다시 작성합니다.
5. orchestrator에게 반환: status `complete`, 갱신된 artifact 경로, 질문 파일 경로.

---

## 3. 규칙(모든 skill에 적용)

1. clarification 여부를 판단하기 전에 `validation-spec.md`를 읽으십시오 — 어떤 질문을 해야 하는지, 어떤 기준에 맞춰 만들어야 하는지를 알려 줍니다.
2. 사람과 직접 상호작용하지 마십시오. 사람과의 모든 소통은 orchestrator를 통해 라우팅됩니다.
3. validator protocol을 읽지 마십시오.
4. **Scope-by-phase.** 후속 skill의 책임인 사안에 대해 묻거나 설계하지 마십시오.
   - **Inception phase skill**(requirements-analysis, user-stories, application-design): tech stack, framework, database, protocol, infrastructure, deployment에 대해 묻지 마십시오. 이는 construction(nfr-assessment 이후)의 책임입니다.
   - **application-design**: 논리적 동작만 기술합니다 — language, framework, database, protocol, broker, vendor에 특화된 세부 사항을 포함하지 않습니다.
   - **functional-design**: 기술 비종속적인 domain/business logic만 다룹니다.
5. **공백 처리.** 입력에 포함되지 않은 requirement 또는 기능이 발견되면, 후속 질문으로 제기하십시오. upstream 문서에 명시된 범위를 넘어 기능을 조용히 추가하지 마십시오.
6. **Brownfield 컨텍스트.** skill의 prerequisite에서 brownfield를 언급하는 경우, 가용한 어떤 출처에서 오는 brownfield 컨텍스트라도 수용하십시오: codekb, reverse-engineering artifact, 조직 단위 knowledge base, 또는 기존 codebase에 대한 LLM 분석 결과. 이를 skill의 `SKILL.md`에 다시 기술하지 마십시오.

## 4. State 파일 책임

builder는 다음의 state 전이를 `intent-state.md`에 기록합니다(위의 skill별 플래그에 따라 다름).

- `clarification:pending → clarification:awaiting-human`(질문 생성 후, `human-clarification: "true"`일 때)
- `clarification:awaiting-human → clarification:follow-up`(후속 질문 생성 후)
- `clarification:answered → clarification:complete`(답변이 명확함을 확인한 후, `human-clarification: "true"`일 때)
- `clarification:pending → clarification:awaiting-human → clarification:answered → clarification:complete`를 단일 pass로 처리(`human-clarification: "false"`일 때; builder가 자체 답변을 채워 넣음)
- `planning:pending → planning:awaiting-human`(plan 생성 후, `plan-creation: "true"`일 때만)
- `execution:pending → execution:complete`(artifact 생성 후)

`plan-creation: "false"`일 때 builder는 planning을 완전히 건너뛰며, state 행에는 `planning` step이 결코 등장하지 않습니다.

builder는 사람의 응답 전이(`awaiting-human → answered`, `awaiting-human → approved`)를 기록하지 않습니다. 이는 orchestrator가 기록합니다 — 단, `human-clarification: "false"`인 경우는 예외로, 사람이 loop에 들어가지 않으므로 builder가 clarification 전체 경로를 직접 기록합니다.

state 파일 형식, 유효 상태, 전이에 대해서는 `aidlc-common/conventions/aidlc-state-schema.md`를 참고하십시오.
