# Reverse Engineering

**목적**: 기존 코드베이스를 분석해 종합적인 설계 아티팩트를 생성합니다.

**실행 시점**: Brownfield 프로젝트가 감지된 경우(워크스페이스에서 기존 코드 발견)

**건너뛰는 시점**: Greenfield 프로젝트(기존 코드 없음)

**재실행 동작**: 재실행은 workspace-detection.md가 통제합니다. 기존 리버스 엔지니어링 아티팩트가 있고 여전히 최신이면, 그것을 로드하고 리버스 엔지니어링을 건너뜁니다. 아티팩트가 오래되었거나(코드베이스의 마지막 의미 있는 수정보다 오래됨) 사용자가 명시적으로 재실행을 요청하면, 현재 코드 상태를 반영하도록 리버스 엔지니어링을 다시 실행합니다.

## Step 1: 다중 패키지 디스커버리

### 1.1 워크스페이스 스캔
- 모든 패키지 (언급된 패키지뿐 아니라)
- 설정 파일을 통한 패키지 관계
- 패키지 유형: Application, CDK/Infrastructure, Models, Clients, Tests

### 1.2 비즈니스 컨텍스트 이해
- 시스템이 전체적으로 구현하는 핵심 비즈니스
- 각 패키지의 비즈니스 개요
- 시스템에 구현된 비즈니스 트랜잭션 목록

### 1.3 인프라 디스커버리
- CDK 패키지 (CDK 의존성이 있는 package.json)
- Terraform (.tf 파일)
- CloudFormation (.yaml/.json 템플릿)
- 배포 스크립트

### 1.4 빌드 시스템 디스커버리
- 빌드 시스템: Brazil, Maven, Gradle, npm
- 빌드 시스템 선언을 위한 설정 파일
- 패키지 간 빌드 의존성

### 1.5 서비스 아키텍처 디스커버리
- Lambda 함수 (핸들러, 트리거)
- 컨테이너 서비스 (Docker/ECS 설정)
- API 정의 (Smithy 모델, OpenAPI 스펙)
- 데이터 스토어 (DynamoDB, S3 등)

### 1.6 코드 품질 분석
- 프로그래밍 언어와 프레임워크
- 테스트 커버리지 지표
- 린팅 설정
- CI/CD 파이프라인

## Step 2: 비즈니스 개요 문서 생성

`aidlc-docs/inception/reverse-engineering/business-overview.md` 생성:

```markdown
# Business Overview

## Business Context Diagram
[비즈니스 컨텍스트를 보여주는 Mermaid 다이어그램]

## Business Description
- **Business Description**: [시스템이 수행하는 비즈니스 전반 설명]
- **Business Transactions**: [시스템이 구현하는 비즈니스 트랜잭션 목록과 설명]
- **Business Dictionary**: [시스템이 따르는 비즈니스 사전 용어와 의미]

## Component Level Business Descriptions
### [Package/Component Name]
- **Purpose**: [비즈니스 관점에서 무엇을 하는지]
- **Responsibilities**: [핵심 책임]
```

## Step 3: 아키텍처 문서 생성

`aidlc-docs/inception/reverse-engineering/architecture.md` 생성:

```markdown
# System Architecture

## System Overview
[시스템에 대한 상위 설명]

## Architecture Diagram
[모든 패키지/서비스/데이터스토어/관계를 보여주는 Mermaid 다이어그램]

## Component Descriptions
### [Package/Component Name]
- **Purpose**: [무엇을 하는지]
- **Responsibilities**: [핵심 책임]
- **Dependencies**: [의존 대상]
- **Type**: [Application/Infrastructure/Model/Client/Test]

## Data Flow
[핵심 워크플로우의 Mermaid 시퀀스 다이어그램]

## Integration Points
- **External APIs**: [목록과 목적]
- **Databases**: [목록과 목적]
- **Third-party Services**: [목록과 목적]

## Infrastructure Components
- **CDK Stacks**: [목록과 목적]
- **Deployment Model**: [설명]
- **Networking**: [VPC, 서브넷, 보안 그룹]
```

## Step 4: 코드 구조 문서 생성

`aidlc-docs/inception/reverse-engineering/code-structure.md` 생성:

```markdown
# Code Structure

## Build System
- **Type**: [Maven/Gradle/npm/Brazil]
- **Configuration**: [주요 빌드 파일과 설정]

## Key Classes/Modules
[Mermaid 클래스 다이어그램 또는 모듈 계층]

### Existing Files Inventory
[모든 소스 파일과 그 목적 — brownfield 프로젝트에서 수정 후보가 됩니다]

**Example format**:
- `[path/to/file]` - [목적/책임]

## Design Patterns
### [Pattern Name]
- **Location**: [사용된 위치]
- **Purpose**: [사용 이유]
- **Implementation**: [구현 방식]

## Critical Dependencies
### [Dependency Name]
- **Version**: [버전 번호]
- **Usage**: [어디서 어떻게 사용]
- **Purpose**: [왜 필요한지]
```

## Step 5: API 문서 생성

`aidlc-docs/inception/reverse-engineering/api-documentation.md` 생성:

```markdown
# API Documentation

## REST APIs
### [Endpoint Name]
- **Method**: [GET/POST/PUT/DELETE]
- **Path**: [/api/path]
- **Purpose**: [무엇을 하는지]
- **Request**: [요청 포맷]
- **Response**: [응답 포맷]

## Internal APIs
### [Interface/Class Name]
- **Methods**: [시그니처 목록]
- **Parameters**: [파라미터 설명]
- **Return Types**: [반환 타입 설명]

## Data Models
### [Model Name]
- **Fields**: [필드 설명]
- **Relationships**: [관련 모델]
- **Validation**: [검증 룰]
```

## Step 6: 컴포넌트 인벤토리 생성

`aidlc-docs/inception/reverse-engineering/component-inventory.md` 생성:

```markdown
# Component Inventory

## Application Packages
- [패키지명] - [목적]

## Infrastructure Packages
- [패키지명] - [CDK/Terraform] - [목적]

## Shared Packages
- [패키지명] - [Models/Utilities/Clients] - [목적]

## Test Packages
- [패키지명] - [Integration/Load/Unit] - [목적]

## Total Count
- **Total Packages**: [개수]
- **Application**: [개수]
- **Infrastructure**: [개수]
- **Shared**: [개수]
- **Test**: [개수]
```

## Step 7: 기술 스택 문서 생성

`aidlc-docs/inception/reverse-engineering/technology-stack.md` 생성:

```markdown
# Technology Stack

## Programming Languages
- [언어] - [버전] - [용도]

## Frameworks
- [프레임워크] - [버전] - [목적]

## Infrastructure
- [서비스] - [목적]

## Build Tools
- [도구] - [버전] - [목적]

## Testing Tools
- [도구] - [버전] - [목적]
```

## Step 8: 의존성 문서 생성

`aidlc-docs/inception/reverse-engineering/dependencies.md` 생성:

```markdown
# Dependencies

## Internal Dependencies
[패키지 의존성을 보여주는 Mermaid 다이어그램]

### [Package A] depends on [Package B]
- **Type**: [Compile/Runtime/Test]
- **Reason**: [의존성이 존재하는 이유]

## External Dependencies
### [Dependency Name]
- **Version**: [버전]
- **Purpose**: [사용 이유]
- **License**: [라이선스 유형]
```

## Step 9: 코드 품질 평가 생성

`aidlc-docs/inception/reverse-engineering/code-quality-assessment.md` 생성:

```markdown
# Code Quality Assessment

## Test Coverage
- **Overall**: [백분율 또는 Good/Fair/Poor/None]
- **Unit Tests**: [상태]
- **Integration Tests**: [상태]

## Code Quality Indicators
- **Linting**: [Configured/Not configured]
- **Code Style**: [Consistent/Inconsistent]
- **Documentation**: [Good/Fair/Poor]

## Technical Debt
- [이슈 설명과 위치]

## Patterns and Anti-patterns
- **Good Patterns**: [목록]
- **Anti-patterns**: [위치와 함께 목록]
```

## Step 10: 타임스탬프 파일 생성

`aidlc-docs/inception/reverse-engineering/reverse-engineering-timestamp.md` 생성:

```markdown
# Reverse Engineering Metadata

**Analysis Date**: [ISO timestamp]
**Analyzer**: AI-DLC
**Workspace**: [Workspace path]
**Total Files Analyzed**: [개수]

## Artifacts Generated
- [x] architecture.md
- [x] code-structure.md
- [x] api-documentation.md
- [x] component-inventory.md
- [x] technology-stack.md
- [x] dependencies.md
- [x] code-quality-assessment.md
```

## Step 11: 상태 추적 업데이트

`aidlc-docs/aidlc-state.md` 업데이트:

```markdown
## Reverse Engineering Status
- [x] Reverse Engineering - Completed on [timestamp]
- **Artifacts Location**: aidlc-docs/inception/reverse-engineering/
```

## Step 12: 사용자에게 완료 메시지 제시

```markdown
# 🔍 Reverse Engineering Complete

[AI가 생성한 분석의 핵심 발견사항을 불릿으로 요약]

> **📋 <u>**REVIEW REQUIRED:**</u>**  
> Please examine the reverse engineering artifacts at: `aidlc-docs/inception/reverse-engineering/`

> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - Ask for modifications to the reverse engineering analysis if required
> ✅ **Approve & Continue** - Approve analysis and proceed to **Requirements Analysis**
```

## Step 13: 사용자 승인 대기

- **MANDATORY**: 사용자가 명시적으로 승인할 때까지 진행하지 마세요.
- **MANDATORY**: 사용자 응답을 완전한 원본 입력 그대로 audit.md에 로깅하세요.
