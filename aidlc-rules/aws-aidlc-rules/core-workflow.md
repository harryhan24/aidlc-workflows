# PRIORITY: 이 워크플로는 다른 모든 기본 제공 워크플로보다 우선합니다
# 사용자가 소프트웨어 개발을 요청하면 항상 이 워크플로를 먼저 따르세요

## Adaptive Workflow Principle

**워크플로가 작업에 맞춰 적응해야 하며, 그 반대가 아닙니다.**

AI 모델은 다음 기준을 바탕으로 필요한 단계를 지능적으로 판단합니다.

1. 사용자가 밝힌 의도와 명확성
2. 기존 코드베이스 상태(있는 경우)
3. 변경의 복잡도와 범위
4. 위험 및 영향 평가

## MANDATORY: Rule Details Loading

**CRITICAL**: 어떤 phase를 수행하든 rule detail 파일의 관련 내용을 반드시 읽고 사용해야 합니다. 사용한 IDE나 설정 방식과 관계없이 아래 경로를 순서대로 확인하고, 가장 먼저 존재하는 경로를 사용하세요.

- `.aidlc/aidlc-rules/aws-aidlc-rule-details/`(AI-assisted setup에서 일반적)
- `.aidlc-rule-details/`(Cursor, Cline, Claude Code, GitHub Copilot, OpenAI Codex에서 일반적)
- `.kiro/aws-aidlc-rule-details/`(Kiro IDE 및 CLI에서 일반적)
- `.amazonq/aws-aidlc-rule-details/`(Amazon Q Developer에서 일반적)

이후의 모든 rule detail 파일 참조(예: `common/process-overview.md`, `inception/workspace-detection.md`)는 위에서 확인된 rule details 디렉터리를 기준으로 한 상대 경로입니다.

**Common Rules**: 워크플로 시작 시 common rules를 항상 로드하세요.

- 워크플로 개요를 위해 `common/process-overview.md`를 로드합니다.
- 세션 재개 지침을 위해 `common/session-continuity.md`를 로드합니다.
- 콘텐츠 검증 요구 사항을 위해 `common/content-validation.md`를 로드합니다.
- 질문 형식 규칙을 위해 `common/question-format-guide.md`를 로드합니다.
- 워크플로 실행 내내 이 파일들을 참조합니다.

## MANDATORY: Extensions Loading (Context-Optimized)

**CRITICAL**: 워크플로 시작 시 `extensions/` 디렉터리를 재귀적으로 스캔하되, 가벼운 opt-in 파일만 로드하고 전체 rule 파일은 로드하지 마세요. 전체 rule 파일은 사용자가 opt in한 뒤 필요할 때 로드합니다.

**Loading process**:

1. `extensions/` 아래의 모든 하위 디렉터리를 나열합니다(예: `extensions/security/`, `extensions/compliance/`).
2. 각 하위 디렉터리에서 `*.opt-in.md` 파일만 로드합니다. 이 파일에는 extension의 opt-in 프롬프트가 들어 있습니다. 대응되는 rules file은 명명 규칙으로 도출합니다. `.opt-in.md` 접미사를 제거하고 `.md`를 붙입니다(예: `security-baseline.opt-in.md` → `security-baseline.md`).
3. 이 단계에서는 전체 rule 파일(예: `security-baseline.md`)을 로드하지 마세요.

**Deferred Rule Loading**:

- Requirements Analysis 중 로드된 `*.opt-in.md` 파일의 opt-in 프롬프트를 사용자에게 제시합니다.
- 사용자가 extension에 opt in하면 그 시점에 대응되는 rules file(명명 규칙으로 도출)을 로드합니다.
- 사용자가 opt out하면 전체 rules file은 로드하지 않습니다. 이렇게 해서 context를 절약합니다.
- 대응되는 `*.opt-in.md` 파일이 없는 extension은 항상 적용합니다. 워크플로 시작 즉시 해당 rule 파일을 로드하세요.

**Enforcement**(로드/활성화된 extension에만 적용):

- Extension rules는 선택적 안내가 아니라 강제 제약입니다.
- 각 단계에서 모델은 현재 단계의 목적, 생성되는 산출물, 작업 context를 바탕으로 어떤 extension rule이 적용되는지 지능적으로 평가하고, 관련 있는 규칙만 적용합니다.
- 현재 단계에 적용되지 않는 규칙은 compliance summary에서 N/A로 표시해야 합니다. 이는 blocking finding이 아닙니다.
- 활성화된 extension rule 중 적용 가능한 규칙을 준수하지 않으면 **blocking finding**입니다. 해결 전에는 stage completion을 제시하지 마세요.
- stage completion을 제시할 때 extension rule compliance 요약을 포함하세요. 각 규칙별로 compliant/non-compliant/N/A를 표시하고, N/A 판단에는 간단한 근거를 덧붙입니다.

**Conditional Enforcement**: Extension은 조건부로 활성화/비활성화될 수 있습니다. opt-in 메커니즘은 `inception/requirements-analysis.md`를 참조하세요. 어떤 stage에서든 extension을 적용하기 전에 `aidlc-docs/aidlc-state.md`의 `## Extension Configuration` 아래 `Enabled` 상태를 확인하세요. 비활성화된 extension은 건너뛰고 audit.md에 skip을 기록합니다. 설정이 없으면 기본적으로 적용된 것으로 간주합니다.

## MANDATORY: Content Validation

**CRITICAL**: 어떤 파일이든 생성하기 전에 `common/content-validation.md` 규칙에 따라 콘텐츠를 반드시 검증해야 합니다.

- Mermaid diagram 문법을 검증합니다.
- ASCII art diagram을 검증합니다(`common/ascii-diagram-standards.md` 참조).
- 특수 문자를 올바르게 escape합니다.
- 복잡한 시각 콘텐츠에는 text alternative를 제공합니다.
- 콘텐츠 parsing compatibility를 테스트합니다.

## MANDATORY: Question File Format

**CRITICAL**: 어떤 phase에서든 질문할 때는 question format guidelines를 반드시 따라야 합니다.

**다음 내용을 포함한 전체 질문 형식 규칙은 `common/question-format-guide.md`를 참조하세요.**

- Multiple choice format(A, B, C, D, E options)
- [Answer]: tag 사용
- 답변 검증 및 모호성 해소

## MANDATORY: Custom Welcome Message

**CRITICAL**: 어떤 소프트웨어 개발 요청이든 시작할 때 welcome message를 반드시 표시해야 합니다.

**Welcome Message 표시 방법**:

1. 확인된 rule details 디렉터리의 `common/welcome-message.md`에서 welcome message를 로드합니다.
2. 사용자에게 전체 메시지를 표시합니다.
3. 새 워크플로 시작 시 한 번만 수행합니다.
4. context 공간을 절약하기 위해 이후 상호작용에서는 이 파일을 다시 로드하지 마세요.

# Adaptive Software Development Workflow

---

# INCEPTION PHASE

**Purpose**: 계획 수립, 요구 사항 수집, 아키텍처 결정

**Focus**: 무엇을 만들고 왜 만드는지 결정합니다.

**Stages in INCEPTION PHASE**:

- Workspace Detection (ALWAYS)
- Reverse Engineering (CONDITIONAL - Brownfield only)
- Requirements Analysis (ALWAYS - Adaptive depth)
- User Stories (CONDITIONAL)
- Workflow Planning (ALWAYS)
- Application Design (CONDITIONAL)
- Units Generation (CONDITIONAL)

---

## Workspace Detection (ALWAYS EXECUTE)

1. **MANDATORY**: 최초 사용자 요청을 완전한 raw input으로 audit.md에 기록합니다.
2. `inception/workspace-detection.md`의 모든 단계를 로드합니다.
3. workspace detection을 실행합니다.
   - 기존 aidlc-state.md가 있는지 확인합니다(있으면 resume).
   - workspace에서 기존 코드를 스캔합니다.
   - brownfield인지 greenfield인지 판단합니다.
   - 기존 reverse engineering 산출물이 있는지 확인합니다.
4. 다음 phase를 결정합니다. Brownfield이고 산출물이 없으면 Reverse Engineering, 그 외에는 Requirements Analysis로 진행합니다.
5. **MANDATORY**: findings를 audit.md에 기록합니다.
6. 사용자에게 완료 메시지를 제시합니다(메시지 형식은 workspace-detection.md 참조).
7. 다음 phase로 자동 진행합니다.

## Reverse Engineering (CONDITIONAL - Brownfield Only)

**Execute IF**:

- 기존 코드베이스가 감지됨
- 이전 reverse engineering 산출물이 없음

**Skip IF**:

- Greenfield 프로젝트
- 이전 reverse engineering 산출물이 있음

**Execution**:

1. **MANDATORY**: reverse engineering 시작을 audit.md에 기록합니다.
2. `inception/reverse-engineering.md`의 모든 단계를 로드합니다.
3. reverse engineering을 실행합니다.
   - 모든 package와 component를 분석합니다.
   - 시스템 전체의 business transactions를 포괄하는 business overview를 생성합니다.
   - architecture documentation을 생성합니다.
   - code structure documentation을 생성합니다.
   - API documentation을 생성합니다.
   - component inventory를 생성합니다.
   - business transaction이 component 전반에서 어떻게 구현되는지 보여주는 Interaction Diagrams를 생성합니다.
   - technology stack documentation을 생성합니다.
   - dependencies documentation을 생성합니다.

4. **Wait for Explicit Approval**: 상세 완료 메시지를 제시합니다(메시지 형식은 reverse-engineering.md 참조). 사용자가 확인할 때까지 진행하지 마세요.
5. **MANDATORY**: 사용자 응답을 완전한 raw input으로 audit.md에 기록합니다.

## Requirements Analysis (ALWAYS EXECUTE - Adaptive Depth)

**항상 실행**하지만, 요청의 명확성과 복잡도에 따라 depth가 달라집니다.

- **Minimal**: 단순하고 명확한 요청 - intent analysis만 문서화합니다.
- **Standard**: 일반적인 복잡도 - functional/non-functional requirements를 수집합니다.
- **Comprehensive**: 복잡하고 위험도가 높음 - traceability를 포함한 상세 요구 사항을 작성합니다.

**Execution**:

1. **MANDATORY**: 이 phase 중 발생한 모든 사용자 입력을 audit.md에 기록합니다.
2. `inception/requirements-analysis.md`의 모든 단계를 로드합니다.
3. requirements analysis를 실행합니다.
   - reverse engineering 산출물을 로드합니다(brownfield인 경우).
   - 사용자 요청을 분석합니다(intent analysis).
   - 필요한 requirements depth를 결정합니다.
   - 현재 requirements를 평가합니다.
   - 필요한 경우 clarification questions를 묻습니다.
   - requirements document를 생성합니다.
4. 적절한 depth(minimal/standard/comprehensive)로 실행합니다.
5. **Wait for Explicit Approval**: requirements-analysis.md 상세 단계의 approval format을 따릅니다. 사용자가 확인할 때까지 진행하지 마세요.
6. **MANDATORY**: 사용자 응답을 완전한 raw input으로 audit.md에 기록합니다.

## User Stories (CONDITIONAL)

**INTELLIGENT ASSESSMENT**: user stories가 가치를 더하는지 판단하기 위해 multi-factor analysis를 사용합니다.

**ALWAYS Execute IF**(High Priority Indicators):

- 새로운 user-facing 기능 또는 기능성
- 사용자 workflow 또는 interaction에 영향을 주는 변경
- 여러 user type 또는 persona가 관여함
- acceptance criteria가 필요한 복잡한 business requirements
- cross-functional team collaboration이 필요함
- customer-facing API 또는 service 변경
- 새로운 product capabilities 또는 enhancements

**LIKELY Execute IF**(Medium Priority - Assess Complexity):

- 기존 user-facing 기능 수정
- user experience에 간접 영향을 주는 backend 변경
- user workflow에 영향을 주는 integration 작업
- 사용자에게 보이는 이점이 있는 performance 개선
- user interaction에 영향을 주는 security enhancement
- user data 또는 report에 영향을 주는 data model 변경

**COMPLEXITY-BASED ASSESSMENT**: medium priority 사례에서는 다음에 해당하면 user stories를 실행합니다.

- 요청이 여러 component 또는 service를 포함함
- 변경이 여러 user touchpoint에 걸쳐 있음
- business logic이 복잡하거나 여러 scenario가 있음
- story가 명확히 할 수 있는 requirements ambiguity가 있음
- 구현이 여러 user journey에 영향을 줌
- 변경의 business impact 또는 risk가 큼

**SKIP ONLY IF**(Low Priority - Simple Cases):

- 사용자 영향이 전혀 없는 순수 internal refactoring
- 범위가 명확하고 격리된 단순 bug fix
- user-facing 영향이 없는 infrastructure 변경
- functional change가 없는 technical debt cleanup
- developer tooling 또는 build process 개선
- documentation-only updates

**ASSESSMENT CRITERIA**: 확신이 없으면 다음 경우 user stories 포함을 선호합니다.

- business stakeholder가 관여하는 요청
- user acceptance testing이 필요한 변경
- 여러 implementation approach가 가능한 기능
- team shared understanding이 도움이 되는 작업
- requirements clarity가 중요한 프로젝트

**ASSESSMENT PROCESS**:

1. 요청의 복잡도와 범위를 분석합니다.
2. user impact를 식별합니다(직접 또는 간접).
3. business context와 stakeholder needs를 평가합니다.
4. team collaboration benefits를 고려합니다.
5. 경계 사례에서는 기본적으로 포함합니다.

**Note**: Requirements Analysis가 실행되었다면 Stories는 해당 requirements를 참조하고 그 위에 작성할 수 있습니다.

**User Stories는 한 stage 안에서 두 부분으로 구성됩니다.**

1. **Part 1 - Planning**: 질문이 포함된 story plan을 만들고, 답변을 수집하고, 모호성을 분석하고, 승인을 받습니다.
2. **Part 2 - Generation**: 승인된 plan을 실행해 stories와 personas를 생성합니다.

**Execution**:

1. **MANDATORY**: 이 phase 중 발생한 모든 사용자 입력을 audit.md에 기록합니다.
2. `inception/user-stories.md`의 모든 단계를 로드합니다.
3. **MANDATORY**: user stories가 필요한지 검증하기 위해 intelligent assessment(user-stories.md의 Step 1)를 수행합니다.
4. reverse engineering 산출물을 로드합니다(brownfield인 경우).
5. Requirements가 있으면 stories를 만들 때 참조합니다.
6. 적절한 depth(minimal/standard/comprehensive)로 실행합니다.
7. **PART 1 - Planning**: 질문이 포함된 story plan을 만들고, 사용자 답변을 기다리고, 모호성을 분석하고, 승인을 받습니다.
8. **PART 2 - Generation**: 승인된 plan을 실행해 stories와 personas를 생성합니다.
9. **Wait for Explicit Approval**: user-stories.md 상세 단계의 approval format을 따릅니다. 사용자가 확인할 때까지 진행하지 마세요.
10. **MANDATORY**: 사용자 응답을 완전한 raw input으로 audit.md에 기록합니다.

## Workflow Planning (ALWAYS EXECUTE)

1. **MANDATORY**: 이 phase 중 발생한 모든 사용자 입력을 audit.md에 기록합니다.
2. `inception/workflow-planning.md`의 모든 단계를 로드합니다.
3. **MANDATORY**: `common/content-validation.md`에서 content validation rules를 로드합니다.
4. 이전 context를 모두 로드합니다.
   - reverse engineering 산출물(brownfield인 경우)
   - intent analysis
   - requirements(실행된 경우)
   - user stories(실행된 경우)
5. workflow planning을 실행합니다.
   - 실행할 phases를 결정합니다.
   - 각 phase의 depth level을 결정합니다.
   - multi-package change sequence를 만듭니다(brownfield인 경우).
   - workflow visualization을 생성합니다(쓰기 전에 Mermaid 문법을 검증).
6. **MANDATORY**: content-validation.md 규칙에 따라 파일 생성 전 모든 콘텐츠를 검증합니다.
7. **Wait for Explicit Approval**: workflow-planning.md Step 9의 표현을 사용해 권장 사항을 제시하고, 사용자가 권장 사항을 override할 수 있음을 강조합니다. 사용자가 확인할 때까지 진행하지 마세요.
8. **MANDATORY**: 사용자 응답을 완전한 raw input으로 audit.md에 기록합니다.

## Application Design (CONDITIONAL)

**Execute IF**:

- 새로운 component 또는 service가 필요함
- component method와 business rule 정의가 필요함
- service layer design이 필요함
- component dependency를 명확히 해야 함

**Skip IF**:

- 변경이 기존 component boundary 안에 있음
- 새로운 component 또는 method가 없음
- 순수 implementation 변경

**Execution**:

1. **MANDATORY**: 이 phase 중 발생한 모든 사용자 입력을 audit.md에 기록합니다.
2. `inception/application-design.md`의 모든 단계를 로드합니다.
3. reverse engineering 산출물을 로드합니다(brownfield인 경우).
4. 적절한 depth(minimal/standard/comprehensive)로 실행합니다.
5. **Wait for Explicit Approval**: 상세 완료 메시지를 제시합니다(메시지 형식은 application-design.md 참조). 사용자가 확인할 때까지 진행하지 마세요.
6. **MANDATORY**: 사용자 응답을 완전한 raw input으로 audit.md에 기록합니다.

## Units Generation (CONDITIONAL)

**Execute IF**:

- 시스템을 여러 units of work로 분해해야 함
- 여러 service 또는 module이 필요함
- 구조화된 breakdown이 필요한 복잡한 시스템

**Skip IF**:

- 단일 simple unit
- decomposition이 필요 없음
- 단일 component의 단순 구현

**Execution**:

1. **MANDATORY**: 이 phase 중 발생한 모든 사용자 입력을 audit.md에 기록합니다.
2. `inception/units-generation.md`의 모든 단계를 로드합니다.
3. reverse engineering 산출물을 로드합니다(brownfield인 경우).
4. 적절한 depth(minimal/standard/comprehensive)로 실행합니다.
5. **Wait for Explicit Approval**: 상세 완료 메시지를 제시합니다(메시지 형식은 units-generation.md 참조). 사용자가 확인할 때까지 진행하지 마세요.
6. **MANDATORY**: 사용자 응답을 완전한 raw input으로 audit.md에 기록합니다.

---

# 🟢 CONSTRUCTION PHASE

**Purpose**: 상세 설계, NFR 구현, 코드 생성

**Focus**: 어떻게 만들지 결정합니다.

**Stages in CONSTRUCTION PHASE**:

- Per-Unit Loop(각 unit마다 실행):
  - Functional Design (CONDITIONAL, per-unit)
  - NFR Requirements (CONDITIONAL, per-unit)
  - NFR Design (CONDITIONAL, per-unit)
  - Infrastructure Design (CONDITIONAL, per-unit)
  - Code Generation (ALWAYS, per-unit)
- Build and Test (ALWAYS - 모든 unit 완료 후)

**Note**: 각 unit은 다음 unit으로 넘어가기 전에 완전히 완료합니다(design + code).

---

## Per-Unit Loop (Executes for Each Unit)

**각 unit of work마다 다음 stage를 순서대로 실행합니다.**

### Functional Design (CONDITIONAL, per-unit)

**Execute IF**:

- 새로운 data model 또는 schema
- 복잡한 business logic
- business rule의 상세 설계 필요

**Skip IF**:

- 단순 logic 변경
- 새로운 business logic 없음

**Execution**:

1. **MANDATORY**: 이 stage 중 발생한 모든 사용자 입력을 audit.md에 기록합니다.
2. `construction/functional-design.md`의 모든 단계를 로드합니다.
3. 이 unit의 functional design을 실행합니다.
4. **MANDATORY**: functional-design.md에 정의된 표준 2-option completion message를 제시합니다. emergent 3-option behavior를 사용하지 마세요.
5. **Wait for Explicit Approval**: 사용자는 "Request Changes" 또는 "Continue to Next Stage" 중 하나를 선택해야 합니다. 사용자가 확인할 때까지 진행하지 마세요.
6. **MANDATORY**: 사용자 응답을 완전한 raw input으로 audit.md에 기록합니다.

### NFR Requirements (CONDITIONAL, per-unit)

**Execute IF**:

- performance requirements가 있음
- security considerations가 필요함
- scalability concerns가 있음
- tech stack selection이 필요함

**Skip IF**:

- NFR requirements 없음
- tech stack이 이미 결정됨

**Execution**:

1. **MANDATORY**: 이 stage 중 발생한 모든 사용자 입력을 audit.md에 기록합니다.
2. `construction/nfr-requirements.md`의 모든 단계를 로드합니다.
3. 이 unit의 NFR assessment를 실행합니다.
4. **MANDATORY**: nfr-requirements.md에 정의된 표준 2-option completion message를 제시합니다. emergent behavior를 사용하지 마세요.
5. **Wait for Explicit Approval**: 사용자는 "Request Changes" 또는 "Continue to Next Stage" 중 하나를 선택해야 합니다. 사용자가 확인할 때까지 진행하지 마세요.
6. **MANDATORY**: 사용자 응답을 완전한 raw input으로 audit.md에 기록합니다.

### NFR Design (CONDITIONAL, per-unit)

**Execute IF**:

- NFR Requirements가 실행됨
- NFR pattern을 반영해야 함

**Skip IF**:

- NFR requirements 없음
- NFR Requirements를 건너뜀

**Execution**:

1. **MANDATORY**: 이 stage 중 발생한 모든 사용자 입력을 audit.md에 기록합니다.
2. `construction/nfr-design.md`의 모든 단계를 로드합니다.
3. 이 unit의 NFR design을 실행합니다.
4. **MANDATORY**: nfr-design.md에 정의된 표준 2-option completion message를 제시합니다. emergent behavior를 사용하지 마세요.
5. **Wait for Explicit Approval**: 사용자는 "Request Changes" 또는 "Continue to Next Stage" 중 하나를 선택해야 합니다. 사용자가 확인할 때까지 진행하지 마세요.
6. **MANDATORY**: 사용자 응답을 완전한 raw input으로 audit.md에 기록합니다.

### Infrastructure Design (CONDITIONAL, per-unit)

**Execute IF**:

- infrastructure service mapping이 필요함
- deployment architecture가 필요함
- cloud resource specification이 필요함

**Skip IF**:

- infrastructure 변경 없음
- infrastructure가 이미 정의됨

**Execution**:

1. **MANDATORY**: 이 stage 중 발생한 모든 사용자 입력을 audit.md에 기록합니다.
2. `construction/infrastructure-design.md`의 모든 단계를 로드합니다.
3. 이 unit의 infrastructure design을 실행합니다.
4. **MANDATORY**: infrastructure-design.md에 정의된 표준 2-option completion message를 제시합니다. emergent behavior를 사용하지 마세요.
5. **Wait for Explicit Approval**: 사용자는 "Request Changes" 또는 "Continue to Next Stage" 중 하나를 선택해야 합니다. 사용자가 확인할 때까지 진행하지 마세요.
6. **MANDATORY**: 사용자 응답을 완전한 raw input으로 audit.md에 기록합니다.

### Code Generation (ALWAYS EXECUTE, per-unit)

**각 unit마다 항상 실행합니다.**

**Code Generation은 한 stage 안에서 두 부분으로 구성됩니다.**

1. **Part 1 - Planning**: 명시적 단계가 포함된 상세 code generation plan을 만듭니다.
2. **Part 2 - Generation**: 승인된 plan을 실행해 code, tests, artifacts를 생성합니다.

**Execution**:

1. **MANDATORY**: 이 stage 중 발생한 모든 사용자 입력을 audit.md에 기록합니다.
2. `construction/code-generation.md`의 모든 단계를 로드합니다.
3. **PART 1 - Planning**: checkbox가 포함된 code generation plan을 만들고 사용자 승인을 받습니다.
4. **PART 2 - Generation**: 승인된 plan을 실행해 이 unit의 code를 생성합니다.
5. **MANDATORY**: code-generation.md에 정의된 표준 2-option completion message를 제시합니다. emergent behavior를 사용하지 마세요.
6. **Wait for Explicit Approval**: 사용자는 "Request Changes" 또는 "Continue to Next Stage" 중 하나를 선택해야 합니다. 사용자가 확인할 때까지 진행하지 마세요.
7. **MANDATORY**: 사용자 응답을 완전한 raw input으로 audit.md에 기록합니다.

---

## Build and Test (ALWAYS EXECUTE)

1. **MANDATORY**: 이 phase 중 발생한 모든 사용자 입력을 audit.md에 기록합니다.
2. `construction/build-and-test.md`의 모든 단계를 로드합니다.
3. 종합 build and test instructions를 생성합니다.
   - 모든 unit의 build instructions
   - unit test execution instructions
   - integration test instructions(unit 간 interaction 테스트)
   - performance test instructions(해당되는 경우)
   - 필요에 따른 추가 test instructions(contract tests, security tests, e2e tests)
4. build-and-test/ 하위 디렉터리에 instruction files를 생성합니다. build-instructions.md, unit-test-instructions.md, integration-test-instructions.md, performance-test-instructions.md, build-and-test-summary.md
5. **Wait for Explicit Approval**: "**Build and test instructions complete. Ready to proceed to Operations stage?**"라고 묻습니다. 사용자가 확인할 때까지 진행하지 마세요.
6. **MANDATORY**: 사용자 응답을 완전한 raw input으로 audit.md에 기록합니다.

---

# 🟡 OPERATIONS PHASE

**Purpose**: 향후 deployment 및 monitoring workflow를 위한 placeholder

**Focus**: 어떻게 DEPLOY하고 RUN할지 결정합니다(향후 확장).

**Stages in OPERATIONS PHASE**:

- Operations (PLACEHOLDER)

---

## Operations (PLACEHOLDER)

**Status**: 이 stage는 현재 향후 확장을 위한 placeholder입니다.

Operations stage는 최종적으로 다음을 포함하게 됩니다.

- deployment planning and execution
- monitoring and observability setup
- incident response procedures
- maintenance and support workflows
- production readiness checklists

**Current State**: 모든 build and test 활동은 CONSTRUCTION phase에서 처리합니다.

## Key Principles

- **Adaptive Execution**: 가치를 더하는 stage만 실행합니다.
- **Transparent Planning**: 시작 전에 항상 execution plan을 보여줍니다.
- **User Control**: 사용자는 stage inclusion/exclusion을 요청할 수 있습니다.
- **Progress Tracking**: 실행 및 skip된 stage를 aidlc-state.md에 업데이트합니다.
- **Complete Audit Trail**: 모든 사용자 입력과 AI 응답을 timestamp와 함께 audit.md에 기록합니다.
  - **CRITICAL**: 사용자의 COMPLETE RAW INPUT을 제공된 그대로 캡처합니다.
  - **CRITICAL**: audit log에서 사용자 입력을 요약하거나 바꾸어 말하지 않습니다.
  - **CRITICAL**: approval뿐 아니라 모든 interaction을 기록합니다.
- **Quality Focus**: 복잡한 변경은 완전하게 다루고, 단순한 변경은 효율적으로 처리합니다.
- **Content Validation**: content-validation.md 규칙에 따라 파일 생성 전 항상 콘텐츠를 검증합니다.
- **NO EMERGENT BEHAVIOR**: Construction phases는 각 rule file에 정의된 표준 2-option completion messages를 반드시 사용해야 합니다. 3-option menu나 다른 emergent navigation pattern을 만들지 마세요.

## MANDATORY: Plan-Level Checkbox Enforcement

### MANDATORY RULES FOR PLAN EXECUTION

1. **plan checkbox를 업데이트하지 않고 어떤 작업도 완료하지 마세요.**
2. **plan file에 설명된 어떤 step이든 완료한 직후 해당 step을 [x]로 표시하세요.**
3. **이 작업은 work가 완료된 같은 interaction 안에서 수행해야 합니다.**
4. **NO EXCEPTIONS**: 모든 plan step completion은 checkbox update로 추적되어야 합니다.

### Two-Level Checkbox Tracking System

- **Plan-Level**: 각 stage 안의 상세 execution progress를 추적합니다.
- **Stage-Level**: aidlc-state.md의 전체 workflow progress를 추적합니다.
- **Update immediately**: 모든 progress update는 work가 완료된 같은 interaction 안에서 수행합니다.

## Prompts Logging Requirements

- **MANDATORY**: 모든 user input(prompts, questions, responses)을 timestamp와 함께 audit.md에 기록합니다.
- **MANDATORY**: 사용자의 COMPLETE RAW INPUT을 제공된 그대로 캡처합니다(절대 요약하지 않음).
- **MANDATORY**: 사용자에게 묻기 전에 모든 approval prompt를 timestamp와 함께 기록합니다.
- **MANDATORY**: 사용자 응답을 받은 뒤 모든 user response를 timestamp와 함께 기록합니다.
- **CRITICAL**: audit.md에는 항상 append/edit 방식으로 변경을 추가하고, 내용을 완전히 덮어쓰는 tools/commands를 절대 사용하지 마세요.
- **CRITICAL**: audit.md 전체 내용을 덮어쓰는 file writing tools/commands를 절대 사용하지 마세요. 이는 duplication을 유발합니다.
- timestamp는 ISO 8601 형식(YYYY-MM-DDTHH:MM:SSZ)을 사용합니다.
- 각 entry에 stage context를 포함합니다.

### Audit Log Format

```markdown
## [Stage Name or Interaction Type]
**Timestamp**: [ISO timestamp]
**User Input**: "[Complete raw user input - never summarized]"
**AI Response**: "[AI's response or action taken]"
**Context**: [Stage, action, or decision made]

---
```

### Correct Tool Usage for audit.md

✅ CORRECT:

1. audit.md 파일을 읽습니다.
2. 변경 사항을 추가하기 위해 파일에 append/edit합니다.

❌ WRONG:

1. audit.md 파일을 읽습니다.
2. 읽은 내용에 새로 추가하려는 변경 사항을 더해 audit.md 전체를 완전히 덮어씁니다.

## Directory Structure

```text
<WORKSPACE-ROOT>/                   # ⚠️ 여기에 application code 배치
├── [project-specific structure]    # 프로젝트별로 다름(code-generation.md 참조)
│
├── aidlc-docs/                     # 📄 documentation 전용
│   ├── inception/                  # 🔵 INCEPTION PHASE
│   │   ├── plans/
│   │   ├── reverse-engineering/    # Brownfield 전용
│   │   ├── requirements/
│   │   ├── user-stories/
│   │   └── application-design/
│   ├── construction/               # 🟢 CONSTRUCTION PHASE
│   │   ├── plans/
│   │   ├── {unit-name}/
│   │   │   ├── functional-design/
│   │   │   ├── nfr-requirements/
│   │   │   ├── nfr-design/
│   │   │   ├── infrastructure-design/
│   │   │   └── code/               # Markdown summary만
│   │   └── build-and-test/
│   ├── operations/                 # 🟡 OPERATIONS PHASE(placeholder)
│   ├── aidlc-state.md
│   └── audit.md
```

**CRITICAL RULE**:

- Application code: Workspace root(절대 aidlc-docs/ 안에 두지 않음)
- Documentation: aidlc-docs/ only
- Project structure: project type별 pattern은 code-generation.md를 참조하세요.
