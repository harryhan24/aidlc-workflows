# User Stories - Detailed Steps

## Purpose

**requirements를 acceptance criteria가 포함된 user-centered stories로 변환합니다.**

User Stories는 다음에 집중합니다.

- business requirements를 user-centered narratives로 변환
- 각 story에 대한 clear acceptance criteria 정의
- 서로 다른 stakeholder types를 나타내는 user personas 생성
- teams 전반의 shared understanding 수립
- implementation을 위한 testable specifications 제공

## Prerequisites

- Workspace Detection이 완료되어야 합니다.
- Requirements Analysis를 권장합니다(requirements가 있으면 참조 가능).
- Workflow Planning이 User Stories stage 실행을 지시해야 합니다.

## Intelligent Assessment Guidelines

**WHEN TO EXECUTE USER STORIES**: 진행 전에 이 enhanced assessment를 사용합니다.

### High Priority Execution (ALWAYS Execute)

- **New User Features**: 사용자가 직접 interaction할 새로운 functionality
- **User Experience Changes**: 기존 user workflows 또는 interfaces 수정
- **Multi-Persona Systems**: 여러 user type을 대상으로 하는 applications
- **Customer-Facing APIs**: external users 또는 systems가 consume할 services
- **Complex Business Logic**: 여러 scenarios 또는 business rules가 있는 requirements
- **Cross-Team Projects**: 여러 teams 사이의 shared understanding이 필요한 작업

### Medium Priority Execution (Assess Complexity)

- **Backend User Impact**: user experience에 간접 영향을 주는 internal changes
- **Performance Improvements**: user-visible benefits가 있는 enhancements
- **Integration Work**: user workflows에 영향을 주는 systems 연결
- **Data Changes**: user data, reports, analytics에 영향을 주는 modifications
- **Security Enhancements**: user authentication 또는 permissions에 영향을 주는 changes

### Complexity Assessment Factors

medium priority cases에서는 다음 중 하나라도 해당하면 user stories를 실행합니다.

- **Scope**: changes가 multiple components 또는 user touchpoints에 걸쳐 있음
- **Ambiguity**: stories가 명확히 할 수 있는 unclear requirements aspects가 있음
- **Risk**: business impact가 크거나 misunderstanding 가능성이 높음
- **Stakeholders**: multiple business stakeholders가 requirements에 관여함
- **Testing**: user acceptance testing이 필요함
- **Options**: 유효한 implementation approaches가 여러 개 있음

### Skip Only For Simple Cases

- **Pure Refactoring**: user impact가 전혀 없는 internal code improvements
- **Isolated Bug Fixes**: scope가 명확한 simple, well-defined fixes
- **Infrastructure Only**: user-facing effects가 없는 changes
- **Developer Tooling**: build processes, CI/CD, development environment changes
- **Documentation**: functionality에 영향을 주지 않는 updates

### Default Decision Rule

**확신이 없으면 user stories를 포함하고 clarifying questions를 물으세요.** proper clarification이 포함된 comprehensive stories를 만드는 overhead는 보통 다음 이점보다 작습니다.

- requirements understanding 명확화
- team alignment 개선
- testing criteria 개선
- stakeholder communication 강화
- implementation risks 감소
- development 중 costly changes 감소
- user experience outcomes 개선

---

# PART 1: PLANNING

## Step 1: Validate User Stories Need (MANDATORY)

**CRITICAL**: user stories를 진행하기 전에 이 assessment를 수행합니다.

### Assessment Process

1. **Analyze Request Context**:
   - original user request와 requirements를 검토합니다.
   - user-facing vs internal-only changes를 식별합니다.
   - work의 complexity와 scope를 평가합니다.
   - business stakeholder involvement를 평가합니다.

2. **Apply Assessment Criteria**:
   - High Priority indicators와 대조합니다(always execute).
   - Medium Priority factors를 평가합니다(complexity-based decision).
   - 건너뛰어야 하는 simple case가 아닌지 확인합니다.

3. **Document Assessment Decision**:
   - `aidlc-docs/inception/plans/user-stories-assessment.md`를 생성합니다.
   - 이 request에 user stories가 가치 있는 이유를 포함합니다.
   - 적용되는 specific assessment criteria를 참조합니다.
   - expected benefits(clarity, testing, stakeholder alignment)를 설명합니다.

4. **Proceed Only If Justified**:
   - user stories는 project에 clear value를 더해야 합니다.
   - assessment는 concrete benefits가 overhead를 넘는다는 점을 보여야 합니다.
   - decision은 project stakeholders에게 방어 가능해야 합니다.

### Assessment Documentation Template

```markdown
# User Stories Assessment

## Request Analysis
- **Original Request**: [Brief summary]
- **User Impact**: [Direct/Indirect/None]
- **Complexity Level**: [Simple/Medium/Complex]
- **Stakeholders**: [List involved parties]

## Assessment Criteria Met
- [ ] High Priority: [List applicable criteria]
- [ ] Medium Priority: [List applicable criteria with complexity justification]
- [ ] Benefits: [Expected value from user stories]

## Decision
**Execute User Stories**: [Yes/No]
**Reasoning**: [Detailed justification]

## Expected Outcomes
- [user stories가 제공할 specific benefits 목록]
- [stories가 project success를 개선하는 방식]
```

## Step 2: Create Story Plan

- product owner 역할을 맡습니다.
- story development를 위한 step-by-step execution checklist가 포함된 comprehensive plan을 생성합니다.
- 각 step과 sub-step에는 checkbox []가 있어야 합니다.
- requirements를 user stories로 변환하는 methodology와 approach에 집중합니다.

## Step 3: Generate Context-Appropriate Questions

**DIRECTIVE**: requirements와 context를 철저히 분석해 clarification이 story quality와 team understanding을 개선할 모든 영역을 식별합니다. comprehensive user story development를 보장하기 위해 proactive하게 질문하세요.

**CRITICAL**: story quality에 영향을 줄 수 있는 ambiguity나 missing detail이 조금이라도 있으면 기본적으로 질문하세요. incomplete 또는 unclear stories를 만드는 것보다 질문을 너무 많이 하는 편이 낫습니다.

**question formatting rules는 `common/question-format-guide.md`를 참조하세요.**

- [Answer]: tag format을 사용해 questions를 EMBED합니다.
- ambiguity, missing information, clarification이 필요한 모든 영역에 집중합니다.
- user input이 story creation decisions를 개선할 수 있는 곳에는 질문을 생성합니다.
- **확신이 없으면 질문하세요** - overconfidence는 poor stories로 이어집니다.

**평가할 question categories**(모든 category 고려):

- **User Personas** - user types, roles, characteristics, motivations에 대해 질문합니다.
- **Story Granularity** - 적절한 detail level, story size, breakdown approach에 대해 질문합니다.
- **Story Format** - format preferences, template usage, documentation standards에 대해 질문합니다.
- **Breakdown Approach** - organization method, prioritization, grouping strategies에 대해 질문합니다.
- **Acceptance Criteria** - detail level, format, testing approach, validation methods에 대해 질문합니다.
- **User Journeys** - user workflows, interaction patterns, experience flows에 대해 질문합니다.
- **Business Context** - business goals, success metrics, stakeholder needs에 대해 질문합니다.
- **Technical Constraints** - technical limitations, integration requirements, system boundaries에 대해 질문합니다.

## Step 4: Include Mandatory Story Artifacts in Plan

- story plan에는 다음 mandatory artifacts를 **항상** 포함합니다.
  - [ ] INVEST criteria를 따르는 user stories가 포함된 stories.md 생성
  - [ ] user archetypes와 characteristics가 포함된 personas.md 생성
  - [ ] stories가 Independent, Negotiable, Valuable, Estimable, Small, Testable한지 확인
  - [ ] 각 story에 acceptance criteria 포함
  - [ ] personas를 관련 user stories에 mapping

## Step 5: Present Story Options

- plan document에 story breakdown을 위한 여러 approaches를 포함합니다.
  - **User Journey-Based**: stories가 user workflows와 interactions를 따름
  - **Feature-Based**: stories를 system features와 capabilities 중심으로 구성
  - **Persona-Based**: stories를 서로 다른 user types와 needs별로 grouping
  - **Domain-Based**: stories를 business domains 또는 contexts 중심으로 구성
  - **Epic-Based**: stories를 sub-stories가 있는 hierarchical epics로 구조화
- 각 approach의 trade-offs와 benefits를 설명합니다.
- clear decision criteria가 있는 hybrid approaches를 허용합니다.

## Step 6: Store Story Plan

- embedded questions가 포함된 complete story plan을 `aidlc-docs/inception/plans/` directory에 저장합니다.
- Filename: `story-generation-plan.md`
- user input을 위한 모든 [Answer]: tags를 포함합니다.
- plan이 story development의 모든 aspects를 포괄하도록 합니다.

## Step 7: Request User Input

- 사용자에게 story plan document 안의 모든 [Answer]: tags를 직접 채우도록 요청합니다.
- audit trail과 decision documentation의 중요성을 강조합니다.
- [Answer]: tags를 채우는 방법에 대한 clear instructions를 제공합니다.
- 진행 전 모든 questions에 답해야 한다고 설명합니다.

## Step 8: Collect Answers

- 사용자가 document 안의 [Answer]: tags로 모든 questions에 답할 때까지 기다립니다.
- 모든 [Answer]: tags가 완료될 때까지 진행하지 마세요.
- document를 검토해 빈 [Answer]: tags가 없는지 확인합니다.

## Step 9: ANALYZE ANSWERS (MANDATORY)

진행 전에 모든 user answers를 신중히 검토해 다음을 확인해야 합니다.

- **Vague or ambiguous responses**: "mix of", "somewhere between", "not sure", "depends", "maybe", "probably"
- **Undefined criteria or terms**: clear definitions 없이 concepts를 참조
- **Contradictory answers**: 서로 충돌하는 responses
- **Missing generation details**: implementation을 위한 specific guidance가 부족한 answers
- **Answers that combine options**: clear decision rules 없이 여러 approaches를 병합한 responses
- **Incomplete explanations**: external factors를 참조하지만 정의하지 않은 answers
- **Assumption-based responses**: 명시되지 않은 knowledge를 assumption하는 answers

## Step 10: MANDATORY Follow-up Questions

step 9의 analysis에서 ambiguous answers가 하나라도 발견되면 반드시 다음을 수행합니다.

- [Answer]: tags를 사용해 별도 clarification questions file을 생성합니다.
- 모든 ambiguity가 완전히 해결될 때까지 approval로 진행하지 마세요.
- **CRITICAL**: 철저하게 처리합니다. 불명확한 모든 response에 follow-up questions를 묻습니다.
- required follow-up examples:
  - "You mentioned 'mix of A and B' - what specific criteria should determine when to use A vs B?"
  - "You said 'somewhere between A and B' - can you define the exact middle ground approach?"
  - "You indicated 'not sure' - what additional information would help you decide?"
  - "You mentioned 'depends on complexity' - how do you define complexity levels and thresholds?"
  - "You chose 'hybrid approach' - what are the specific rules for when to use each method?"
  - "You said 'probably X' - what factors would make it definitely X vs definitely not X?"
  - "You referenced 'standard practice' - can you define what that standard practice is?"

## Step 11: Avoid Implementation Details

- prioritization 또는 development tasks가 아니라 story creation methodology에 집중합니다.
- 이 stage에서는 technical generation을 논의하지 않습니다.
- development timelines 또는 sprint planning 생성을 피합니다.
- story structure와 format decisions에 집중합니다.

## Step 12: Log Approval Prompt

- approval을 요청하기 전에 prompt를 timestamp와 함께 `aidlc-docs/audit.md`에 기록합니다.
- complete approval prompt text를 포함합니다.
- ISO 8601 timestamp format을 사용합니다.

## Step 13: Wait for Explicit Approval of Plan

- 사용자가 story approach를 명시적으로 승인할 때까지 진행하지 마세요.
- approval은 clear and unambiguous해야 합니다.
- 사용자가 changes를 요청하면 plan을 업데이트하고 approval process를 반복합니다.

## Step 14: Record Approval Response

- 사용자 approval response를 timestamp와 함께 `aidlc-docs/audit.md`에 기록합니다.
- exact user response text를 포함합니다.
- approval status를 명확하게 표시합니다.

---

# PART 2: GENERATION

## Step 15: Load Story Generation Plan

- [ ] `aidlc-docs/inception/plans/story-generation-plan.md`에서 complete story plan을 읽습니다.
- [ ] 다음 uncompleted step(첫 번째 [ ] checkbox)을 식별합니다.
- [ ] 해당 step의 context와 requirements를 로드합니다.

## Step 16: Execute Current Step

- [ ] current step이 설명하는 내용을 정확히 수행합니다.
- [ ] plan에 지정된 story artifacts를 생성합니다.
- [ ] Planning에서 승인된 methodology와 format을 따릅니다.
- [ ] plan에 지정된 story breakdown approach를 사용합니다.

## Step 17: Update Progress

- [ ] story generation plan에서 completed step을 [x]로 표시합니다.
- [ ] `aidlc-docs/aidlc-state.md` current status를 업데이트합니다.
- [ ] 생성된 모든 artifacts를 저장합니다.

## Step 18: Continue or Complete Generation

- [ ] 남은 step이 있으면 Step 15로 돌아갑니다.
- [ ] 모든 step이 complete되면 stories가 next stage에 준비되었는지 검증합니다.
- [ ] 모든 mandatory artifacts가 생성되었는지 확인합니다.

## Step 19: Log Approval Prompt

- approval을 요청하기 전에 prompt를 timestamp와 함께 `aidlc-docs/audit.md`에 기록합니다.
- complete approval prompt text를 포함합니다.
- ISO 8601 timestamp format을 사용합니다.

## Step 20: Present Completion Message

- 다음 구조로 completion message를 제시합니다.
  1. **Completion Announcement**(mandatory): 항상 다음으로 시작합니다.

```markdown
# 📚 User Stories Complete
```

1. **AI Summary**(optional): generated stories에 대한 structured bullet-point summary를 제공합니다.
   - Format: "User stories generation has created [description]:"
   - 생성된 key personas를 bullet points로 나열합니다.
   - 생성된 user stories를 count와 organization과 함께 나열합니다.
   - story structure 및 compliance(INVEST criteria, acceptance criteria)를 언급합니다.
   - workflow instructions("please review", "let me know", "proceed to next phase", "before we proceed")를 포함하지 마세요.
   - factual하고 content-focused하게 유지합니다.
1. **Formatted Workflow Message**(mandatory): 항상 아래 exact format으로 끝냅니다.

```markdown
> **📋 <u>**REVIEW REQUIRED:**</u>**
> user stories와 personas를 검토해 주세요: `aidlc-docs/inception/user-stories/stories.md` 및 `aidlc-docs/inception/user-stories/personas.md`



> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** -  검토 결과에 따라 stories 또는 personas 수정을 요청하세요
> ✅ **Approve & Continue** - user stories를 승인하고 **Workflow Planning**으로 진행합니다

---
```

## Step 21: Wait for Explicit Approval of Generated Stories

- 사용자가 generated stories를 명시적으로 승인할 때까지 진행하지 마세요.
- approval은 clear and unambiguous해야 합니다.
- 사용자가 changes를 요청하면 stories를 업데이트하고 approval process를 반복합니다.

## Step 22: Record Approval Response

- 사용자 approval response를 timestamp와 함께 `aidlc-docs/audit.md`에 기록합니다.
- exact user response text를 포함합니다.
- approval status를 명확하게 표시합니다.

## Step 23: Update Progress

- `aidlc-state.md`에서 User Stories stage complete를 표시합니다.
- "Current Status" section을 업데이트합니다.
- next stage로 transition할 준비를 합니다.

---

# CRITICAL RULES

## Planning Phase Rules

- **CONTEXT-APPROPRIATE QUESTIONS**: 이 specific context와 관련 있는 questions만 묻습니다.
- **MANDATORY ANSWER ANALYSIS**: 진행 전 항상 answers를 분석해 ambiguities를 확인합니다.
- **NO PROCEEDING WITH AMBIGUITY**: generation 전에 vague answers를 모두 해결해야 합니다.
- **EXPLICIT APPROVAL REQUIRED**: generation 시작 전 사용자가 plan을 승인해야 합니다.

## Generation Phase Rules

- **NO HARDCODED LOGIC**: story generation plan에 쓰인 내용만 실행합니다.
- **FOLLOW PLAN EXACTLY**: step sequence에서 벗어나지 마세요.
- **UPDATE CHECKBOXES**: 각 step 완료 직후 [x]로 표시합니다.
- **USE APPROVED METHODOLOGY**: Planning에서 승인된 story approach를 따릅니다.
- **VERIFY COMPLETION**: 진행 전 모든 story artifacts가 complete한지 확인합니다.

## Completion Criteria

- 모든 planning questions가 답변되고 ambiguities가 해결됨
- Story plan이 사용자에게 명시적으로 승인됨
- story generation plan의 모든 steps가 [x]로 표시됨
- 모든 story artifacts가 plan에 따라 생성됨(stories.md, personas.md)
- generated stories가 사용자에게 명시적으로 승인됨
- stories가 검증되어 next stage에 준비됨
