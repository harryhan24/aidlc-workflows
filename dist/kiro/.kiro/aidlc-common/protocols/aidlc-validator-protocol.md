# Validator Protocol

validator의 실행 protocol입니다. 본 파일은 validator 동작의 단일 진실 공급원입니다. 해당 skill의 `validation-spec.md`와 함께 패키징됩니다.

---

## 1. 입력

validator가 orchestrator로부터 받는 항목입니다.

- 검증할 artifact 경로
- 답변된 질문 파일 경로
- upstream artifact 경로(traceability 검사용) — skill의 `validation-spec.md` "Inputs" 섹션에 명시됨
- skill 출력 디렉터리 경로
- skill scripts 디렉터리 경로(skill에 script가 없으면 부재할 수 있음)

## 2. Protocol

1. `validation-spec.md`를 읽습니다("Inputs" 섹션 포함).
2. 제공된 경로의 모든 artifact를 읽습니다.
3. `validation-spec.md`에 나열된 모든 upstream artifact를 읽습니다.
4. 답변된 질문 파일을 읽습니다.
5. skill의 `scripts/` 디렉터리에 있는 모든 script를 **정확히 한 번씩** 실행합니다. 각각의 출력과 종료 코드를 캡처합니다. 디렉터리가 없거나 비어 있으면 "no scripts"로 기록합니다. script가 하나라도 실패하면, 다른 발견과 무관하게 전체 validation status는 반드시 `fail`이어야 합니다 — 다만 멈추지는 말고 남은 script를 먼저 실행하십시오.
6. 검증합니다.
   - **Spec compliance** — `validation-spec.md`의 모든 규칙을 artifact에 대해 확인합니다.
   - **Script results** — step 5에서 캡처한 종료 코드를 findings에 반영합니다. script를 다시 실행하지 마십시오.
   - **Clarification consistency** — artifact가 질문 파일의 답변과 일관되는지 확인합니다.
   - **Completeness** — spec이 예상하지 못한 공백(누락된 커버리지, 명시되지 않은 가정, 논리적 비일관성).
7. validation report를 skill 출력 폴더에 기록합니다.
8. orchestrator에게 반환: status `pass` 또는 `fail`, validation report 경로.

## 3. Validation Report 형식

report는 두 부분으로 구성됩니다.

### 3.1 사람이 읽는 섹션

적절한 markdown 형식이라면 어떤 것이든 사용해 작성합니다. 다음 항목을 포함합니다.

- **Status:** `pass` 또는 `fail`
- **Rules checked:** validation-spec 규칙 목록과 규칙별 pass/fail
- **Scripts invoked:** `scripts/` 내 모든 script와 종료 코드 및 출력
- **Findings:** 각 실패에 대해 위반된 규칙(또는 실패한 script), 위반이 발생한 artifact와 섹션, 그리고 문제 설명
- **Recommendations:** 권장 수정 사항(validator는 수정하지 않고 권고만 합니다)

### 3.2 기계가 읽는 블록

report의 가장 끝에 고정된 delimiter가 있는 plain-text 블록을 덧붙입니다. 이 블록은 `process_checker`가 파싱합니다. 형식은 정확해야 합니다 — markdown 금지, 추가 공백 금지, 변형 금지.

```
---PROCESS-CHECK-DATA---
STATUS: PASS
TOOLS: verify-structure.sh,check-coverage.py
RULES: 1,2,3,4,5
---END-PROCESS-CHECK-DATA---
```

규칙:
- `STATUS`는 정확히 `PASS` 또는 `FAIL`이어야 합니다(대문자).
- `TOOLS`는 실행된 script 파일명을 쉼표로 구분한 목록입니다. script가 존재하지 않으면 `TOOLS: none`을 사용합니다. (필드 이름이 `TOOLS`인 이유는 process_checker 하위 호환성 때문이며, 실제로는 `scripts/`의 파일명을 담습니다.)
- `RULES`는 검사한 `validation-spec.md` 규칙 번호의 쉼표 구분 목록입니다.
- delimiter는 정확히 위에 나타낸 그대로여야 합니다.
- 이 블록은 파일의 마지막에 위치해야 합니다.

## 4. 규칙(모든 skill에 적용)

1. artifact를 절대 수정하지 마십시오. 검증하고 보고만 하십시오.
2. 사람과 직접 상호작용하지 마십시오.
3. builder protocol 또는 skill의 `SKILL.md`를 읽지 마십시오. artifact가 어떻게 생성되었는지는 알 필요 없으며, 오직 spec을 충족하는지만 판단합니다.
4. 이전 validation 실행의 컨텍스트를 이어 가지 마십시오.

## 5. State 파일 책임

validator는 다음의 state 전이를 `intent-state.md`에 기록합니다.

- `validation:pending → validation:pass`(모든 검사 통과 후)
- `validation:pending → validation:fail`(하나 이상의 검사 실패 후)

validator는 그 외의 state 전이를 기록하지 않습니다.

state 파일 형식은 `aidlc-common/conventions/aidlc-state-schema.md`를 참고하십시오.
