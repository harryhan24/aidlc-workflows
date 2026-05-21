# AI-DLC Terminology Glossary

## Core Terminology

### Phase vs Stage

**Phase**: AI-DLC의 세 가지 high-level lifecycle phases 중 하나입니다.

- 🔵 **INCEPTION PHASE** - Planning & Architecture(WHAT and WHY)
- 🟢 **CONSTRUCTION PHASE** - Design, Implementation & Test(HOW)
- 🟡 **OPERATIONS PHASE** - Deployment & Monitoring(향후 확장)

**Stage**: phase 안의 개별 workflow activity입니다.

- 예: Context Assessment stage, Requirements Assessment stage, Code Generation stage
- 각 stage에는 특정 prerequisites, steps, outputs가 있습니다.
- stage는 ALWAYS-EXECUTE 또는 CONDITIONAL일 수 있습니다.

**Usage Examples**:

- ✅ "CONSTRUCTION phase에는 7개 stage가 있습니다."
- ✅ "Code Generation stage는 항상 실행됩니다."
- ✅ "우리는 INCEPTION phase에서 Requirements Assessment stage를 실행 중입니다."
- ❌ "Requirements Assessment phase"(올바른 표현은 "stage")
- ❌ "CONSTRUCTION stage"(올바른 표현은 "phase")

## Three-Phase Lifecycle

### INCEPTION PHASE

**Purpose**: 계획 및 architectural decisions
**Focus**: 무엇을 만들고 왜 만드는지 결정
**Location**: `inception/` directory

**Stages**:

- Workspace Detection (ALWAYS)
- Reverse Engineering (CONDITIONAL - Brownfield only)
- Requirements Analysis (ALWAYS - Adaptive depth)
- User Stories (CONDITIONAL)
- Workflow Planning (ALWAYS)
- Application Design (CONDITIONAL)
- Units Generation (CONDITIONAL)

**Outputs**: Requirements, user stories, architectural decisions, unit definitions

### CONSTRUCTION PHASE

**Purpose**: 상세 설계 및 구현
**Focus**: 어떻게 만들지 결정
**Location**: `construction/` directory

**Stages**:

- Functional Design (CONDITIONAL, per-unit)
- NFR Requirements (CONDITIONAL, per-unit)
- NFR Design (CONDITIONAL, per-unit)
- Infrastructure Design (CONDITIONAL, per-unit)
- Code Generation (ALWAYS) — Part 1: Planning과 Part 2: Generation 포함
- Build and Test (ALWAYS)

**Outputs**: Design artifacts, NFR implementations, code, tests

### OPERATIONS PHASE

**Purpose**: Deployment 및 operational readiness
**Focus**: 어떻게 DEPLOY하고 RUN할지 결정
**Location**: `operations/` directory

**Stages**:

- Operations (PLACEHOLDER)

**Outputs**: Build instructions, deployment guides, monitoring setup, verification procedures

---

## Workflow Stages

### Always-Execute Stages

- **Workspace Detection**: workspace state와 project type의 초기 분석
- **Requirements Analysis**: requirements 수집(complexity에 따라 depth가 달라짐)
- **Workflow Planning**: 어떤 phase를 실행할지에 대한 execution plan 작성
- **Code Generation**: 두 부분으로 구성된 단일 stage입니다. Part 1(Planning)은 상세 implementation plans를 만들고, Part 2(Generation)는 plan과 이전 artifacts를 바탕으로 실제 code를 생성합니다.
- **Build and Test**: 모든 unit을 build하고 comprehensive testing 실행

### Conditional Stages

- **Reverse Engineering**: 기존 코드베이스 분석(brownfield projects only)
- **User Stories**: user stories와 personas 작성(Story Planning 및 Story Generation 포함)
- **Application Design**: application components, methods, business rules, services 설계
- **Units Generation**: system을 units of work로 분해(internal planning 및 generation sub-steps, per-unit design 포함)
- **Functional Design**: technology-agnostic business logic design(per-unit)
- **NFR Requirements**: NFR 결정 및 tech stack 선택(per-unit)
- **NFR Design**: NFR patterns와 logical components 반영(per-unit)
- **Infrastructure Design**: 실제 infrastructure services로 mapping(per-unit)

## Application Design Terms

- **Component**: 특정 responsibilities를 가진 functional unit
- **Method**: 정의된 business rules를 가진 component 내부의 function 또는 operation
- **Business Rule**: method behavior와 validation을 제어하는 logic
- **Service**: components 전반의 business logic을 조율하는 orchestration layer
- **Component Dependency**: components 사이의 relationship 및 communication pattern

## Architecture Terms (Infrastructure)

### Unit of Work

개발 목적의 user stories logical grouping입니다. planning과 decomposition 중 사용하는 용어입니다.

**Usage**: "We need to decompose the system into units of work"

### Service

microservices architecture에서 independently deployable한 component입니다. 각 service는 별도의 unit of work입니다.

**Usage**: "The Payment Service handles all payment processing"

### Module

single service 또는 monolith 내부의 functionality logical grouping입니다. module은 independently deployable하지 않습니다.

**Usage**: "The authentication module within the User Service"

### Component

service 또는 module 내부의 reusable building block입니다. component는 특정 functionality를 제공하는 classes, functions, packages입니다.

**Usage**: "The EmailValidator component validates email addresses"

## Terminology Guidelines

### When to Use Each Term

**Unit of Work**:

- Units Generation stage 중
- system decomposition을 논의할 때
- planning documents 및 discussions에서
- Example: "How should we decompose this into units of work?"

**Service**:

- independently deployable components를 가리킬 때
- microservices architecture context에서
- deployment 및 infrastructure discussions에서
- Example: "The Order Service will be deployed to ECS"

**Module**:

- service 내부의 logical groupings를 가리킬 때
- monolith architecture context에서
- internal organization을 논의할 때
- Example: "The reporting module generates all reports"

**Component**:

- 특정 classes, functions, packages를 가리킬 때
- design 및 implementation discussions에서
- reusable building blocks를 논의할 때
- Example: "The DatabaseConnection component manages connections"

## Stage Terminology

### Planning vs Generation

- **Planning**: 실행을 위한 questions와 checkboxes가 포함된 plan 작성
- **Generation**: plan을 실행해 artifacts 생성

예시(이들은 별도 stage가 아니라 single stage 안의 internal sub-steps입니다):

- Story Planning → Story Generation(User Stories stage 안)
- Units Planning → Units Generation(Units Generation stage 안)
- Unit Design Planning → Unit Design Generation(per-unit design 안)
- NFR Planning → NFR Generation(NFR Requirements stage 안)
- Code Generation Part 1(Planning) → Code Generation Part 2(Generation)

### Depth Levels

- **Minimal**: 단순 변경을 위한 빠르고 집중된 실행
- **Standard**: 일반 프로젝트를 위한 standard artifacts와 보통 depth
- **Comprehensive**: complex/high-risk 프로젝트를 위한 모든 artifact와 full depth

## Artifact Types

### Plans

실행을 안내하는 checkboxes와 questions가 포함된 문서입니다.

- `aidlc-docs/plans/`에 위치
- 예: `story-generation-plan.md`, `unit-of-work-plan.md`

### Artifacts

plan 실행으로 생성되는 outputs입니다.

- 다양한 `aidlc-docs/` 하위 디렉터리에 위치
- 예: `requirements.md`, `stories.md`, `design.md`

### State Files

workflow progress와 status를 추적하는 파일입니다.

- `aidlc-state.md`: 전체 workflow state
- `audit.md`: 모든 interaction의 complete audit trail

## Common Abbreviations

- **AI-DLC**: AI-Driven Development Life Cycle
- **NFR**: Non-Functional Requirements
- **UOW**: Unit of Work
- **API**: Application Programming Interface
- **CDK**: Cloud Development Kit (AWS)
