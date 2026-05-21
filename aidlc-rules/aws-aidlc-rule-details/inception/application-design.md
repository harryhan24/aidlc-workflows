# Application Design - Detailed Steps

## Purpose

**High-level component identification 및 service layer design**

Application Design은 다음에 집중합니다.

- main functional components와 responsibilities 식별
- component interfaces 정의(상세 business logic은 제외)
- orchestration을 위한 service layer 설계
- component dependencies 및 communication patterns 수립

**Note**: 상세 business logic design은 나중에 Functional Design(per-unit, CONSTRUCTION phase)에서 수행합니다.

## Prerequisites

- Workspace Detection이 완료되어야 합니다.
- Requirements Analysis를 권장합니다(functional context 제공).
- User Stories를 권장합니다(user stories가 design decisions를 안내).
- execution plan이 Application Design stage 실행을 지시해야 합니다.

## Step-by-Step Execution

### 1. Analyze Context

- `aidlc-docs/inception/requirements/requirements.md`와 `aidlc-docs/inception/user-stories/stories.md`를 읽습니다.
- key business capabilities와 functional areas를 식별합니다.
- design scope와 complexity를 결정합니다.

### 2. Create Application Design Plan

- application design을 위한 checkboxes []가 포함된 plan을 생성합니다.
- components, responsibilities, methods, business rules, services에 집중합니다.
- 각 step과 sub-step에는 checkbox []가 있어야 합니다.

### 3. Include Mandatory Design Artifacts in Plan

- design plan에는 다음 mandatory artifacts를 **항상** 포함합니다.
  - [ ] component definitions와 high-level responsibilities가 포함된 components.md 생성
  - [ ] method signatures가 포함된 component-methods.md 생성(business rules는 나중에 Functional Design에서 상세화)
  - [ ] service definitions와 orchestration patterns가 포함된 services.md 생성
  - [ ] dependency relationships와 communication patterns가 포함된 component-dependency.md 생성
  - [ ] design completeness 및 consistency 검증

### 4. Generate Context-Appropriate Questions

**DIRECTIVE**: requirements와 stories를 분석해 이 specific application design과 관련 있는 질문을 생성합니다. 아래 categories는 guidance로 사용합니다. 각 category를 평가하고, applicability에 확신이 없으면 건너뛰지 말고 질문하세요. overconfidence는 poor outcomes로 이어집니다(overconfidence-prevention.md 참조).

- [Answer]: tag format을 사용해 questions를 EMBED합니다.
- ambiguity, missing information, clarification이 필요한 모든 영역에 집중합니다.
- user input이 design decisions를 개선할 수 있는 곳에는 질문을 생성합니다.
- **확신이 없으면 질문하세요** - overconfidence는 poor designs로 이어집니다.

**평가할 question categories**(모든 category 고려):

- **Component Identification** - component boundaries, organization, grouping strategies에 대해 질문합니다.
- **Component Methods** - method signatures, input/output expectations, interface contracts에 대해 질문합니다(상세 business rules는 나중에 작성).
- **Service Layer Design** - service orchestration, boundaries, coordination patterns에 대해 질문합니다.
- **Component Dependencies** - communication patterns, dependency management, coupling concerns에 대해 질문합니다.
- **Design Patterns** - architectural style preferences, pattern choices, design constraints에 대해 질문합니다.

### 5. Store Application Design Plan

- `aidlc-docs/inception/plans/application-design-plan.md`로 저장합니다.
- user input을 위한 모든 [Answer]: tags를 포함합니다.
- plan이 모든 design aspects를 포괄하도록 합니다.

### 6. Request User Input

- 사용자에게 plan document 안의 [Answer]: tags를 직접 채우도록 요청합니다.
- design decisions의 중요성을 강조합니다.
- [Answer]: tags를 완료하는 방법에 대한 clear instructions를 제공합니다.

### 7. Collect Answers

- 사용자가 document 안의 [Answer]: tags로 모든 questions에 답할 때까지 기다립니다.
- 모든 [Answer]: tags가 완료될 때까지 진행하지 마세요.
- document를 검토해 빈 [Answer]: tags가 없는지 확인합니다.

### 8. ANALYZE ANSWERS (MANDATORY)

진행 전에 모든 user answers를 신중히 검토해 다음을 확인해야 합니다.

- **Vague or ambiguous responses**: "mix of", "somewhere between", "not sure", "depends"
- **Undefined criteria or terms**: clear definitions 없이 concepts를 참조
- **Contradictory answers**: 서로 충돌하는 responses
- **Missing design details**: specific guidance가 부족한 answers
- **Answers that combine options**: clear decision rules 없이 여러 approaches를 병합한 responses

### 9. MANDATORY Follow-up Questions

step 8의 analysis에서 ambiguous answers가 하나라도 발견되면 반드시 다음을 수행합니다.

- [Answer]: tags를 사용해 specific follow-up questions를 plan document에 추가합니다.
- 모든 ambiguity가 해결될 때까지 approval로 진행하지 마세요.
- required follow-up examples:
  - "You mentioned 'mix of A and B' - what specific criteria should determine when to use A vs B?"
  - "You said 'somewhere between A and B' - can you define the exact middle ground approach?"
  - "You indicated 'not sure' - what additional information would help you decide?"
  - "You mentioned 'depends on complexity' - how do you define complexity levels?"

### 10. Generate Application Design Artifacts

- 승인된 plan을 실행해 design artifacts를 생성합니다.
- `aidlc-docs/inception/application-design/components.md`를 생성하며 다음을 포함합니다.
  - Component name and purpose
  - Component responsibilities
  - Component interfaces
- `aidlc-docs/inception/application-design/component-methods.md`를 생성하며 다음을 포함합니다.
  - 각 component의 Method signatures
  - 각 method의 high-level purpose
  - Input/output types
  - Note: 상세 business rules는 Functional Design(per-unit, CONSTRUCTION phase)에서 정의합니다.
- `aidlc-docs/inception/application-design/services.md`를 생성하며 다음을 포함합니다.
  - Service definitions
  - Service responsibilities
  - Service interactions and orchestration
- `aidlc-docs/inception/application-design/component-dependency.md`를 생성하며 다음을 포함합니다.
  - relationships를 보여주는 dependency matrix
  - components 사이의 communication patterns
  - Data flow diagrams
- 위에서 만든 여러 design docs를 하나의 문서로 통합한 `aidlc-docs/inception/application-design/application-design.md`를 생성합니다.

### 11. Log Approval

- approval prompt를 timestamp와 함께 `aidlc-docs/audit.md`에 기록합니다.
- complete approval prompt text를 포함합니다.
- ISO 8601 timestamp format을 사용합니다.

### 12. Present Completion Message

```markdown
# 🏗️ Application Design Complete

[생성된 application design artifacts에 대한 AI-generated summary를 bullet points로 작성]

> **📋 <u>**REVIEW REQUIRED:**</u>**
> application design artifacts를 검토해 주세요: `aidlc-docs/inception/application-design/`

> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - 필요하다면 application design 수정을 요청하세요
> [IF Units Generation is skipped:]
> 📝 **Add Units Generation** - **Units Generation** stage를 포함하도록 선택합니다(현재 skipped)
> ✅ **Approve & Continue** - design을 승인하고 **[Units Generation/CONSTRUCTION PHASE]**로 진행합니다
```

### 13. Wait for Explicit Approval

- 사용자가 application design을 명시적으로 승인할 때까지 진행하지 마세요.
- approval은 clear and unambiguous해야 합니다.
- 사용자가 changes를 요청하면 design을 업데이트하고 approval process를 반복합니다.

### 14. Record Approval Response

- 사용자 approval response를 timestamp와 함께 `aidlc-docs/audit.md`에 기록합니다.
- exact user response text를 포함합니다.
- approval status를 명확하게 표시합니다.

### 15. Update Progress

- `aidlc-docs/aidlc-state.md`에서 Application Design stage complete를 표시합니다.
- "Current Status" section을 업데이트합니다.
- next stage로 transition할 준비를 합니다.
