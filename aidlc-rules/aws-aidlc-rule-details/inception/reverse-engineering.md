# Reverse Engineering

**Purpose**: 기존 codebase를 분석하고 comprehensive design artifacts를 생성합니다.

**Execute when**: Brownfield project가 감지된 경우(workspace에서 기존 code 발견)

**Skip when**: Greenfield project인 경우(기존 code 없음)

**Rerun behavior**: rerun은 workspace-detection.md가 제어합니다. 기존 reverse engineering artifacts가 발견되고 아직 current 상태이면 이를 로드하고 reverse engineering을 건너뜁니다. artifacts가 stale(codebase의 last significant modification보다 오래됨)하거나 사용자가 명시적으로 rerun을 요청하면, artifacts가 현재 code state를 반영하도록 reverse engineering을 다시 실행합니다.

## Step 1: Multi-Package Discovery

### 1.1 Scan Workspace

- 모든 packages(언급된 package만이 아님)
- config files를 통한 package relationships
- package types: Application, CDK/Infrastructure, Models, Clients, Tests

### 1.2 Understand the Business Context

- system이 전체적으로 구현하는 core business
- 모든 package의 business overview
- system에 구현된 Business Transactions 목록

### 1.3 Infrastructure Discovery

- CDK packages(CDK dependencies가 있는 package.json)
- Terraform(.tf files)
- CloudFormation(.yaml/.json templates)
- Deployment scripts

### 1.4 Build System Discovery

- Build systems: Brazil, Maven, Gradle, npm
- build-system declarations를 위한 config files
- packages 사이의 build dependencies

### 1.5 Service Architecture Discovery

- Lambda functions(handlers, triggers)
- Container services(Docker/ECS configs)
- API definitions(Smithy models, OpenAPI specs)
- Data stores(DynamoDB, S3 등)

### 1.6 Code Quality Analysis

- Programming languages and frameworks
- Test coverage indicators
- Linting configurations
- CI/CD pipelines

## Step 2: Generate Business Overview Documentation

`aidlc-docs/inception/reverse-engineering/business-overview.md`를 생성합니다.

```markdown
# Business Overview

## Business Context Diagram
[Business Context를 보여주는 Mermaid diagram]

## Business Description
- **Business Description**: [system이 수행하는 전체 Business description]
- **Business Transactions**: [system이 구현하는 Business Transactions 목록과 설명]
- **Business Dictionary**: [system이 따르는 Business dictionary terms와 의미]

## Component Level Business Descriptions
### [Package/Component Name]
- **Purpose**: [business 관점에서 수행하는 일]
- **Responsibilities**: [주요 responsibilities]
```

## Step 3: Generate Architecture Documentation

`aidlc-docs/inception/reverse-engineering/architecture.md`를 생성합니다.

```markdown
# System Architecture

## System Overview
[system의 high-level description]

## Architecture Diagram
[모든 packages, services, data stores, relationships를 보여주는 Mermaid diagram]

## Component Descriptions
### [Package/Component Name]
- **Purpose**: [수행하는 일]
- **Responsibilities**: [주요 responsibilities]
- **Dependencies**: [의존 대상]
- **Type**: [Application/Infrastructure/Model/Client/Test]

## Data Flow
[주요 workflows의 Mermaid sequence diagram]

## Integration Points
- **External APIs**: [목적이 포함된 목록]
- **Databases**: [목적이 포함된 목록]
- **Third-party Services**: [목적이 포함된 목록]

## Infrastructure Components
- **CDK Stacks**: [목적이 포함된 목록]
- **Deployment Model**: [설명]
- **Networking**: [VPC, subnets, security groups]
```

## Step 4: Generate Code Structure Documentation

`aidlc-docs/inception/reverse-engineering/code-structure.md`를 생성합니다.

```markdown
# Code Structure

## Build System
- **Type**: [Maven/Gradle/npm/Brazil]
- **Configuration**: [주요 build files 및 settings]

## Key Classes/Modules
[Mermaid class diagram 또는 module hierarchy]

### Existing Files Inventory
[모든 source files와 각 purpose를 나열합니다. 이 파일들은 brownfield projects에서 modification candidates입니다.]

**Example format**:
- `[path/to/file]` - [Purpose/responsibility]

## Design Patterns
### [Pattern Name]
- **Location**: [사용 위치]
- **Purpose**: [사용 이유]
- **Implementation**: [구현 방식]

## Critical Dependencies
### [Dependency Name]
- **Version**: [Version number]
- **Usage**: [사용 방식과 위치]
- **Purpose**: [필요한 이유]
```

## Step 5: Generate API Documentation

`aidlc-docs/inception/reverse-engineering/api-documentation.md`를 생성합니다.

```markdown
# API Documentation

## REST APIs
### [Endpoint Name]
- **Method**: [GET/POST/PUT/DELETE]
- **Path**: [/api/path]
- **Purpose**: [수행하는 일]
- **Request**: [Request format]
- **Response**: [Response format]

## Internal APIs
### [Interface/Class Name]
- **Methods**: [signatures가 포함된 목록]
- **Parameters**: [Parameter descriptions]
- **Return Types**: [Return type descriptions]

## Data Models
### [Model Name]
- **Fields**: [Field descriptions]
- **Relationships**: [Related models]
- **Validation**: [Validation rules]
```

## Step 6: Generate Component Inventory

`aidlc-docs/inception/reverse-engineering/component-inventory.md`를 생성합니다.

```markdown
# Component Inventory

## Application Packages
- [Package name] - [Purpose]

## Infrastructure Packages
- [Package name] - [CDK/Terraform] - [Purpose]

## Shared Packages
- [Package name] - [Models/Utilities/Clients] - [Purpose]

## Test Packages
- [Package name] - [Integration/Load/Unit] - [Purpose]

## Total Count
- **Total Packages**: [Number]
- **Application**: [Number]
- **Infrastructure**: [Number]
- **Shared**: [Number]
- **Test**: [Number]
```

## Step 7: Generate Technology Stack Documentation

`aidlc-docs/inception/reverse-engineering/technology-stack.md`를 생성합니다.

```markdown
# Technology Stack

## Programming Languages
- [Language] - [Version] - [Usage]

## Frameworks
- [Framework] - [Version] - [Purpose]

## Infrastructure
- [Service] - [Purpose]

## Build Tools
- [Tool] - [Version] - [Purpose]

## Testing Tools
- [Tool] - [Version] - [Purpose]
```

## Step 8: Generate Dependencies Documentation

`aidlc-docs/inception/reverse-engineering/dependencies.md`를 생성합니다.

```markdown
# Dependencies

## Internal Dependencies
[package dependencies를 보여주는 Mermaid diagram]

### [Package A] depends on [Package B]
- **Type**: [Compile/Runtime/Test]
- **Reason**: [dependency가 존재하는 이유]

## External Dependencies
### [Dependency Name]
- **Version**: [Version]
- **Purpose**: [사용 이유]
- **License**: [License type]
```

## Step 9: Generate Code Quality Assessment

`aidlc-docs/inception/reverse-engineering/code-quality-assessment.md`를 생성합니다.

```markdown
# Code Quality Assessment

## Test Coverage
- **Overall**: [Percentage 또는 Good/Fair/Poor/None]
- **Unit Tests**: [Status]
- **Integration Tests**: [Status]

## Code Quality Indicators
- **Linting**: [Configured/Not configured]
- **Code Style**: [Consistent/Inconsistent]
- **Documentation**: [Good/Fair/Poor]

## Technical Debt
- [Issue description and location]

## Patterns and Anti-patterns
- **Good Patterns**: [List]
- **Anti-patterns**: [List with locations]
```

## Step 10: Create Timestamp File

`aidlc-docs/inception/reverse-engineering/reverse-engineering-timestamp.md`를 생성합니다.

```markdown
# Reverse Engineering Metadata

**Analysis Date**: [ISO timestamp]
**Analyzer**: AI-DLC
**Workspace**: [Workspace path]
**Total Files Analyzed**: [Number]

## Artifacts Generated
- [x] architecture.md
- [x] code-structure.md
- [x] api-documentation.md
- [x] component-inventory.md
- [x] technology-stack.md
- [x] dependencies.md
- [x] code-quality-assessment.md
```

## Step 11: Update State Tracking

`aidlc-docs/aidlc-state.md`를 업데이트합니다.

```markdown
## Reverse Engineering Status
- [x] Reverse Engineering - Completed on [timestamp]
- **Artifacts Location**: aidlc-docs/inception/reverse-engineering/
```

## Step 12: Present Completion Message to User

```markdown
# 🔍 Reverse Engineering Complete

[analysis에서 얻은 key findings를 bullet points 형태로 AI가 생성한 summary]

> **📋 <u>**REVIEW REQUIRED:**</u>**
> reverse engineering artifacts를 검토해 주세요: `aidlc-docs/inception/reverse-engineering/`

> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - 필요하다면 reverse engineering analysis 수정을 요청하세요
> ✅ **Approve & Continue** - analysis를 승인하고 **Requirements Analysis**로 진행합니다
```

## Step 13: Wait for User Approval

- **MANDATORY**: 사용자가 명시적으로 승인할 때까지 진행하지 마세요.
- **MANDATORY**: 사용자 응답을 complete raw input으로 audit.md에 기록합니다.
