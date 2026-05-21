# Infrastructure Design

## Prerequisites

- Functional Design이 unit에 대해 완료되어야 합니다.
- NFR Design을 권장합니다(mapping할 logical components 제공).
- execution plan이 Infrastructure Design stage 실행을 지시해야 합니다.

## Overview

deployment environments를 위해 logical software components를 actual infrastructure choices에 매핑합니다.

## Steps to Execute

### Step 1: Analyze Design Artifacts

- `aidlc-docs/construction/{unit-name}/functional-design/`에서 functional design을 읽습니다.
- 존재하는 경우 `aidlc-docs/construction/{unit-name}/nfr-design/`에서 NFR design을 읽습니다.
- infrastructure가 필요한 logical components를 식별합니다.

### Step 2: Create Infrastructure Design Plan

- infrastructure design을 위한 checkboxes []가 포함된 plan을 생성합니다.
- actual services(AWS, Azure, GCP, on-premise)로의 mapping에 집중합니다.
- 각 step에는 checkbox []가 있어야 합니다.

### Step 3: Generate Context-Appropriate Questions

**DIRECTIVE**: functional design과 NFR design을 철저히 분석해 clarification이 infrastructure decisions를 개선할 수 있는 모든 영역을 식별합니다. comprehensive infrastructure coverage를 보장하기 위해 적극적으로 질문하세요.

**CRITICAL**: infrastructure quality에 영향을 줄 수 있는 ambiguity 또는 missing detail이 조금이라도 있으면 기본적으로 질문합니다. incorrect infrastructure assumptions를 하는 것보다 질문을 너무 많이 하는 편이 낫습니다.

**MANDATORY**: 다음 category를 모두 평가하고 각 category에 대해 targeted questions를 묻습니다. 각 category에 대해서는 functional and NFR design artifacts의 evidence를 기준으로 applicability를 판단합니다. explicit justification 없이 categories를 건너뛰지 마세요.

- [Answer]: tag format을 사용해 questions를 EMBED합니다.
- ambiguity, missing information, clarification이 필요한 모든 영역에 집중합니다.
- user input이 infrastructure decisions를 개선할 수 있는 곳에는 질문을 생성합니다.
- **확신이 없으면 질문하세요** - overconfidence는 poor infrastructure choices로 이어집니다.

**평가할 question categories**(모든 category 고려):

- **Deployment Environment** - cloud provider preferences, environment setup, deployment targets에 대해 질문합니다.
- **Compute Infrastructure** - compute service choices, sizing, scaling requirements에 대해 질문합니다.
- **Storage Infrastructure** - database selection, storage patterns, data lifecycle needs에 대해 질문합니다.
- **Messaging Infrastructure** - messaging/queuing services, event-driven patterns, async processing에 대해 질문합니다.
- **Networking Infrastructure** - load balancing, API gateway approach, network topology에 대해 질문합니다.
- **Monitoring Infrastructure** - observability tooling, alerting strategy, logging requirements에 대해 질문합니다.
- **Shared Infrastructure** - infrastructure sharing strategy, multi-tenancy, resource isolation에 대해 질문합니다.

### Step 4: Store Plan

- `aidlc-docs/construction/plans/{unit-name}-infrastructure-design-plan.md`로 저장합니다.
- user input을 위한 모든 [Answer]: tags를 포함합니다.

### Step 5: Collect and Analyze Answers

- 사용자가 모든 [Answer]: tags를 완료할 때까지 기다립니다.
- vague 또는 ambiguous responses가 있는지 검토합니다.
- 필요한 경우 follow-up questions를 추가합니다.

### Step 6: Generate Infrastructure Design Artifacts

- `aidlc-docs/construction/{unit-name}/infrastructure-design/infrastructure-design.md`를 생성합니다.
- `aidlc-docs/construction/{unit-name}/infrastructure-design/deployment-architecture.md`를 생성합니다.
- shared infrastructure가 있는 경우 `aidlc-docs/construction/shared-infrastructure.md`를 생성합니다.

### Step 7: Present Completion Message

- 다음 구조로 completion message를 제시합니다.
     1. **Completion Announcement**(mandatory): 항상 다음으로 시작합니다.

```markdown
# 🏢 Infrastructure Design Complete - [unit-name]
```

     2. **AI Summary**(optional): infrastructure design에 대한 structured bullet-point summary를 제공합니다.
        - Format: "Infrastructure design has mapped [description]:"
        - key infrastructure services와 components를 bullet points로 나열합니다.
        - deployment architecture decisions와 rationale을 나열합니다.
        - cloud provider choices와 service mappings를 언급합니다.
        - workflow instructions("please review", "let me know", "proceed to next phase", "before we proceed")를 포함하지 마세요.
        - factual하고 content-focused하게 유지합니다.
     3. **Formatted Workflow Message**(mandatory): 항상 다음 exact format으로 끝냅니다.

```markdown
> **📋 <u>**REVIEW REQUIRED:**</u>**  
> infrastructure design을 검토해 주세요: `aidlc-docs/construction/[unit-name]/infrastructure-design/`



> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - 검토 결과를 바탕으로 infrastructure design 수정을 요청하세요
> ✅ **Continue to Next Stage** - infrastructure design을 승인하고 **Code Generation**으로 진행합니다.

---
```

### Step 8: Wait for Explicit Approval

- 사용자가 infrastructure design을 명시적으로 승인할 때까지 진행하지 마세요.
- approval은 clear and unambiguous해야 합니다.
- 사용자가 changes를 요청하면 design을 업데이트하고 approval process를 반복합니다.

### Step 9: Record Approval and Update Progress

- approval을 timestamp와 함께 audit.md에 기록합니다.
- 사용자의 approval response를 timestamp와 함께 기록합니다.
- aidlc-state.md에서 Infrastructure Design stage complete를 표시합니다.
