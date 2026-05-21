# Requirements Analysis (Adaptive)

**Assume the role** of a product owner

**Adaptive Phase**: 항상 실행합니다. detail level은 problem complexity에 맞게 조정됩니다.

**adaptive depth 설명은 [depth-levels.md](../common/depth-levels.md)를 참조하세요.**

## Prerequisites

- Workspace Detection이 완료되어야 합니다.
- Reverse Engineering이 완료되어야 합니다(brownfield인 경우).

## Execution Steps

### Step 1: Load Reverse Engineering Context (if available)

**IF brownfield project**:

- `aidlc-docs/inception/reverse-engineering/architecture.md`를 로드합니다.
- `aidlc-docs/inception/reverse-engineering/component-inventory.md`를 로드합니다.
- `aidlc-docs/inception/reverse-engineering/technology-stack.md`를 로드합니다.
- request를 분석할 때 이 파일들을 사용해 existing system을 이해합니다.

### Step 2: Analyze User Request (Intent Analysis)

#### 2.1 Request Clarity

- **Clear**: 구체적이고 잘 정의되어 있으며 actionable함
- **Vague**: 일반적이고 ambiguous하며 clarification이 필요함
- **Incomplete**: key information이 누락됨

#### 2.2 Request Type

- **New Feature**: 새로운 functionality 추가
- **Bug Fix**: 기존 issue 수정
- **Refactoring**: code structure 개선
- **Upgrade**: dependencies 또는 frameworks 업데이트
- **Migration**: 다른 technology로 이동
- **Enhancement**: 기존 feature 개선
- **New Project**: scratch에서 시작

#### 2.3 Initial Scope Estimate

- **Single File**: 한 file 변경
- **Single Component**: 한 component/package 변경
- **Multiple Components**: 여러 components에 걸친 변경
- **System-wide**: 전체 system에 영향을 주는 변경
- **Cross-system**: 여러 systems에 영향을 주는 변경

#### 2.4 Initial Complexity Estimate

- **Trivial**: 단순하고 straightforward한 변경
- **Simple**: implementation path가 명확함
- **Moderate**: 어느 정도 complexity와 여러 considerations가 있음
- **Complex**: 상당한 complexity와 많은 considerations가 있음

### Step 3: Determine Requirements Depth

**request analysis를 바탕으로 depth를 결정합니다.**

**Minimal Depth** - 다음 경우 사용합니다.

- request가 clear하고 simple함
- detailed requirements가 필요 없음
- basic understanding만 문서화하면 됨

**Standard Depth** - 다음 경우 사용합니다.

- request에 clarification이 필요함
- functional and non-functional requirements가 필요함
- normal complexity

**Comprehensive Depth** - 다음 경우 사용합니다.

- 여러 stakeholders가 있는 complex project
- high risk 또는 critical system
- traceability가 포함된 detailed requirements가 필요함

### Step 4: Assess Current Requirements

사용자가 제공한 모든 내용을 분석합니다.

- intent statements 또는 descriptions(이미 audit.md에 기록됨)
- 기존 requirements documents(언급된 경우 workspace 검색)
- pasted content 또는 file references
- non-markdown documents를 markdown format으로 변환

### Step 5: Thorough Completeness Analysis

**CRITICAL**: comprehensive analysis를 사용해 requirements completeness를 평가합니다. ambiguity 또는 missing detail이 조금이라도 있으면 기본적으로 질문합니다.

**MANDATORY**: 다음 영역을 모두 평가하고, 명확하지 않은 항목이 있으면 질문해야 합니다.

- **Functional Requirements**: core features, user interactions, system behaviors
- **Non-Functional Requirements**: performance, security, scalability, usability
- **User Scenarios**: use cases, user journeys, edge cases, error scenarios
- **Business Context**: goals, constraints, success criteria, stakeholder needs
- **Technical Context**: integration points, data requirements, system boundaries
- **Quality Attributes**: reliability, maintainability, testability, accessibility

**확신이 없으면 질문하세요** - incomplete requirements는 poor implementations로 이어집니다.

### Step 5.1: Extension Opt-In Prompts

**MANDATORY**: workflow 시작 시 `extensions/` 하위 디렉터리에서 로드된 모든 `*.opt-in.md` 파일을 스캔해 `## Opt-In Prompt` 섹션을 찾습니다. extension이 해당 섹션을 선언하면 Step 6에서 생성하는 clarifying questions file에 그 질문을 포함합니다. 각 opt-in question은 사용자 대화와 같은 언어로 제시합니다.

답변을 받은 뒤:

1. 각 extension의 enablement status를 `aidlc-docs/aidlc-state.md`의 `## Extension Configuration` 아래에 기록합니다.

```markdown
## Extension Configuration
| Extension | Enabled | Decided At |
|---|---|---|
| [Extension Name] | [Yes/No] | Requirements Analysis |
```

1. **Deferred Rule Loading**: 사용자가 opt IN한 각 extension에 대해 지금 full rules file을 로드합니다. rules file은 명명 규칙으로 도출합니다. opt-in filename에서 `.opt-in.md`를 제거하고 `.md`를 붙입니다(예: `security-baseline.opt-in.md` → `security-baseline.md`). 사용자가 opt OUT한 extension의 full rules file은 로드하지 마세요.

### Step 6: Generate Clarifying Questions (PROACTIVE APPROACH)

- requirements가 exceptionally clear and complete한 경우가 아니면 **항상** `aidlc-docs/inception/requirements/requirement-verification-questions.md`를 생성합니다.
- missing, unclear, ambiguous한 모든 영역에 대해 질문합니다.
- functional requirements, non-functional requirements, user scenarios, business context에 집중합니다.
- 사용자에게 questions document 안의 모든 [Answer]: tags를 직접 채우도록 요청합니다.
- answers에 multiple-choice options를 제시하는 경우:
  - options를 A, B, C, D 등으로 label합니다.
  - options가 mutually exclusive하고 overlap되지 않게 합니다.
  - custom response option을 항상 포함합니다: "X) Other (please describe after [Answer]: tag below)"
- document 안의 user answers를 기다립니다.
- **MANDATORY**: 모든 answers를 분석해 ambiguity가 있는지 확인하고, 필요하면 follow-up questions를 만듭니다.
- **MANDATORY**: 모든 ambiguity가 해결되거나 사용자가 명시적으로 진행을 요청할 때까지 계속 질문합니다.

### ⛔ GATE: Await User Answers

requirement-verification-questions.md의 모든 questions가 답변되고 검증될 때까지 Step 7로 진행하지 마세요.
question file을 사용자에게 제시하고 STOP합니다.

### Step 7: Generate Requirements Document

- **PREREQUISITE**: Step 6 gate를 통과해야 합니다. 모든 answers를 받고 분석해야 합니다.
- `aidlc-docs/inception/requirements/requirements.md`를 생성합니다.
- 상단에 intent analysis summary를 포함합니다.
  - User request
  - Request type
  - Scope estimate
  - Complexity estimate
- functional and non-functional requirements를 모두 포함합니다.
- clarifying questions에 대한 사용자 답변을 반영합니다.
- key requirements의 brief summary를 제공합니다.

### Step 8: Update State Tracking

`aidlc-docs/aidlc-state.md`를 업데이트합니다.

```markdown
## Stage Progress
### 🔵 INCEPTION PHASE
- [x] Workspace Detection
- [x] Reverse Engineering (if applicable)
- [x] Requirements Analysis
```

### Step 9: Log and Proceed

- approval prompt를 timestamp와 함께 `aidlc-docs/audit.md`에 기록합니다.
- 다음 구조로 completion message를 제시합니다.
  1. **Completion Announcement**(mandatory): 항상 다음으로 시작합니다.

```markdown
# 🔍 Requirements Analysis Complete
```

1. **AI Summary**(optional): requirements에 대한 structured bullet-point summary를 제공합니다.
   - Format: "Requirements analysis has identified [project type/complexity]:"
   - key functional requirements를 bullet points로 나열합니다.
   - key non-functional requirements를 bullet points로 나열합니다.
   - 관련이 있으면 architectural considerations 또는 technical decisions를 언급합니다.
   - workflow instructions("please review", "let me know", "proceed to next phase", "before we proceed")를 포함하지 마세요.
   - factual하고 content-focused하게 유지합니다.
1. **Formatted Workflow Message**(mandatory): 항상 아래 exact format으로 끝냅니다.

```markdown
> **📋 <u>**REVIEW REQUIRED:**</u>**
> requirements document를 검토해 주세요: `aidlc-docs/inception/requirements/requirements.md`



> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** -  검토 결과 필요하다면 requirements 수정을 요청하세요
> [IF User Stories will be skipped, add this option:]
> 📝 **Add User Stories** - **User Stories** stage를 포함하도록 선택합니다(project simplicity 기준으로 현재 skipped 상태)
> ✅ **Approve & Continue** - requirements를 승인하고 **[User Stories/Workflow Planning]**으로 진행합니다

---
```

**Note**: "Add User Stories" option은 User Stories stage가 skipped될 예정인 경우에만 포함합니다. [User Stories/Workflow Planning]은 실제 next stage name으로 바꾸세요.

- 진행 전 explicit user approval을 기다립니다.
- approval response를 timestamp와 함께 기록합니다.
- aidlc-state.md에서 Requirements Analysis stage complete를 업데이트합니다.
