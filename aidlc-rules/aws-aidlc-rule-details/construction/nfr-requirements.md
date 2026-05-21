# NFR Requirements

## Prerequisites

- Functional Design이 unit에 대해 완료되어야 합니다.
- unit functional design artifacts를 사용할 수 있어야 합니다.
- execution plan이 NFR Requirements stage 실행을 지시해야 합니다.

## Overview

unit의 non-functional requirements를 결정하고 tech stack choices를 확정합니다.

## Steps to Execute

### Step 1: Analyze Functional Design

- `aidlc-docs/construction/{unit-name}/functional-design/`에서 functional design artifacts를 읽습니다.
- business logic complexity와 requirements를 이해합니다.

### Step 2: Create NFR Requirements Plan

- NFR assessment를 위한 checkboxes []가 포함된 plan을 생성합니다.
- scalability, performance, availability, security에 집중합니다.
- 각 step에는 checkbox []가 있어야 합니다.

### Step 3: Generate Context-Appropriate Questions

**DIRECTIVE**: functional design을 철저히 분석해 NFR clarification이 system quality와 architecture decisions를 개선할 수 있는 모든 영역을 식별합니다. comprehensive NFR coverage를 보장하기 위해 적극적으로 질문하세요.

**CRITICAL**: system quality에 영향을 줄 수 있는 ambiguity 또는 missing detail이 조금이라도 있으면 기본적으로 질문합니다. incorrect NFR assumptions를 하는 것보다 질문을 너무 많이 하는 편이 낫습니다.

- [Answer]: tag format을 사용해 questions를 EMBED합니다.
- ambiguity, missing information, clarification이 필요한 모든 영역에 집중합니다.
- user input이 NFR과 tech stack decisions를 개선할 수 있는 곳에는 질문을 생성합니다.
- **확신이 없으면 질문하세요** - overconfidence는 poor system quality로 이어집니다.

**평가할 question categories**(모든 category 고려):

- **Scalability Requirements** - expected load, growth patterns, scaling triggers, capacity planning에 대해 질문합니다.
- **Performance Requirements** - response times, throughput, latency, performance benchmarks에 대해 질문합니다.
- **Availability Requirements** - uptime expectations, disaster recovery, failover, business continuity에 대해 질문합니다.
- **Security Requirements** - data protection, compliance, authentication, authorization, threat models에 대해 질문합니다.
- **Tech Stack Selection** - technology preferences, constraints, existing systems, integration requirements에 대해 질문합니다.
- **Reliability Requirements** - error handling, fault tolerance, monitoring, alerting needs에 대해 질문합니다.
- **Maintainability Requirements** - code quality, documentation, testing, operational requirements에 대해 질문합니다.
- **Usability Requirements** - user experience, accessibility, interface requirements에 대해 질문합니다.

### Step 4: Store Plan

- `aidlc-docs/construction/plans/{unit-name}-nfr-requirements-plan.md`로 저장합니다.
- user input을 위한 모든 [Answer]: tags를 포함합니다.

### Step 5: Collect and Analyze Answers

- 사용자가 모든 [Answer]: tags를 완료할 때까지 기다립니다.
- **MANDATORY**: vague 또는 ambiguous answers가 있는지 모든 responses를 신중히 검토합니다.
- **CRITICAL**: unclear responses가 하나라도 있으면 follow-up questions를 추가합니다. ambiguity가 남아 있는 상태로 진행하지 마세요.
- "depends", "maybe", "not sure", "mix of", "somewhere between", "standard", "typical" 같은 responses를 찾습니다.
- ambiguity가 하나라도 감지되면 clarification questions file을 생성합니다.
- **모든 ambiguity가 해결될 때까지 진행하지 마세요.**

### Step 6: Generate NFR Requirements Artifacts

- `aidlc-docs/construction/{unit-name}/nfr-requirements/nfr-requirements.md`를 생성합니다.
- `aidlc-docs/construction/{unit-name}/nfr-requirements/tech-stack-decisions.md`를 생성합니다.

### Step 7: Present Completion Message

- 다음 구조로 completion message를 제시합니다.
     1. **Completion Announcement**(mandatory): 항상 다음으로 시작합니다.

```markdown
# 📊 NFR Requirements Complete - [unit-name]
```

     2. **AI Summary**(optional): NFR requirements에 대한 structured bullet-point summary를 제공합니다.
        - Format: "NFR requirements assessment has identified [description]:"
        - key scalability, performance, availability requirements를 bullet points로 나열합니다.
        - 식별된 security and compliance requirements를 나열합니다.
        - tech stack decisions와 rationale을 언급합니다.
        - workflow instructions("please review", "let me know", "proceed to next phase", "before we proceed")를 포함하지 마세요.
        - factual하고 content-focused하게 유지합니다.
     3. **Formatted Workflow Message**(mandatory): 항상 다음 exact format으로 끝냅니다.

```markdown
> **📋 <u>**REVIEW REQUIRED:**</u>**  
> NFR requirements를 검토해 주세요: `aidlc-docs/construction/[unit-name]/nfr-requirements/`



> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - 검토 결과를 바탕으로 NFR requirements 수정을 요청하세요
> ✅ **Continue to Next Stage** - NFR requirements를 승인하고 **[next-stage-name]**로 진행합니다.

---
```

### Step 8: Wait for Explicit Approval

- 사용자가 NFR requirements를 명시적으로 승인할 때까지 진행하지 마세요.
- approval은 clear and unambiguous해야 합니다.
- 사용자가 changes를 요청하면 requirements를 업데이트하고 approval process를 반복합니다.

### Step 9: Record Approval and Update Progress

- approval을 timestamp와 함께 audit.md에 기록합니다.
- 사용자의 approval response를 timestamp와 함께 기록합니다.
- aidlc-state.md에서 NFR Requirements stage complete를 표시합니다.
