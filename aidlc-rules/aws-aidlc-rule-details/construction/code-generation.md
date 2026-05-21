# Code Generation - Detailed Steps

## Overview

이 stage는 두 개의 통합된 part를 통해 각 unit of work의 code를 생성합니다.

- **Part 1 - Planning**: explicit steps가 포함된 detailed code generation plan 생성
- **Part 2 - Generation**: 승인된 plan을 실행해 code, tests, artifacts 생성

**Note**: brownfield project에서 "generate"는 중복 파일을 만드는 것이 아니라, 적절한 경우 기존 파일을 수정한다는 뜻입니다.

## Prerequisites

- Unit Design Generation이 unit에 대해 완료되어야 합니다.
- NFR Implementation을 실행한 경우 unit에 대해 완료되어야 합니다.
- 모든 unit design artifacts를 사용할 수 있어야 합니다.
- unit이 code generation 준비 상태여야 합니다.

---

# PART 1: PLANNING

## Step 1: Analyze Unit Context

- [ ] Unit Design Generation의 unit design artifacts를 읽습니다.
- [ ] unit story map을 읽어 assigned stories를 이해합니다.
- [ ] unit dependencies와 interfaces를 식별합니다.
- [ ] unit이 code generation 준비 상태인지 검증합니다.

## Step 2: Create Detailed Unit Code Generation Plan

- [ ] `aidlc-docs/aidlc-state.md`에서 workspace root와 project type을 읽습니다.
- [ ] code location을 결정합니다(structure patterns는 Critical Rules 참조).
- [ ] **Brownfield only**: 수정할 existing files를 파악하기 위해 reverse engineering의 code-structure.md를 검토합니다.
- [ ] exact paths를 문서화합니다(절대 aidlc-docs/가 아님).
- [ ] unit generation을 위한 explicit steps를 생성합니다.
  - Project Structure Setup(greenfield only)
  - Business Logic Generation
  - Business Logic Unit Testing
  - Business Logic Summary
  - API Layer Generation
  - API Layer Unit Testing
  - API Layer Summary
  - Repository Layer Generation
  - Repository Layer Unit Testing
  - Repository Layer Summary
  - Frontend Components Generation(applicable한 경우)
  - Frontend Components Unit Testing(applicable한 경우)
  - Frontend Components Summary(applicable한 경우)
  - Database Migration Scripts(data models가 있는 경우)
  - Documentation Generation(API docs, README updates)
  - Deployment Artifacts Generation
- [ ] 각 step에 sequential number를 붙입니다.
- [ ] story mapping references를 포함합니다.
- [ ] 각 step에 checkboxes [ ]를 추가합니다.

## Step 3: Include Unit Generation Context

- [ ] 이 unit에 대해 다음을 포함합니다.
  - 이 unit이 구현하는 stories
  - 다른 units/services에 대한 dependencies
  - expected interfaces and contracts
  - 이 unit이 소유한 database entities
  - service boundaries and responsibilities

## Step 4: Create Unit Plan Document

- [ ] complete plan을 `aidlc-docs/construction/plans/{unit-name}-code-generation-plan.md`로 저장합니다.
- [ ] step numbering(Step 1, Step 2 등)을 포함합니다.
- [ ] unit context와 dependencies를 포함합니다.
- [ ] story traceability를 포함합니다.
- [ ] plan이 step-by-step으로 실행 가능하도록 합니다.
- [ ] 이 plan이 Code Generation의 single source of truth임을 강조합니다.

## Step 5: Summarize Unit Plan

- [ ] unit code generation plan summary를 사용자에게 제공합니다.
- [ ] unit generation approach를 강조합니다.
- [ ] step sequence와 story coverage를 설명합니다.
- [ ] total number of steps와 estimated scope를 언급합니다.

## Step 6: Log Approval Prompt

- [ ] approval을 요청하기 전에 prompt를 timestamp와 함께 `aidlc-docs/audit.md`에 기록합니다.
- [ ] complete unit code generation plan에 대한 reference를 포함합니다.
- [ ] ISO 8601 timestamp format을 사용합니다.

## Step 7: Wait for Explicit Approval

- [ ] 사용자가 unit code generation plan을 명시적으로 승인할 때까지 진행하지 마세요.
- [ ] approval은 entire plan과 generation sequence를 포함해야 합니다.
- [ ] 사용자가 changes를 요청하면 plan을 업데이트하고 approval process를 반복합니다.

## Step 8: Record Approval Response

- [ ] 사용자의 approval response를 timestamp와 함께 `aidlc-docs/audit.md`에 기록합니다.
- [ ] exact user response text를 포함합니다.
- [ ] approval status를 명확하게 표시합니다.

## Step 9: Update Progress

- [ ] `aidlc-state.md`에서 Code Generation Part 1(Planning) complete를 표시합니다.
- [ ] "Current Status" section을 업데이트합니다.
- [ ] Code Generation으로 transition할 준비를 합니다.

---

# PART 2: GENERATION

## Step 10: Load Unit Code Generation Plan

- [ ] `aidlc-docs/construction/plans/{unit-name}-code-generation-plan.md`에서 complete plan을 읽습니다.
- [ ] 다음 미완료 step(첫 번째 [ ] checkbox)을 식별합니다.
- [ ] 해당 step의 context(unit, dependencies, stories)를 로드합니다.

## Step 11: Execute Current Step

- [ ] plan의 target directory를 검증합니다(절대 aidlc-docs/가 아님).
- [ ] **Brownfield only**: target file이 존재하는지 확인합니다.
- [ ] current step이 설명한 내용을 정확히 생성합니다.
  - **If file exists**: in-place로 수정합니다(절대 `ClassName_modified.java`, `ClassName_new.java` 등 생성 금지).
  - **If file doesn't exist**: 새 파일을 생성합니다.
- [ ] 올바른 위치에 작성합니다.
  - **Application Code**: project structure에 따른 workspace root
  - **Documentation**: `aidlc-docs/construction/{unit-name}/code/`(markdown only)
  - **Build/Config Files**: workspace root
- [ ] unit story requirements를 따릅니다.
- [ ] dependencies와 interfaces를 존중합니다.

## Step 12: Update Progress

- [ ] unit code generation plan에서 완료된 step을 [x]로 표시합니다.
- [ ] 관련 unit stories의 generation이 끝나면 [x]로 표시합니다.
- [ ] `aidlc-docs/aidlc-state.md` current status를 업데이트합니다.
- [ ] **Brownfield only**: duplicate files가 생성되지 않았는지 검증합니다(예: `ClassName.java` 옆에 `ClassName_modified.java`가 없어야 함).
- [ ] 생성된 모든 artifacts를 저장합니다.

## Step 13: Continue or Complete Generation

- [ ] 더 남은 steps가 있으면 Step 10으로 돌아갑니다.
- [ ] 모든 steps가 complete이면 completion message 제시로 진행합니다.

## Step 14: Present Completion Message

- 다음 구조로 completion message를 제시합니다.
     1. **Completion Announcement**(mandatory): 항상 다음으로 시작합니다.

```markdown
# 💻 Code Generation Complete - [unit-name]
```

     2. **AI Summary**(optional): structured bullet-point summary를 제공합니다.
        - **Brownfield**: modified files와 created files를 구분합니다(예: "• Modified: `src/services/user-service.ts`", "• Created: `src/services/auth-service.ts`").
        - **Greenfield**: created files를 paths와 함께 나열합니다(예: "• Created: `src/services/user-service.ts`").
        - tests, documentation, deployment artifacts를 paths와 함께 나열합니다.
        - factual하게 유지하고 workflow instructions는 포함하지 않습니다.
     3. **Formatted Workflow Message**(mandatory): 항상 다음 exact format으로 끝냅니다.

```markdown
> **📋 <u>**REVIEW REQUIRED:**</u>**  
> generated code를 검토해 주세요:
> - **Application Code**: `[actual-workspace-path]`
> - **Documentation**: `aidlc-docs/construction/[unit-name]/code/`



> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - 검토 결과를 바탕으로 generated code 수정을 요청하세요
> ✅ **Continue to Next Stage** - code generation을 승인하고 **[next-unit/Build & Test]**로 진행합니다.

---
```

## Step 15: Wait for Explicit Approval

- 사용자가 generated code를 명시적으로 승인할 때까지 진행하지 마세요.
- approval은 clear and unambiguous해야 합니다.
- 사용자가 changes를 요청하면 code를 업데이트하고 approval process를 반복합니다.

## Step 16: Record Approval and Update Progress

- approval을 timestamp와 함께 audit.md에 기록합니다.
- 사용자의 approval response를 timestamp와 함께 기록합니다.
- aidlc-state.md에서 이 unit의 Code Generation stage complete를 표시합니다.

---

## Critical Rules

### Code Location Rules

- **Application code**: workspace root only(NEVER aidlc-docs/)
- **Documentation**: aidlc-docs/ only(markdown summaries)
- code를 생성하기 전에 aidlc-state.md에서 **workspace root를 읽습니다**.

**project type별 structure patterns**:

- **Brownfield**: existing structure를 사용합니다(예: `src/main/java/`, `lib/`, `pkg/`).
- **Greenfield single unit**: workspace root에 `src/`, `tests/`, `config/`
- **Greenfield multi-unit (microservices)**: `{unit-name}/src/`, `{unit-name}/tests/`
- **Greenfield multi-unit (monolith)**: `src/{unit-name}/`, `tests/{unit-name}/`

### Brownfield File Modification Rules

- 생성 전에 file이 존재하는지 확인합니다.
- 존재하면 in-place로 수정합니다(절대 `ClassName_modified.java` 같은 copies 생성 금지).
- 존재하지 않으면 새 파일을 생성합니다.
- generation 후 duplicate files가 없는지 검증합니다(Step 12).

### Planning Phase Rules

- 모든 generation activities에 대해 explicit, numbered steps를 생성합니다.
- plan에 story traceability를 포함합니다.
- unit context와 dependencies를 문서화합니다.
- generation 전에 explicit user approval을 받습니다.

### Generation Phase Rules

- **NO HARDCODED LOGIC**: unit plan에 작성된 내용만 실행합니다.
- **FOLLOW PLAN EXACTLY**: step sequence에서 벗어나지 마세요.
- **UPDATE CHECKBOXES**: 각 step을 완료한 즉시 [x]로 표시합니다.
- **STORY TRACEABILITY**: functionality가 구현되면 unit stories를 [x]로 표시합니다.
- **RESPECT DEPENDENCIES**: unit dependencies가 충족된 경우에만 구현합니다.

### Automation Friendly Code Rules

UI code(web, mobile, desktop)를 생성할 때 elements가 automation-friendly하도록 보장합니다.

- interactive elements(buttons, inputs, links, forms)에 `data-testid` attributes를 추가합니다.
- consistent naming을 사용합니다: `{component}-{element-role}`(예: `login-form-submit-button`, `user-list-search-input`).
- render마다 바뀌는 dynamic 또는 auto-generated IDs를 피합니다.
- code changes 사이에서도 `data-testid` values를 stable하게 유지합니다(element purpose가 바뀔 때만 변경).

## Completion Criteria

- complete unit code generation plan이 생성되고 승인됨
- unit code generation plan의 모든 steps가 [x]로 표시됨
- 모든 unit stories가 plan에 따라 구현됨
- 모든 code와 tests가 생성됨(tests는 Build & Test phase에서 실행)
- deployment artifacts가 생성됨
- complete unit이 build and verification 준비 상태임
