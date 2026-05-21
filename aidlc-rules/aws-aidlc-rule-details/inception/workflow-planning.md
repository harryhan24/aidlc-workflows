# Workflow Planning

**Purpose**: 실행할 phases를 결정하고 comprehensive execution plan을 작성합니다.

**Always Execute**: 이 phase는 requirements와 scope를 이해한 뒤 항상 실행합니다.

## Step 1: Load All Prior Context

### 1.1 Load Reverse Engineering Artifacts (if brownfield)

- architecture.md
- component-inventory.md
- technology-stack.md
- dependencies.md

### 1.2 Load Requirements Analysis

- requirements.md(intent analysis 포함)
- requirement-verification-questions.md(answers 포함)

### 1.3 Load User Stories (if executed)

- stories.md
- personas.md

## Step 2: Detailed Scope and Impact Analysis

**complete context(requirements + stories)를 확보했으므로 detailed analysis를 수행합니다.**

### 2.1 Transformation Scope Detection (Brownfield Only)

**IF brownfield project**, transformation scope를 분석합니다.

#### Architectural Transformation

- **Single component change** vs **architectural transformation**
- **Infrastructure changes** vs **application changes**
- **Deployment model changes**(Lambda→Container, EC2→Serverless 등)

#### Related Component Identification

transformation의 경우 다음을 식별합니다.

- update가 필요한 **Infrastructure code**
- 변경이 필요한 **CDK stacks**
- **API Gateway** configurations
- **Load balancer** requirements
- 필요한 **Networking** changes
- **Monitoring/logging** adaptations

#### Cross-Package Impact

- update가 필요한 **CDK infrastructure** packages
- version updates가 필요한 **Shared models**
- endpoint changes가 필요한 **Client libraries**
- 새 test scenarios가 필요한 **Test packages**

### 2.2 Change Impact Assessment

#### Impact Areas

1. **User-facing changes**: user experience에 영향을 주는가?
2. **Structural changes**: system architecture를 변경하는가?
3. **Data model changes**: database schemas 또는 data structures에 영향을 주는가?
4. **API changes**: interfaces 또는 contracts에 영향을 주는가?
5. **NFR impact**: performance, security, scalability에 영향을 주는가?

#### Application Layer Impact (if applicable)

- **Code changes**: new entry points, adapters, configurations
- **Dependencies**: new libraries, framework changes
- **Configuration**: environment variables, config files
- **Testing**: unit tests, integration tests

#### Infrastructure Layer Impact (if applicable)

- **Deployment model**: Lambda→ECS, EC2→Fargate 등
- **Networking**: VPC, security groups, load balancers
- **Storage**: persistent volumes, shared storage
- **Scaling**: auto-scaling policies, capacity planning

#### Operations Layer Impact (if applicable)

- **Monitoring**: CloudWatch, custom metrics, dashboards
- **Logging**: log aggregation, structured logging
- **Alerting**: alarm configurations, notification channels
- **Deployment**: CI/CD pipeline changes, rollback strategies

### 2.3 Component Relationship Mapping (Brownfield Only)

**IF brownfield project**, component dependency graph를 생성합니다.

```markdown
## Component Relationships
- **Primary Component**: [Package being changed]
- **Infrastructure Components**: [CDK/Terraform packages]
- **Shared Components**: [Models, utilities, clients]
- **Dependent Components**: [Services that call this component]
- **Supporting Components**: [Monitoring, logging, deployment]
```

각 related component에 대해 다음을 식별합니다.

- **Change Type**: Major, Minor, Configuration-only
- **Change Reason**: Direct dependency, deployment model, networking
- **Change Priority**: Critical, Important, Optional

### 2.4 Risk Assessment

risk level을 평가합니다.

1. **Low**: isolated change, easy rollback, well-understood
2. **Medium**: multiple components, moderate rollback, some unknowns
3. **High**: system-wide impact, complex rollback, significant unknowns
4. **Critical**: production-critical, difficult rollback, high uncertainty

## Step 3: Phase Determination

### 3.1 User Stories - Already Executed or Skip?

**Already executed**: 다음 determination으로 이동합니다.
**Not executed - Execute IF**:

- multiple user personas
- user experience impact
- acceptance criteria needed
- team collaboration required

**Skip IF**:

- internal refactoring
- clear reproduction이 있는 bug fix
- technical debt reduction
- infrastructure changes

### 3.2 Application Design - Execute IF:

- 새로운 components 또는 services 필요
- component methods와 business rules 정의 필요
- service layer design 필요
- component dependencies clarification 필요

**Skip IF**:

- 기존 component boundaries 안의 변경
- 새로운 components 또는 methods 없음
- pure implementation changes

### 3.3 Units Generation - Execute IF:

- 새로운 data models 또는 schemas
- API changes 또는 new endpoints
- complex algorithms 또는 business logic
- state management changes
- multiple packages require changes
- infrastructure-as-code updates 필요

**Skip IF**:

- simple logic changes
- UI-only changes
- configuration updates
- straightforward implementations

### 3.4 NFR Implementation - Execute IF:

- performance requirements
- security considerations
- scalability concerns
- monitoring/observability 필요

**Skip IF**:

- 기존 NFR setup이 충분함
- new NFR requirements 없음
- NFR impact가 없는 simple changes

## Step 4: Note Adaptive Detail

**adaptive depth 설명은 [depth-levels.md](../common/depth-levels.md)를 참조하세요.**

실행할 각 stage에 대해:

- 정의된 모든 artifacts가 생성됩니다.
- artifacts 안의 detail level은 problem complexity에 맞게 조정됩니다.
- model은 problem characteristics를 바탕으로 적절한 detail을 결정합니다.

## Step 5: Multi-Module Coordination Analysis (Brownfield Only)

**IF brownfield with multiple modules/packages**, dependencies를 분석하고 optimal update strategy를 결정합니다.

### 5.1 Analyze Module Dependencies

- build system dependencies 및 dependency manifests 검토
- build-time vs runtime dependencies 식별
- modules 사이의 API contracts 및 shared interfaces mapping

### 5.2 Determine Update Strategy

dependency analysis를 바탕으로 다음을 결정합니다.

- **Update sequence**: dependencies 때문에 먼저 업데이트해야 하는 modules
- **Parallelization opportunities**: 동시에 업데이트할 수 있는 modules
- **Coordination requirements**: version compatibility, API contracts, deployment order
- **Testing strategy**: per-module vs integrated testing approach
- **Rollback strategy**: mid-sequence failures가 발생할 경우 recovery plan

### 5.3 Document Coordination Plan

```markdown
## Module Update Strategy
- **Update Approach**: [Sequential/Parallel/Hybrid]
- **Critical Path**: [Modules that block other updates]
- **Coordination Points**: [Shared APIs, infrastructure, data contracts]
- **Testing Checkpoints**: [When to validate integration]
```

영향을 받는 각 module에 대해 다음을 식별합니다.

- **Update priority**: Must-update-first vs can-update-later
- **Dependency constraints**: 무엇에 의존하는지, 무엇이 이 module에 의존하는지
- **Change scope**: Major(breaking), Minor(compatible), Patch(fixes)

## Step 6: Generate Workflow Visualization

다음을 보여주는 Mermaid flowchart를 생성합니다.

- sequence 안의 모든 phases
- 각 conditional phase에 대한 EXECUTE 또는 SKIP decision
- 각 phase state에 맞는 styling

**Styling rules**(flowchart 뒤에 추가):

```text
style WD fill:#4CAF50,stroke:#1B5E20,stroke-width:3px,color:#fff
style CG fill:#4CAF50,stroke:#1B5E20,stroke-width:3px,color:#fff
style BT fill:#4CAF50,stroke:#1B5E20,stroke-width:3px,color:#fff
style US fill:#BDBDBD,stroke:#424242,stroke-width:2px,stroke-dasharray: 5 5,color:#000
style Start fill:#CE93D8,stroke:#6A1B9A,stroke-width:3px,color:#000
style End fill:#CE93D8,stroke:#6A1B9A,stroke-width:3px,color:#000

linkStyle default stroke:#333,stroke-width:2px
```

**Style Guidelines**:

- Completed/Always execute: `fill:#4CAF50,stroke:#1B5E20,stroke-width:3px,color:#fff`(Material Green with white text)
- Conditional EXECUTE: `fill:#FFA726,stroke:#E65100,stroke-width:3px,stroke-dasharray: 5 5,color:#000`(Material Orange with black text)
- Conditional SKIP: `fill:#BDBDBD,stroke:#424242,stroke-width:2px,stroke-dasharray: 5 5,color:#000`(Material Gray with black text)
- Start/End: `fill:#CE93D8,stroke:#6A1B9A,stroke-width:3px,color:#000`(Material Purple with black text)
- Phase containers: 더 밝은 Material colors 사용(INCEPTION: #BBDEFB, CONSTRUCTION: #C8E6C9, OPERATIONS: #FFF59D)

## Step 7: Create Execution Plan Document

`aidlc-docs/inception/plans/execution-plan.md`를 생성합니다.

````markdown
# Execution Plan

## Detailed Analysis Summary

### Transformation Scope (Brownfield Only)
- **Transformation Type**: [Single component/Architectural/Infrastructure]
- **Primary Changes**: [Description]
- **Related Components**: [List]

### Change Impact Assessment
- **User-facing changes**: [Yes/No - Description]
- **Structural changes**: [Yes/No - Description]
- **Data model changes**: [Yes/No - Description]
- **API changes**: [Yes/No - Description]
- **NFR impact**: [Yes/No - Description]

### Component Relationships (Brownfield Only)
[Component dependency graph]

### Risk Assessment
- **Risk Level**: [Low/Medium/High/Critical]
- **Rollback Complexity**: [Easy/Moderate/Difficult]
- **Testing Complexity**: [Simple/Moderate/Complex]

## Workflow Visualization

```mermaid
flowchart TD
    Start(["사용자 요청"])

    subgraph INCEPTION["🔵 INCEPTION PHASE"]
        WD["Workspace Detection<br/><b>STATUS</b>"]
        RE["Reverse Engineering<br/><b>STATUS</b>"]
        RA["Requirements Analysis<br/><b>STATUS</b>"]
        US["User Stories<br/><b>STATUS</b>"]
        WP["Workflow Planning<br/><b>STATUS</b>"]
        AD["Application Design<br/><b>STATUS</b>"]
        UG["Units Generation<br/>(Planning + Generation)<br/><b>STATUS</b>"]
    end

    subgraph CONSTRUCTION["🟢 CONSTRUCTION PHASE"]
        FD["Functional Design<br/><b>STATUS</b>"]
        NFRA["NFR Requirements<br/><b>STATUS</b>"]
        NFRD["NFR Design<br/><b>STATUS</b>"]
        ID["Infrastructure Design<br/><b>STATUS</b>"]
        CG["Code Generation<br/>(Planning + Generation)<br/><b>EXECUTE</b>"]
        BT["Build and Test<br/><b>EXECUTE</b>"]
    end

    subgraph OPERATIONS["🟡 OPERATIONS PHASE"]
        OPS["Operations<br/><b>PLACEHOLDER</b>"]
    end

    Start --> WD
    WD --> RA
    RA --> WP
    WP --> CG
    CG --> BT
    BT --> End(["완료"])

    %% STATUS를 실제 상태(COMPLETED, SKIP, EXECUTE)로 바꾸세요
    %% status에 맞는 styling을 적용하세요
```

**Note**: STATUS placeholders를 실제 phase status(COMPLETED/SKIP/EXECUTE)로 바꾸고 적절한 styling을 적용하세요.

## Phases to Execute

### 🔵 INCEPTION PHASE
- [x] Workspace Detection (COMPLETED)
- [x] Reverse Engineering (COMPLETED/SKIPPED)
- [x] Requirements Analysis (COMPLETED)
- [x] User Stories (COMPLETED/SKIPPED)
- [x] Execution Plan (IN PROGRESS)
- [ ] Application Design - [EXECUTE/SKIP]
  - **Rationale**: [실행 또는 skip 이유]
- [ ] Units Generation - [EXECUTE/SKIP]
  - **Rationale**: [실행 또는 skip 이유]

### 🟢 CONSTRUCTION PHASE
- [ ] Functional Design - [EXECUTE/SKIP]
  - **Rationale**: [실행 또는 skip 이유]
- [ ] NFR Requirements - [EXECUTE/SKIP]
  - **Rationale**: [실행 또는 skip 이유]
- [ ] NFR Design - [EXECUTE/SKIP]
  - **Rationale**: [실행 또는 skip 이유]
- [ ] Infrastructure Design - [EXECUTE/SKIP]
  - **Rationale**: [실행 또는 skip 이유]
- [ ] Code Generation - EXECUTE (ALWAYS)
  - **Rationale**: Implementation planning 및 code generation 필요
- [ ] Build and Test - EXECUTE (ALWAYS)
  - **Rationale**: Build, test, verification 필요

### 🟡 OPERATIONS PHASE
- [ ] Operations - PLACEHOLDER
  - **Rationale**: 향후 deployment 및 monitoring workflows

## Package Change Sequence (Brownfield Only)
[해당되는 경우 dependencies가 포함된 package update sequence를 나열]

## Estimated Timeline
- **Total Phases**: [Number]
- **Estimated Duration**: [Time estimate]

## Success Criteria
- **Primary Goal**: [Main objective]
- **Key Deliverables**: [List]
- **Quality Gates**: [List]

[IF brownfield]
- **Integration Testing**: 모든 components가 함께 동작함
- **Operational Readiness**: monitoring, logging, alerting이 동작함
````

## Step 8: Initialize State Tracking

`aidlc-docs/aidlc-state.md`를 업데이트합니다.

```markdown
# AI-DLC State Tracking

## Project Information
- **Project Type**: [Greenfield/Brownfield]
- **Start Date**: [ISO timestamp]
- **Current Stage**: INCEPTION - Workflow Planning

## Execution Plan Summary
- **Total Stages**: [Number]
- **Stages to Execute**: [List]
- **Stages to Skip**: [List with reasons]

## Stage Progress

### 🔵 INCEPTION PHASE
- [x] Workspace Detection
- [x] Reverse Engineering (if applicable)
- [x] Requirements Analysis
- [x] User Stories (if applicable)
- [x] Workflow Planning
- [ ] Application Design - [EXECUTE/SKIP]
- [ ] Units Generation - [EXECUTE/SKIP]

### 🟢 CONSTRUCTION PHASE
- [ ] Functional Design - [EXECUTE/SKIP]
- [ ] NFR Requirements - [EXECUTE/SKIP]
- [ ] NFR Design - [EXECUTE/SKIP]
- [ ] Infrastructure Design - [EXECUTE/SKIP]
- [ ] Code Generation - EXECUTE
- [ ] Build and Test - EXECUTE

### 🟡 OPERATIONS PHASE
- [ ] Operations - PLACEHOLDER

## Current Status
- **Lifecycle Phase**: INCEPTION
- **Current Stage**: Workflow Planning Complete
- **Next Stage**: [Next stage to execute]
- **Status**: Ready to proceed
```

## Step 9: Present Plan to User

```markdown
# 📋 Workflow Planning Complete

다음을 바탕으로 comprehensive execution plan을 만들었습니다.
- Your request: [Summary]
- Existing system: [Summary if brownfield]
- Requirements: [Summary if executed]
- User stories: [Summary if executed]

**Detailed Analysis**:
- Risk level: [Level]
- Impact: [Summary of key impacts]
- Components affected: [List]

**Recommended Execution Plan**:

[X]개 stages 실행을 권장합니다.

🔵 **INCEPTION PHASE:**
1. [Stage name] - *Rationale:* [실행 이유]
2. [Stage name] - *Rationale:* [실행 이유]
...

🟢 **CONSTRUCTION PHASE:**
3. [Stage name] - *Rationale:* [실행 이유]
4. [Stage name] - *Rationale:* [실행 이유]
...

[Y]개 stages skip을 권장합니다.

🔵 **INCEPTION PHASE:**
1. [Stage name] - *Rationale:* [skip 이유]
2. [Stage name] - *Rationale:* [skip 이유]
...

🟢 **CONSTRUCTION PHASE:**
3. [Stage name] - *Rationale:* [skip 이유]
4. [Stage name] - *Rationale:* [skip 이유]
...

[IF brownfield with multiple packages]
**Recommended Package Update Sequence**:
1. [Package] - [Reason]
2. [Package] - [Reason]
...

**Estimated Timeline**: [Duration]

> **📋 <u>**REVIEW REQUIRED:**</u>**
> execution plan을 검토해 주세요: `aidlc-docs/inception/plans/execution-plan.md`

> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - 필요하다면 execution plan 수정을 요청하세요
> [IF any stages are skipped:]
> 📝 **Add Skipped Stages** - 현재 SKIP으로 표시된 stages를 포함하도록 선택합니다
> ✅ **Approve & Continue** - plan을 승인하고 **[Next Stage Name]**으로 진행합니다
```

## Step 10: Handle User Response

- **If approved**: execution plan의 next stage로 진행합니다.
- **If changes requested**: execution plan을 업데이트하고 다시 확인합니다.
- **If user wants to force include/exclude stages**: 그에 맞게 plan을 업데이트합니다.

## Step 11: Log Interaction

`aidlc-docs/audit.md`에 기록합니다.

```markdown
## Workflow Planning - Approval
**Timestamp**: [ISO timestamp]
**AI Prompt**: "Ready to proceed with this plan?"
**User Response**: "[User's COMPLETE RAW response]"
**Status**: [Approved/Changes Requested]
**Context**: Workflow plan created with [X] stages to execute

---
```
