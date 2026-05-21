# Mid-Workflow Changes and Stage Management

## Overview

사용자는 workflow 중 execution plan 또는 stage execution 변경을 요청할 수 있습니다. 이 문서는 이러한 요청을 안전하고 효과적으로 처리하기 위한 지침을 제공합니다.

---

## Types of Mid-Workflow Changes

### 1. Adding a Skipped Stage

**Scenario**: 사용자가 원래 건너뛴 stage를 추가하려고 합니다.

**Example**: "Actually, I want to add user stories even though we skipped that stage"

**Handling**:

1. **Confirm Request**: "User Stories stage를 추가하려고 합니다. 이 stage는 user stories와 personas를 생성합니다. 확인하시겠습니까?"
2. **Check Dependencies**: 모든 prerequisite stages가 완료되었는지 확인합니다.
3. **Update Execution Plan**: rationale과 함께 stage를 `execution-plan.md`에 추가합니다.
4. **Update State**: `aidlc-state.md`에서 stage를 "PENDING"으로 표시합니다.
5. **Execute Stage**: 일반 stage execution process를 따릅니다.
6. **Log Change**: timestamp 및 reason과 함께 `audit.md`에 문서화합니다.

**Considerations**:

- 새 artifact의 이점을 받을 수 있는 later stages를 업데이트해야 할 수 있습니다.
- 기존 artifact에 새 정보를 반영하기 위한 revision이 필요할 수 있습니다.
- timeline이 늘어납니다.

---

### 2. Skipping a Planned Stage

**Scenario**: 사용자가 실행하기로 계획된 stage를 건너뛰려고 합니다.

**Example**: "Let's skip the NFR Design stage for now"

**Handling**:

1. **Confirm Request**: "NFR Design을 건너뛰려고 합니다. 이는 NFR patterns 또는 logical components가 반영되지 않음을 의미합니다. 확인하시겠습니까?"
2. **Warn About Impact**: 빠지는 항목과 잠재적 consequences를 설명합니다.
3. **Get Explicit Confirmation**: 사용자가 impact를 이해했음을 명시적으로 확인해야 합니다.
4. **Update Execution Plan**: reason과 함께 stage를 "SKIPPED"로 표시합니다.
5. **Update State**: `aidlc-state.md`에서 stage를 "SKIPPED"로 표시합니다.
6. **Adjust Later Stages**: later stages에 manual setup이 필요할 수 있음을 기록합니다.
7. **Log Change**: timestamp 및 reason과 함께 `audit.md`에 문서화합니다.

**Considerations**:

- later stages가 실패하거나 manual intervention을 요구할 수 있습니다.
- 사용자는 missing artifacts에 대한 responsibility를 수락합니다.
- 필요하면 나중에 다시 추가할 수 있습니다.

---

### 3. Restarting Current Stage

**Scenario**: 사용자가 현재 stage 결과에 만족하지 않아 다시 수행하려고 합니다.

**Example**: "I don't like these user stories. Can we start over?"

**Handling**:

1. **Understand Concern**: "stories에서 구체적으로 무엇을 바꾸고 싶으신가요?"
2. **Offer Options**:
   - **Option A**: 기존 artifacts 수정(더 빠르고 일부 작업 보존)
   - **Option B**: complete restart(clean slate, 더 많은 시간 필요)
3. **If Restart Chosen**:
   - 기존 artifacts archive: `{artifact}.backup.{timestamp}`
   - plan file의 stage checkboxes reset
   - `aidlc-state.md`에서 stage를 "IN PROGRESS"로 표시
   - stage completion status clear
   - 처음부터 다시 실행
4. **Log Change**: restart 이유와 변경될 내용을 문서화합니다.

**Considerations**:

- 기존 work는 손실됩니다(backup은 유지).
- dependent stages를 다시 수행해야 할 수 있습니다.
- timeline이 늘어납니다.

---

### 4. Restarting Previous Stage

**Scenario**: 사용자가 완료된 stage로 돌아가 다시 수행하려고 합니다.

**Example**: "I want to change the architectural decision we made earlier"

**Handling**:

1. **Assess Impact**: restart할 stage에 의존하는 모든 stage를 식별합니다.
2. **Warn User**: "Application Design을 다시 시작하면 Units Generation, per-unit design(모든 units), Code Generation을 다시 수행해야 합니다. 확인하시겠습니까?"
3. **Get Explicit Confirmation**: 사용자가 전체 impact를 이해해야 합니다.
4. **If Confirmed**:
   - 영향을 받는 모든 artifacts를 archive합니다.
   - `aidlc-state.md`에서 영향을 받는 모든 stage를 reset합니다.
   - 영향을 받는 모든 plan files의 checkboxes를 clear합니다.
   - restart할 stage로 돌아갑니다.
   - 그 지점부터 다시 실행합니다.
5. **Log Change**: 전체 impact와 restart reason을 문서화합니다.

**Considerations**:

- 상당한 rework가 필요합니다.
- 모든 dependent stages를 다시 수행해야 합니다.
- timeline이 크게 늘어납니다.
- restart보다 modification이 나은지 고려합니다.

---

### 5. Changing Stage Depth

**Scenario**: 사용자가 현재 또는 upcoming stage의 depth level을 바꾸려고 합니다.

**Example**: "Let's do a comprehensive requirements analysis instead of standard"

**Handling**:

1. **Confirm Request**: "Requirements Analysis를 Standard에서 Comprehensive depth로 변경하려고 합니다. 더 철저하지만 시간이 더 걸립니다. 확인하시겠습니까?"
2. **Update Execution Plan**: `workflow-planning.md`의 depth level을 변경합니다.
3. **Adjust Approach**: 해당 stage의 comprehensive depth guidelines를 따릅니다.
4. **Update Estimates**: 새 timeline estimate를 사용자에게 알립니다.
5. **Log Change**: depth change와 reason을 문서화합니다.

**Considerations**:

- 더 깊은 depth = 더 많은 시간, 더 나은 quality
- 낮은 depth = 더 빠르지만 detail 누락 가능
- 완료 후에는 변경할 수 없고, stage 전 또는 stage 중에만 변경할 수 있습니다.

---

### 6. Pausing Workflow

**Scenario**: 사용자가 중단하고 나중에 재개해야 합니다.

**Example**: "I need to stop for now and continue tomorrow"

**Handling**:

1. **Complete Current Step**: 가능하면 진행 중인 current step을 완료합니다.
2. **Update Checkboxes**: 완료된 모든 steps를 [x]로 표시합니다.
3. **Update State**: `aidlc-state.md`가 current status를 반영하도록 합니다.
4. **Log Pause**: `audit.md`에 pause point를 문서화합니다.
5. **Provide Resume Instructions**: "돌아오시면 기존 project를 감지하고 다음 지점부터 이어갈 수 있도록 안내하겠습니다: [current stage, current step]"

**On Resume**:

1. **Detect Existing Project**: `aidlc-state.md`가 있는지 확인합니다.
2. **Load Context**: 완료된 stages의 모든 artifacts를 읽습니다.
3. **Show Status**: current stage와 next step을 표시합니다.
4. **Offer Options**: 중단한 지점부터 계속하거나 이전 work를 review하도록 제안합니다.
5. **Log Resume**: `audit.md`에 resume point를 문서화합니다.

---

### 7. Changing Architectural Decision

**Scenario**: 사용자가 monolith에서 microservices로 또는 그 반대로 바꾸려고 합니다.

**Example**: "Actually, let's do microservices instead of a monolith"

**Handling**:

1. **Assess Current Progress**: workflow가 얼마나 진행되었는지 판단합니다.
2. **Explain Impact**:
   - Units Generation 전: impact가 작으며 decision만 업데이트하면 됩니다.
   - Units Generation 후: Units Generation과 모든 per-unit design을 다시 수행해야 합니다.
   - Code Generation 후: significant rework가 필요합니다.
3. **Recommend Approach**:
   - workflow 초반: Application Design stage부터 restart
   - workflow 후반: restart보다 modification이 feasible한지 검토
4. **Get Confirmation**: 사용자가 전체 change scope를 이해해야 합니다.
5. **Execute Change**: 영향을 받는 stages에 대해 restart procedures를 따릅니다.

**Considerations**:

- architectural changes는 cascading effects가 있습니다.
- workflow 초반일수록 변경이 쉽습니다.
- workflow 후반에는 cost vs. benefit을 고려합니다.

---

### 8. Adding/Removing Units

**Scenario**: 사용자가 Units Generation 후 unit을 추가하거나 제거하려고 합니다.

**Example**: "We need to split the Payment unit into Payment and Billing"

**Handling**:

1. **Assess Impact**: design/code가 완료된 unit이 무엇인지 판단합니다.
2. **Explain Consequences**:
   - unit 추가: 새 unit에 대해 full design and code가 필요합니다.
   - unit 제거: functionality를 다른 unit으로 재분배해야 합니다.
   - unit 분할: resulting units 양쪽의 design and code를 다시 수행해야 합니다.
3. **Update Unit Artifacts**:
   - `unit-of-work.md` 수정
   - `unit-of-work-dependency.md` 업데이트
   - `unit-of-work-story-map.md` 수정
4. **Reset Affected Units**: 영향을 받는 unit을 redesign 필요 상태로 표시합니다.
5. **Execute Changes**: 영향을 받는 units에 대해 일반 unit design and code process를 따릅니다.

**Considerations**:

- 해당 unit들의 모든 downstream stages에 영향을 줍니다.
- dependencies가 바뀌면 다른 unit에도 영향을 줄 수 있습니다.
- timeline impact는 영향을 받는 unit 수에 따라 달라집니다.

---

## General Guidelines for Handling Changes

### Before Making Changes

1. **Understand the Request**: 사용자가 무엇을 왜 바꾸고 싶은지 clarifying questions로 확인합니다.
2. **Assess Impact**: 영향을 받는 stages, artifacts, dependencies를 모두 식별합니다.
3. **Explain Consequences**: 무엇을 다시 해야 하는지와 timeline impact를 명확하게 전달합니다.
4. **Offer Alternatives**: 때로는 restart보다 modification이 낫습니다.
5. **Get Explicit Confirmation**: 사용자가 impact를 이해하고 수락해야 합니다.

### During Changes

1. **Archive Existing Work**: destructive changes 전에 항상 backup합니다.
2. **Update All Tracking**: `aidlc-state.md`, plan files, `audit.md`를 동기화합니다.
3. **Communicate Progress**: 진행 중인 일을 사용자에게 계속 알립니다.
4. **Validate Changes**: 모든 artifacts에서 change가 일관적인지 확인합니다.
5. **Test Continuity**: change 후 workflow가 원활하게 이어질 수 있는지 검증합니다.

### After Changes

1. **Verify Consistency**: 모든 artifacts가 changes와 정렬되어 있는지 확인합니다.
2. **Update Documentation**: 모든 reference가 업데이트되었는지 확인합니다.
3. **Log Completely**: `audit.md`에 전체 change history를 문서화합니다.
4. **Confirm with User**: changes가 사용자 기대에 맞는지 확인합니다.
5. **Resume Workflow**: 새 state에서 normal execution을 계속합니다.

---

## Change Request Decision Tree

```text
사용자가 change 요청
    |
    ├─ current stage인가?
    |   ├─ Yes: current stage를 modify 또는 restart 가능
    |   └─ No: 다음 질문으로 이동
    |
    ├─ completed stage인가?
    |   ├─ Yes: dependent stages에 미치는 impact 평가
    |   |   ├─ Low impact: modify하고 dependents 업데이트
    |   |   └─ High impact: 해당 stage부터 restart 권장
    |   └─ No: 다음 질문으로 이동
    |
    ├─ skipped stage 추가인가?
    |   ├─ Yes: prerequisites 확인, plan에 추가, execute
    |   └─ No: 다음 질문으로 이동
    |
    ├─ planned stage skip인가?
    |   ├─ Yes: impact 경고, confirmation 확보, skip
    |   └─ No: 다음 질문으로 이동
    |
    └─ depth level 변경인가?
        ├─ Yes: plan 업데이트, approach 조정
        └─ No: 사용자에게 request clarification
```

---

## Logging Requirements

### Change Request Log Format

```markdown
## Change Request - [Stage Name]
**Timestamp**: [ISO timestamp]
**Request**: [What user wants to change]
**Current State**: [Where we are in workflow]
**Impact Assessment**: [What will be affected]
**User Confirmation**: [User's explicit confirmation]
**Action Taken**: [What was done]
**Artifacts Affected**: [List of files changed/reset]

---
```

---

## Best Practices

1. **Always Confirm**: explicit user confirmation 없이 destructive changes를 절대 수행하지 마세요.
2. **Explain Impact**: 사용자는 결정 전에 consequences를 이해해야 합니다.
3. **Offer Options**: change를 처리하는 방법은 여러 가지일 수 있습니다.
4. **Archive First**: destructive changes 전에는 항상 backup합니다.
5. **Update Everything**: 모든 tracking files를 동기화합니다.
6. **Log Thoroughly**: audit trail을 위해 모든 changes를 문서화합니다.
7. **Validate After**: workflow가 원활하게 이어질 수 있는지 확인합니다.
8. **Be Flexible**: workflow는 rigid process를 강제하지 말고 user needs에 맞게 적응해야 합니다.
