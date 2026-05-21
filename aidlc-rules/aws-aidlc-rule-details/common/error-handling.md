# Error Handling and Recovery Procedures

## General Error Handling Principles

### When Errors Occur

1. **Identify the error**: 무엇이 잘못되었는지 명확히 말합니다.
2. **Assess impact**: error가 blocking인지, workaround가 가능한지 판단합니다.
3. **Communicate**: 사용자에게 error와 options를 알립니다.
4. **Offer solutions**: error를 해결하거나 우회할 clear steps를 제공합니다.
5. **Document**: error와 resolution을 `audit.md`에 기록합니다.

### Error Severity Levels

**Critical**: workflow를 계속할 수 없음

- 필수 files 또는 artifacts 누락
- 처리할 수 없는 invalid user input
- file operations를 막는 system errors

**High**: stage를 계획대로 완료할 수 없음

- required questions에 대한 incomplete answers
- contradictory user responses
- prior stages의 missing dependencies

**Medium**: workaround로 stage를 계속할 수 있음

- optional artifacts 누락
- non-critical validation failures
- partial completion 가능

**Low**: 진행을 막지 않는 minor issues

- formatting inconsistencies
- optional information 누락
- non-blocking warnings

## Stage-Specific Error Handling

### Workspace Detection Errors

**Error**: workspace files를 읽을 수 없음

- **Cause**: permission issues, missing directories
- **Solution**: 사용자에게 workspace path와 permissions를 확인하도록 요청합니다.
- **Workaround**: user-provided information만 사용해 진행합니다.

**Error**: 기존 `aidlc-state.md`가 corrupted됨

- **Cause**: manual editing, incomplete previous run
- **Solution**: fresh start 또는 recovery attempt 중 무엇을 원하는지 사용자에게 묻습니다.
- **Recovery**: backup을 만들고 새 state file을 시작합니다.

**Error**: required stages를 결정할 수 없음

- **Cause**: 사용자 정보 부족
- **Solution**: intent와 scope에 대한 clarifying questions를 묻습니다.
- **Workaround**: comprehensive execution plan을 기본값으로 사용합니다.

### Requirements Analysis Errors

**Error**: 사용자가 contradictory requirements를 제공함

- **Cause**: unclear understanding, changing needs
- **Solution**: contradictions를 해결하기 위한 follow-up questions를 만듭니다.
- **Do Not Proceed**: contradictions가 해결될 때까지 진행하지 마세요.

**Error**: requirements document를 변환할 수 없음

- **Cause**: unsupported format, corrupted file
- **Solution**: 사용자에게 supported format으로 requirements를 제공하도록 요청합니다.
- **Workaround**: 사용자의 verbal description으로 작업합니다.

**Error**: verification questions에 대한 incomplete answers

- **Cause**: 사용자가 questions를 건너뛰었거나 무엇을 답해야 할지 불명확함
- **Solution**: unanswered questions를 강조하고 examples를 제공합니다.
- **Do Not Proceed**: 모든 required questions에 답할 때까지 진행하지 마세요.

### User Stories Errors

**Error**: requirements를 stories로 mapping할 수 없음

- **Cause**: requirements가 너무 vague하거나 functional details가 누락됨
- **Solution**: clarification을 위해 Requirements Analysis로 돌아갑니다.
- **Workaround**: available information을 바탕으로 stories를 만들고 incomplete로 표시합니다.

**Error**: 사용자가 ambiguous story planning answers를 제공함

- **Cause**: unclear options, complex decision
- **Solution**: specific examples가 포함된 follow-up questions를 추가합니다.
- **Do Not Proceed**: ambiguities가 해결될 때까지 진행하지 마세요.

**Error**: Story generation plan에 uncompleted steps가 있음

- **Cause**: execution interrupted, steps skipped
- **Solution**: 첫 번째 uncompleted step부터 resume합니다.
- **Recovery**: completed steps를 검토하고 checkpoint부터 계속합니다.

### Application Design Errors

**Error**: architectural decision이 unclear 또는 contradictory함

- **Cause**: ambiguous answers, conflicting requirements
- **Solution**: decision을 명확히 하기 위한 follow-up questions를 추가합니다.
- **Do Not Proceed**: decision이 명확하고 문서화될 때까지 진행하지 마세요.

**Error**: services/units 수를 결정할 수 없음

- **Cause**: boundaries에 대한 정보 부족
- **Solution**: deployment, team structure, scaling에 대한 specific questions를 묻습니다.
- **Workaround**: monolith를 기본값으로 사용하고 나중에 변경할 수 있게 합니다.

### Design Errors

**Error**: Unit dependencies가 circular함

- **Cause**: poor boundary definition, tight coupling
- **Solution**: circular dependencies를 식별하고 refactoring을 제안합니다.
- **Recovery**: cycles를 끊도록 unit boundaries를 수정합니다.

**Error**: Unit design plan에 missing steps가 있음

- **Cause**: plan generation incomplete, template error
- **Solution**: 모든 required steps를 포함해 plan을 regenerate합니다.
- **Recovery**: 기존 plan에 missing steps를 추가합니다.

**Error**: design artifacts를 생성할 수 없음

- **Cause**: missing unit information, unclear requirements
- **Solution**: unit definition을 명확히 하기 위해 Units Generation으로 돌아갑니다.
- **Workaround**: partial design을 생성하고 gaps를 표시합니다.

### NFR Implementation Errors

**Error**: technology stack choices가 incompatible함

- **Cause**: conflicting requirements, platform limitations
- **Solution**: incompatibilities를 강조하고 사용자에게 선택하도록 요청합니다.
- **Do Not Proceed**: compatible choices가 정해질 때까지 진행하지 마세요.

**Error**: organizational constraints를 충족할 수 없음

- **Cause**: network restrictions, security policies
- **Solution**: constraints를 문서화하고 사용자에게 workarounds를 요청합니다.
- **Escalation**: setup을 위해 human intervention이 필요할 수 있습니다.

**Error**: NFR implementation step에 human action이 필요함

- **Cause**: AI가 수행할 수 없는 작업(network config, credentials 등)
- **Solution**: **HUMAN TASK**로 명확히 표시하고 instructions를 제공합니다.
- **Wait**: 진행 전 사용자 confirmation을 기다립니다.

### Code Generation Planning Errors

**Error**: code generation plan이 incomplete함

- **Cause**: missing design artifacts, unclear requirements
- **Solution**: Design stage로 돌아가 artifacts를 완료합니다.
- **Recovery**: available information으로 plan을 생성하고 gaps를 표시합니다.

**Error**: Unit dependencies가 충족되지 않음

- **Cause**: dependent units가 아직 생성되지 않음
- **Solution**: dependencies를 준수하도록 generation sequence를 재정렬합니다.
- **Workaround**: stub dependencies로 생성하고 나중에 integrate합니다.

### Code Generation Errors (Part 2: Code Generation)

**Error**: step에 대한 code를 생성할 수 없음

- **Cause**: insufficient design information, unclear requirements
- **Solution**: step을 건너뛰고 incomplete로 문서화한 뒤 계속합니다.
- **Recovery**: 더 많은 정보를 수집한 뒤 해당 step으로 돌아갑니다.

**Error**: generated code에 syntax errors가 있음

- **Cause**: template issues, language-specific problems
- **Solution**: syntax errors를 수정하고 필요하면 regenerate합니다.
- **Validation**: 진행 전 code가 compile되는지 확인합니다.

**Error**: test generation이 실패함

- **Cause**: complex logic, missing test framework setup
- **Solution**: 기본 test structure를 생성하고 manual completion 대상으로 표시합니다.
- **Workaround**: tests 없이 진행하고 Operations phase에서 추가합니다.

### Operations Errors

**Error**: build tool을 결정할 수 없음

- **Cause**: unusual project structure, multiple build systems
- **Solution**: 사용자에게 build tool과 commands를 지정하도록 요청합니다.
- **Workaround**: generic instructions를 제공하고 사용자가 조정하도록 합니다.

**Error**: deployment target이 unclear함

- **Cause**: multiple environments, complex infrastructure
- **Solution**: 사용자에게 deployment targets와 methods를 지정하도록 요청합니다.
- **Workaround**: common platforms용 instructions를 제공합니다.

## Recovery Procedures

### Partial Stage Completion

**Scenario**: Stage가 mid-execution에서 중단됨

**Recovery Steps**:

1. stage plan file을 로드합니다.
2. 마지막 completed step(마지막 [x] checkbox)을 식별합니다.
3. 다음 uncompleted step부터 resume합니다.
4. 이전 steps가 실제로 complete인지 검증합니다.
5. normal execution을 계속합니다.

### Corrupted State File

**Scenario**: `aidlc-state.md`가 corrupted 또는 inconsistent함

**Recovery Steps**:

1. backup 생성: `aidlc-state.md.backup`
2. 사용자에게 실제로 어떤 stage에 있는지 묻습니다.
3. state file을 scratch에서 regenerate합니다.
4. existing artifacts를 기준으로 completed stages를 표시합니다.
5. current stage부터 resume합니다.

### Missing Artifacts

**Scenario**: prior stage의 required artifacts가 누락됨

**Recovery Steps**:

1. 어떤 artifacts가 missing인지 식별합니다.
2. regenerate 가능한지 판단합니다.
3. 가능하면 해당 stage로 돌아가 artifacts를 regenerate합니다.
4. 불가능하면 사용자에게 information을 manually 제공하도록 요청합니다.
5. gap을 `audit.md`에 문서화합니다.

### User Wants to Restart Stage

**Scenario**: 사용자가 stage results에 만족하지 않아 redo를 원함

**Recovery Steps**:

1. 사용자가 restart를 원하는지 확인합니다(data will be lost).
2. existing artifacts archive: `{artifact}.backup`
3. `aidlc-state.md`에서 stage status를 reset합니다.
4. plan files에서 stage checkboxes를 clear합니다.
5. stage를 처음부터 다시 실행합니다.

### User Wants to Skip Stage

**Scenario**: 사용자가 계획된 stage를 건너뛰려고 함

**Recovery Steps**:

1. 사용자가 implications를 이해했는지 확인합니다.
2. skip reason을 `audit.md`에 문서화합니다.
3. `aidlc-state.md`에서 stage를 "SKIPPED"로 표시합니다.
4. next stage로 진행합니다.
5. 참고: dependencies가 missing이면 later stages에서 issue가 발생할 수 있습니다.

## Escalation Guidelines

### When to Ask for User Help

**Immediately**:

- contradictory 또는 ambiguous user input
- missing required information
- AI가 해결할 수 없는 technical constraints
- business judgment가 필요한 decisions

**After Attempting Resolution**:

- 같은 step에서 반복되는 errors
- complex technical issues
- unusual project structures
- external systems와의 integration

### When to Suggest Starting Over

**Consider Fresh Start If**:

- 여러 stages에 errors가 있음
- state file이 심각하게 corrupted됨
- user requirements가 크게 바뀜
- architectural decision을 되돌려야 함
- 사용자가 missing information을 제공할 수 없음
- artifacts가 phases 전반에서 inconsistent함

**Before Starting Over**:

1. 모든 existing work를 archive합니다.
2. lessons learned를 문서화합니다.
3. 보존할 항목을 식별합니다.
4. user confirmation을 받습니다.
5. 새 execution plan을 만듭니다.

## Session Resumption Errors

### Missing Artifacts During Resumption

**Error**: previous stages의 required artifacts가 누락됨

- **Cause**: files가 삭제, 이동되었거나 생성되지 않음
- **Solution**:
  1. missing artifacts를 만든 stage를 식별합니다.
  2. aidlc-state.md에서 stage가 complete로 표시되었는지 확인합니다.
  3. complete로 표시되었지만 artifacts가 missing이면 해당 stage를 regenerate합니다.
  4. complete로 표시되지 않았다면 해당 stage부터 resume합니다.
- **Recovery**: missing artifacts를 생성하는 stage로 돌아가 다시 실행합니다.

**Error**: artifact file은 존재하지만 empty 또는 corrupted임

- **Cause**: interrupted write, manual editing, file system issues
- **Solution**:
  1. corrupted file의 backup을 만듭니다.
  2. 해당 파일을 만드는 stage에서 regenerate를 시도합니다.
  3. regenerate할 수 없으면 재생성을 위한 information을 사용자에게 요청합니다.
- **Recovery**: artifact를 만드는 stage를 다시 실행합니다.

### Inconsistent State During Resumption

**Error**: aidlc-state.md는 stage complete를 표시하지만 artifacts가 존재하지 않음

- **Cause**: state file은 업데이트되었지만 artifact generation이 실패함
- **Solution**:
  1. aidlc-state.md에서 stage를 incomplete로 표시합니다.
  2. artifacts를 생성하도록 stage를 다시 실행합니다.
  3. complete로 표시하기 전에 artifacts 존재를 검증합니다.
- **Recovery**: stage status를 reset하고 다시 실행합니다.

**Error**: artifacts는 존재하지만 aidlc-state.md는 stage incomplete를 표시함

- **Cause**: artifact generation은 성공했지만 state update가 실패함
- **Solution**:
  1. artifacts가 complete하고 valid한지 검증합니다.
  2. aidlc-state.md를 업데이트해 stage complete를 표시합니다.
  3. next stage로 진행합니다.
- **Recovery**: actual completion을 반영하도록 state file을 업데이트합니다.

**Error**: aidlc-state.md에 여러 stage가 "current"로 표시됨

- **Cause**: state file corruption, manual editing
- **Solution**:
  1. artifacts를 검토해 actual progress를 판단합니다.
  2. 실제로 어느 stage에 있는지 사용자에게 묻습니다.
  3. aidlc-state.md가 single current stage를 표시하도록 수정합니다.
- **Recovery**: existing artifacts를 기준으로 state file을 rebuild합니다.

### Context Loading Errors

**Error**: previous stages에서 required context를 로드할 수 없음

- **Cause**: missing files, corrupted content, wrong file paths
- **Solution**:
  1. current stage에 필요한 artifacts를 나열합니다.
  2. 누락되었거나 corrupted된 항목을 확인합니다.
  3. missing artifacts를 regenerate하거나 사용자에게 information을 요청합니다.
- **Recovery**: current stage를 resume하기 전에 prerequisite stages를 완료합니다.

**Error**: 로드한 artifacts에 contradictory information이 포함됨

- **Cause**: manual editing, multiple people working, incomplete updates
- **Solution**:
  1. contradictions를 식별해 사용자에게 제시합니다.
  2. 어떤 information이 맞는지 사용자에게 묻습니다.
  3. contradictions를 해결하도록 artifacts를 업데이트합니다.
- **Recovery**: 진행 전에 contradictions를 reconcile합니다.

### Resumption Best Practices

1. **Always validate state**: aidlc-state.md가 actual artifacts와 일치하는지 확인합니다.
2. **Load incrementally**: artifacts를 stage-by-stage로 로드하고 각각 검증합니다.
3. **Fail fast**: critical artifacts가 missing이면 즉시 중단합니다.
4. **Communicate clearly**: 무엇이 왜 missing인지 사용자에게 정확히 알립니다.
5. **Offer options**: regenerate, manually provide, fresh start 중 선택지를 제공합니다.
6. **Document recovery**: 모든 recovery actions를 audit.md에 기록합니다.

## Logging Requirements

### Error Logging Format

```markdown
## Error - [Stage Name]
**Timestamp**: [ISO timestamp]
**Error Type**: [Critical/High/Medium/Low]
**Description**: [What went wrong]
**Cause**: [Why it happened]
**Resolution**: [How it was resolved]
**Impact**: [Effect on workflow]

---
```

### Recovery Logging Format

```markdown
## Recovery - [Stage Name]
**Timestamp**: [ISO timestamp]
**Issue**: [What needed recovery]
**Recovery Steps**: [What was done]
**Outcome**: [Result of recovery]
**Artifacts Affected**: [List of files]

---
```

## Prevention Best Practices

1. **Validate Early**: 작업 시작 전 inputs와 dependencies를 확인합니다.
2. **Checkpoint Often**: step을 완료한 직후 checkboxes를 업데이트합니다.
3. **Communicate Clearly**: 무엇을 왜 하는지 설명합니다.
4. **Ask Questions**: 추정하지 말고 ambiguity를 즉시 clarify합니다.
5. **Document Everything**: 모든 decisions와 changes를 `audit.md`에 기록합니다.
