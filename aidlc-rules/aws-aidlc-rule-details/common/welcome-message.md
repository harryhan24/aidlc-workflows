# AI-DLC Welcome Message

**Purpose**: 이 파일에는 AI-DLC 워크플로를 시작할 때 한 번 표시해야 하는 사용자 대상 welcome message가 들어 있습니다.

---

# 👋 AI-DLC (AI-Driven Development Life Cycle)에 오신 것을 환영합니다! 👋

사용자의 구체적인 필요에 맞춰 지능적으로 조정되는 adaptive software development workflow를 안내하겠습니다.

## AI-DLC란?

AI-DLC는 프로젝트의 필요에 맞게 적응하는 구조적이면서도 유연한 software development process입니다. 다음을 수행하는 숙련된 software architect가 함께한다고 생각하면 됩니다.

- **요구 사항을 분석하고** 필요할 때 clarification questions를 묻습니다.
- **복잡도와 위험을 기준으로 최적 접근 방식을 계획합니다.**
- 단순 변경에는 **불필요한 단계를 건너뛰고**, 복잡한 프로젝트에는 포괄적인 coverage를 제공합니다.
- 모든 decision과 rationale의 complete record가 남도록 **모든 것을 문서화합니다.**
- 명확한 checkpoint와 approval gate로 **각 phase를 안내합니다.**

## The Three-Phase Lifecycle

```text
                         사용자 요청
                              |
                              v
        +---------------------------------------+
        |     INCEPTION PHASE                   |
        |     Planning & Application Design     |
        +---------------------------------------+
        | * Workspace Detection (ALWAYS)        |
        | * Reverse Engineering (COND)          |
        | * Requirements Analysis (ALWAYS)      |
        | * User Stories (CONDITIONAL)          |
        | * Workflow Planning (ALWAYS)          |
        | * Application Design (CONDITIONAL)    |
        | * Units Generation (CONDITIONAL)      |
        +---------------------------------------+
                              |
                              v
        +---------------------------------------+
        |     CONSTRUCTION PHASE                |
        |     Design, Implementation & Test     |
        +---------------------------------------+
        | * Per-Unit Loop (for each unit):      |
        |   - Functional Design (COND)          |
        |   - NFR Requirements Assess (COND)    |
        |   - NFR Design (COND)                 |
        |   - Infrastructure Design (COND)      |
        |   - Code Generation (ALWAYS)          |
        | * Build and Test (ALWAYS)             |
        +---------------------------------------+
                              |
                              v
        +---------------------------------------+
        |     OPERATIONS PHASE                  |
        |     Placeholder for Future            |
        +---------------------------------------+
        | * Operations (PLACEHOLDER)            |
        +---------------------------------------+
                              |
                              v
                          완료
```

### Phase Breakdown:

**INCEPTION PHASE** - *Planning & Application Design*

- **Purpose**: 무엇을 만들고 왜 만드는지 결정합니다.
- **Activities**: requirements 이해, 기존 코드 분석(있는 경우), 접근 방식 planning
- **Output**: 명확한 requirements, execution plan, 병렬 개발을 위한 units of work 수에 대한 결정
- **Your Role**: 질문에 답하고, 계획을 검토하고, 방향을 승인합니다.

**CONSTRUCTION PHASE** - *Detailed Design, Implementation & Test*

- **Purpose**: 어떻게 만들지 결정합니다.
- **Activities**: 상세 설계(필요 시), code generation, comprehensive testing
- **Output**: 동작하는 code, tests, build instructions
- **Your Role**: design을 검토하고, implementation plan을 승인하고, 결과를 검증합니다.

**OPERATIONS PHASE** - *Deployment & Monitoring (Future)*

- **Purpose**: 어떻게 DEPLOY하고 RUN할지 결정합니다.
- **Status**: 향후 deployment 및 monitoring workflow를 위한 placeholder
- **Current State**: build and test 활동은 CONSTRUCTION phase에서 처리합니다.

## Key Principles:

- ⚡ **Fully Adaptive**: 각 stage를 사용자의 필요에 따라 독립적으로 평가합니다.
- 🎯 **Efficient**: 단순 변경은 필수 stage만 실행합니다.
- 📋 **Comprehensive**: 복잡한 변경은 모든 safeguard와 함께 완전하게 다룹니다.
- 🔍 **Transparent**: 작업 시작 전에 execution plan을 보고 승인합니다.
- 📝 **Documented**: 모든 decision과 change의 complete audit trail을 남깁니다.
- 🎛️ **User Control**: stage를 포함하거나 제외하도록 요청할 수 있습니다.

## What Happens Next:

1. **workspace를 분석해** 새 프로젝트인지 기존 프로젝트인지 파악합니다.
2. **requirements를 수집하고** 필요한 경우 clarification questions를 묻습니다.
3. **execution plan을 작성해** 어떤 stage를 실행할지와 그 이유를 보여줍니다.
4. **계획을 검토하고 승인합니다**(또는 변경을 요청합니다).
5. 각 주요 stage의 checkpoint와 함께 **계획을 실행합니다.**
6. 완전한 documentation과 tests가 포함된 **동작하는 code를 받습니다.**

AI-DLC process는 다음에 맞춰 적응합니다.

- 📋 의도의 명확성과 복잡도
- 🔍 기존 코드베이스 상태
- 🎯 변경의 범위와 영향
- ⚡ 위험 및 품질 요구 사항

시작하겠습니다!
