# Units Generation - Detailed Steps

## Overview

이 stage는 두 개의 통합된 part를 통해 system을 관리 가능한 units of work로 분해합니다.

- **Part 1 - Planning**: questions가 포함된 decomposition plan을 만들고, answers를 수집하고, ambiguities를 분석하고, approval을 받습니다.
- **Part 2 - Generation**: 승인된 plan을 실행해 unit artifacts를 생성합니다.

**DEFINITION**: unit of work는 개발 목적의 stories logical grouping입니다. microservices에서는 각 unit이 independently deployable service가 됩니다. monolith에서는 single unit이 logical modules를 가진 전체 application을 나타냅니다.

**Terminology**: independently deployable components에는 "Service", service 내부 logical groupings에는 "Module", planning context에는 "Unit of Work"를 사용합니다.

## Prerequisites

- Workspace Detection이 완료되어야 합니다.
- Requirements Analysis를 권장합니다(functional scope 제공).
- User Stories를 권장합니다(stories가 units에 mapping됨).
- Application Design stage가 필요합니다(components, methods, services 결정).
- execution plan이 Design stage 실행을 지시해야 합니다.

---

# PART 1: PLANNING

## Step 1: Create Unit of Work Plan

- system을 units of work로 분해하기 위한 checkboxes []가 포함된 plan을 생성합니다.
- system을 관리 가능한 development units로 나누는 데 집중합니다.
- 각 step과 sub-step에는 checkbox []가 있어야 합니다.

## Step 2: Include Mandatory Unit Artifacts in Plan

unit plan에는 다음 mandatory artifacts를 **항상** 포함합니다.

- [ ] unit definitions와 responsibilities가 포함된 `aidlc-docs/inception/application-design/unit-of-work.md` 생성
- [ ] dependency matrix가 포함된 `aidlc-docs/inception/application-design/unit-of-work-dependency.md` 생성
- [ ] stories를 units에 mapping하는 `aidlc-docs/inception/application-design/unit-of-work-story-map.md` 생성
- [ ] **Greenfield only**: `unit-of-work.md`에 code organization strategy 문서화(structure patterns는 code-generation.md 참조)
- [ ] unit boundaries와 dependencies 검증
- [ ] 모든 stories가 units에 assigned되었는지 확인

## Step 3: Generate Context-Appropriate Questions

**DIRECTIVE**: requirements, stories, application design을 철저히 분석해 clarification이 unit decomposition quality를 높일 모든 영역을 식별합니다. decomposition concerns의 comprehensive coverage를 보장하기 위해 proactive하게 질문하세요.

**CRITICAL**: unit boundaries 또는 decomposition quality에 영향을 줄 수 있는 ambiguity나 missing detail이 조금이라도 있으면 기본적으로 질문하세요. system을 어떻게 분해해야 하는지에 대해 incorrect assumptions를 만드는 것보다 질문을 너무 많이 하는 편이 낫습니다.

**MANDATORY**: 아래 categories를 모두 평가하고, 각 category에 대해 targeted questions를 묻습니다. 각 category의 applicability는 requirements, stories, application design의 evidence를 바탕으로 판단하세요. explicit justification 없이 category를 건너뛰지 마세요.

- [Answer]: tag format을 사용해 questions를 EMBED합니다.
- ambiguity, missing information, clarification이 필요한 모든 영역에 집중합니다.
- user input이 decomposition decisions를 개선할 수 있는 곳에는 질문을 생성합니다.
- **확신이 없으면 질문하세요** - overconfidence는 poor unit boundaries로 이어집니다.

**평가할 question categories**(모든 category 고려):

- **Story Grouping** - grouping strategy, story affinity, logical clustering approaches에 대해 질문합니다.
- **Dependencies** - integration approach, shared resources, inter-unit communication patterns에 대해 질문합니다.
- **Team Alignment** - team structure, ownership boundaries, collaboration models에 대해 질문합니다.
- **Technical Considerations** - unit별로 달라질 수 있는 scalability/deployment requirements에 대해 질문합니다.
- **Business Domain** - domain boundaries, bounded contexts, business capability alignment에 대해 질문합니다.
- **Code Organization (Greenfield multi-unit only)** - deployment model과 directory structure preferences에 대해 질문합니다.

## Step 4: Store UOW Plan

- `aidlc-docs/inception/plans/unit-of-work-plan.md`로 저장합니다.
- user input을 위한 모든 [Answer]: tags를 포함합니다.
- plan이 system decomposition의 모든 aspects를 포괄하도록 합니다.

## Step 5: Request User Input

- 사용자에게 plan document 안의 [Answer]: tags를 직접 채우도록 요청합니다.
- decomposition decisions의 중요성을 강조합니다.
- [Answer]: tags를 완료하는 방법에 대한 clear instructions를 제공합니다.

## Step 6: Collect Answers

- 사용자가 document 안의 [Answer]: tags로 모든 questions에 답할 때까지 기다립니다.
- 모든 [Answer]: tags가 완료될 때까지 진행하지 마세요.
- document를 검토해 빈 [Answer]: tags가 없는지 확인합니다.

## Step 7: ANALYZE ANSWERS (MANDATORY)

진행 전에 모든 user answers를 신중히 검토해 다음을 확인해야 합니다.

- **Vague or ambiguous responses**: "mix of", "somewhere between", "not sure", "depends"
- **Undefined criteria or terms**: clear definitions 없이 concepts를 참조
- **Contradictory answers**: 서로 충돌하는 responses
- **Missing generation details**: specific guidance가 부족한 answers
- **Answers that combine options**: clear decision rules 없이 여러 approaches를 병합한 responses

## Step 8: MANDATORY Follow-up Questions

step 7의 analysis에서 ambiguous answers가 하나라도 발견되면 반드시 다음을 수행합니다.

- [Answer]: tags를 사용해 specific follow-up questions를 plan document에 추가합니다.
- 모든 ambiguity가 해결될 때까지 approval로 진행하지 마세요.
- required follow-up examples:
  - "You mentioned 'mix of A and B' - what specific criteria should determine when to use A vs B?"
  - "You said 'somewhere between A and B' - can you define the exact middle ground approach?"
  - "You indicated 'not sure' - what additional information would help you decide?"
  - "You mentioned 'depends on complexity' - how do you define complexity levels?"

## Step 9: Request Approval

- 다음을 묻습니다: "**Unit of work plan complete. Review the plan in aidlc-docs/inception/plans/unit-of-work-plan.md. Ready to proceed to generation?**"
- 사용자가 확인할 때까지 진행하지 마세요.

## Step 10: Log Approval

- prompt와 response를 timestamp와 함께 audit.md에 기록합니다.
- ISO 8601 timestamp format을 사용합니다.
- complete approval prompt text를 포함합니다.

## Step 11: Update Progress

- aidlc-state.md에서 Units Generation Part 1(Planning) complete를 표시합니다.
- "Current Status" section을 업데이트합니다.
- Units Generation Part 2(Generation)로 transition할 준비를 합니다.

---

# PART 2: GENERATION

## Step 12: Load Unit of Work Plan

- [ ] `aidlc-docs/inception/plans/unit-of-work-plan.md`에서 complete plan을 읽습니다.
- [ ] 다음 uncompleted step(첫 번째 [ ] checkbox)을 식별합니다.
- [ ] 해당 step의 context와 requirements를 로드합니다.

## Step 13: Execute Current Step

- [ ] current step이 설명하는 내용을 정확히 수행합니다.
- [ ] plan에 지정된 unit artifacts를 생성합니다.
- [ ] Planning에서 승인된 decomposition approach를 따릅니다.
- [ ] plan에 지정된 criteria와 boundaries를 사용합니다.

## Step 14: Update Progress

- [ ] unit of work plan에서 completed step을 [x]로 표시합니다.
- [ ] `aidlc-docs/aidlc-state.md` current status를 업데이트합니다.
- [ ] 생성된 모든 artifacts를 저장합니다.

## Step 15: Continue or Complete

- [ ] 남은 step이 있으면 Step 12로 돌아갑니다.
- [ ] 모든 step이 complete되면 units가 design stages에 준비되었는지 검증합니다.
- [ ] Units Generation stage를 complete로 표시합니다.

## Step 16: Present Completion Message

```markdown
# 🔧 Units Generation Complete

[생성된 units와 decomposition에 대한 AI-generated summary를 bullet points로 작성]

> **📋 <u>**REVIEW REQUIRED:**</u>**
> units generation artifacts를 검토해 주세요: `aidlc-docs/inception/application-design/`

> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - 필요하다면 units generation 수정을 요청하세요
> ✅ **Approve & Continue** - units를 승인하고 **CONSTRUCTION PHASE**로 진행합니다
```

## Step 17: Wait for Explicit Approval

- 사용자가 units generation을 명시적으로 승인할 때까지 진행하지 마세요.
- approval은 clear and unambiguous해야 합니다.
- 사용자가 changes를 요청하면 units를 업데이트하고 approval process를 반복합니다.

## Step 18: Record Approval Response

- 사용자 approval response를 timestamp와 함께 `aidlc-docs/audit.md`에 기록합니다.
- exact user response text를 포함합니다.
- approval status를 명확하게 표시합니다.

## Step 19: Update Progress

- `aidlc-docs/aidlc-state.md`에서 Units Generation stage complete를 표시합니다.
- "Current Status" section을 업데이트합니다.
- CONSTRUCTION PHASE로 transition할 준비를 합니다.

---

## Critical Rules

### Planning Phase Rules

- context-relevant questions만 생성합니다.
- 모든 questions에 [Answer]: tag format을 사용합니다.
- 진행 전 모든 answers를 분석해 ambiguities를 확인합니다.
- 모든 ambiguities를 follow-up questions로 해결합니다.
- generation 전에 explicit user approval을 받습니다.

### Generation Phase Rules

- **NO HARDCODED LOGIC**: unit of work plan에 쓰인 내용만 실행합니다.
- **FOLLOW PLAN EXACTLY**: step sequence에서 벗어나지 마세요.
- **UPDATE CHECKBOXES**: 각 step 완료 직후 [x]로 표시합니다.
- **USE APPROVED APPROACH**: Planning에서 승인된 decomposition methodology를 따릅니다.
- **VERIFY COMPLETION**: 진행 전 모든 unit artifacts가 complete한지 확인합니다.

## Completion Criteria

- 모든 planning questions가 답변되고 ambiguities가 해결됨
- plan에 대한 user approval 확보
- unit of work plan의 모든 steps가 [x]로 표시됨
- 모든 unit artifacts가 plan에 따라 생성됨:
  - unit definitions가 포함된 `unit-of-work.md`
  - dependency matrix가 포함된 `unit-of-work-dependency.md`
  - story mappings가 포함된 `unit-of-work-story-map.md`
- units가 검증되어 per-unit design stages에 준비됨
