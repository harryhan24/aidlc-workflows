# Functional Design

## Purpose

**unit별 detailed business logic design**

Functional Design은 다음에 집중합니다.

- unit의 detailed business logic과 algorithms
- entities와 relationships가 포함된 domain models
- detailed business rules, validation logic, constraints
- technology-agnostic design(infrastructure concerns 제외)

**Note**: 이 단계는 Application Design(INCEPTION phase)의 high-level component design을 기반으로 합니다.

## Prerequisites

- Units Generation이 완료되어야 합니다.
- Unit of work artifacts를 사용할 수 있어야 합니다.
- Application Design을 권장합니다(high-level component structure 제공).
- execution plan이 Functional Design stage 실행을 지시해야 합니다.

## Overview

technology-agnostic 방식으로 unit의 detailed business logic을 설계하고, 순수 business functions에만 집중합니다.

## Steps to Execute

### Step 1: Analyze Unit Context

- `aidlc-docs/inception/application-design/unit-of-work.md`에서 unit definition을 읽습니다.
- `aidlc-docs/inception/application-design/unit-of-work-story-map.md`에서 assigned stories를 읽습니다.
- unit responsibilities와 boundaries를 이해합니다.

### Step 2: Create Functional Design Plan

- functional design을 위한 checkboxes []가 포함된 plan을 생성합니다.
- business logic, domain models, business rules에 집중합니다.
- 각 step에는 checkbox []가 있어야 합니다.

### Step 3: Generate Context-Appropriate Questions

**DIRECTIVE**: unit definition과 functional design artifacts를 철저히 분석해 clarification이 functional design을 개선할 수 있는 모든 영역을 식별합니다. comprehensive understanding을 보장하기 위해 적극적으로 질문하세요.

**CRITICAL**: functional design quality에 영향을 줄 수 있는 ambiguity 또는 missing detail이 조금이라도 있으면 기본적으로 질문합니다. incorrect assumptions를 하는 것보다 질문을 너무 많이 하는 편이 낫습니다.

- [Answer]: tag format을 사용해 questions를 EMBED합니다.
- ambiguity, missing information, clarification이 필요한 모든 영역에 집중합니다.
- user input이 functional design decisions를 개선할 수 있는 곳에는 질문을 생성합니다.
- **확신이 없으면 질문하세요** - overconfidence는 poor designs로 이어집니다.

**고려할 question categories**(모든 category 평가):

- **Business Logic Modeling** - core entities, workflows, data transformations, business processes에 대해 질문합니다.
- **Domain Model** - domain concepts, entity relationships, data structures, business objects에 대해 질문합니다.
- **Business Rules** - decision rules, validation logic, constraints, business policies에 대해 질문합니다.
- **Data Flow** - data inputs, outputs, transformations, persistence requirements에 대해 질문합니다.
- **Integration Points** - external system interactions, APIs, data exchange에 대해 질문합니다.
- **Error Handling** - error scenarios, validation failures, exception handling에 대해 질문합니다.
- **Business Scenarios** - edge cases, alternative flows, complex business situations에 대해 질문합니다.
- **Frontend Components**(applicable한 경우) - UI component structure, user interactions, state management, form handling에 대해 질문합니다.

### Step 4: Store Plan

- `aidlc-docs/construction/plans/{unit-name}-functional-design-plan.md`로 저장합니다.
- user input을 위한 모든 [Answer]: tags를 포함합니다.

### Step 5: Collect and Analyze Answers

- 사용자가 모든 [Answer]: tags를 완료할 때까지 기다립니다.
- **MANDATORY**: vague 또는 ambiguous answers가 있는지 모든 responses를 신중히 검토합니다.
- **CRITICAL**: unclear responses가 하나라도 있으면 follow-up questions를 추가합니다. ambiguity가 남아 있는 상태로 진행하지 마세요.
- "depends", "maybe", "not sure", "mix of", "somewhere between" 같은 responses를 찾습니다.
- ambiguity가 하나라도 감지되면 clarification questions file을 생성합니다.
- **모든 ambiguity가 해결될 때까지 진행하지 마세요.**

### Step 6: Generate Functional Design Artifacts

- `aidlc-docs/construction/{unit-name}/functional-design/business-logic-model.md`를 생성합니다.
- `aidlc-docs/construction/{unit-name}/functional-design/business-rules.md`를 생성합니다.
- `aidlc-docs/construction/{unit-name}/functional-design/domain-entities.md`를 생성합니다.
- unit에 frontend/UI가 포함된 경우 `aidlc-docs/construction/{unit-name}/functional-design/frontend-components.md`를 생성합니다.
  - Component hierarchy and structure
  - 각 component의 props and state definitions
  - User interaction flows
  - Form validation rules
  - API integration points(각 component가 사용하는 backend endpoints)

### Step 7: Present Completion Message

- 다음 구조로 completion message를 제시합니다.
     1. **Completion Announcement**(mandatory): 항상 다음으로 시작합니다.

```markdown
# 🔧 Functional Design Complete - [unit-name]
```

     2. **AI Summary**(optional): functional design에 대한 structured bullet-point summary를 제공합니다.
        - Format: "Functional design has created [description]:"
        - key business logic models와 entities를 bullet points로 나열합니다.
        - 정의된 business rules와 validation logic을 나열합니다.
        - domain model structure와 relationships를 언급합니다.
        - workflow instructions("please review", "let me know", "proceed to next phase", "before we proceed")를 포함하지 마세요.
        - factual하고 content-focused하게 유지합니다.
     3. **Formatted Workflow Message**(mandatory): 항상 다음 exact format으로 끝냅니다.

```markdown
> **📋 <u>**REVIEW REQUIRED:**</u>**  
> functional design artifacts를 검토해 주세요: `aidlc-docs/construction/[unit-name]/functional-design/`



> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - 검토 결과를 바탕으로 functional design 수정을 요청하세요
> ✅ **Continue to Next Stage** - functional design을 승인하고 **[next-stage-name]**로 진행합니다.

---
```

### Step 8: Wait for Explicit Approval

- 사용자가 functional design을 명시적으로 승인할 때까지 진행하지 마세요.
- approval은 clear and unambiguous해야 합니다.
- 사용자가 changes를 요청하면 design을 업데이트하고 approval process를 반복합니다.

### Step 9: Record Approval and Update Progress

- approval을 timestamp와 함께 audit.md에 기록합니다.
- 사용자의 approval response를 timestamp와 함께 기록합니다.
- aidlc-state.md에서 Functional Design stage complete를 표시합니다.
