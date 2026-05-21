# Technical Environment Document 작성 가이드

## 목적

Technical Environment Document는 프로젝트가 어떻게 빌드되는지를 지배하는 **기술 도구, 표준, 제약, 선호 사항**을 정의합니다. Vision Document의 기술적 대응물이며 AI-DLC의 Construction 단계에서 구속력 있는 레퍼런스 역할을 합니다.

이 문서는 코드 생성, 인프라 설계, NFR 결정이 조직 표준, 보안 정책, 팀 역량과 정렬되도록 보장합니다. 이 문서가 없으면 AI-DLC 스테이지는 이러한 간극을 채우기 위해 광범위한 명확화 질문을 던지거나, 더 나쁜 경우 재작업이 필요한 가정을 합니다.

## 언제 Technical Environment Document를 작성하는가

- 새 프로젝트(그린필드)를 시작하기 전
- 기술적 제약이 변경된 기존 프로젝트(브라운필드)를 수정하기 전
- 조직 기술 표준이 업데이트되었을 때
- 클라우드 제공자, 프레임워크, 배포 모델 사이를 마이그레이션할 때

## 문서 적용성

Technical Environment Document는 두 가지 프로젝트 컨텍스트 중 하나를 타겟할 수 있습니다.

| Context        | 정의                                                  | 핵심 차이                                                                                                        |
| -------------- | ----------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| **Greenfield** | 기존 코드 없음. 밑바닥부터 빌드.                      | 모든 선택이 열려 있음. 문서가 시작점을 정의.                                                                     |
| **Brownfield** | 기존 코드베이스. 추가, 수정, 마이그레이션.            | 선택이 기존에 의해 제약됨. 문서가 무엇을 유지하고, 변경하고, 피할지 정의.                                        |

적용되는 컨텍스트에 맞춰 문서를 구성하세요. 아래 섹션들은 적용 위치를 나타내기 위해 **(Greenfield)**, **(Brownfield)**, 또는 **(Both)**로 표시되어 있습니다.

---

## 문서 구조

### 1. Project Technical Summary (Both)

```markdown
## Project Technical Summary

- **Project Name**: [Name]
- **Project Type**: [Greenfield / Brownfield]
- **Primary Runtime Environment**: [Cloud / On-Premises / Hybrid]
- **Cloud Provider**: [AWS / Azure / GCP / Multi-cloud / None]
- **Target Deployment Model**: [Serverless / Containers / VMs / Hybrid]
- **Team Size**: [개발자 수]
- **Team Experience**: [기술 선택과 관련된 핵심 스킬과 경험 수준]
```

---

### 2. Programming Languages (Both)

프로젝트가 반드시 사용해야 하는, 사용해도 되는, 사용해서는 안 되는 언어를 정의하세요.

```markdown
## Programming Languages

### Required Languages
[특정 목적을 위해 반드시 사용되어야 하는 언어.]

| Language | Version | Purpose | Rationale |
|----------|---------|---------|-----------|
| TypeScript | 5.x | Backend services, CDK infrastructure | Team expertise, type safety |
| Python | 3.12+ | Data processing, Lambda functions | ML library ecosystem |

### Permitted Languages
[정당화될 경우 사용할 수 있지만 필수는 아닌 언어.]

| Language | Conditions for Use |
|----------|-------------------|
| Go | Approved for high-throughput microservices where latency is critical |
| Rust | Approved for systems-level components only with tech lead approval |

### Prohibited Languages
[사용해서는 안 되는 언어와 그 이유.]

| Language | Reason |
|----------|--------|
| PHP | No team expertise, not aligned with platform direction |
| Ruby | Organizational standard prohibits new Ruby services |
```

**브라운필드 추가:**

```markdown
### Existing Language Inventory
[코드베이스에 현재 존재하며 유지되거나 마이그레이션되어야 하는 언어.]

| Language | Current Usage | Direction |
|----------|--------------|-----------|
| Java 11 | Core backend services | Maintain (upgrade to Java 21 in Phase 2) |
| JavaScript | Legacy frontend | Migrate to TypeScript |
```

---

### 3. Frameworks and Libraries (Both)

```markdown
## Frameworks and Libraries

### Required Frameworks
[각 도메인에 반드시 사용되어야 하는 프레임워크.]

| Framework/Library | Version | Domain | Rationale |
|-------------------|---------|--------|-----------|
| React | 18.x | Frontend UI | Organizational standard |
| Express | 4.x | API layer | Lightweight, team familiarity |
| AWS CDK | 2.x | Infrastructure as Code | AWS deployment target |
| Jest | 29.x | Unit testing | Consistent test runner across projects |

### Preferred Libraries
[해당 역량이 필요할 때 사용되어야 하지만, 역량이 필요하지 않은 경우
필수는 아닌 라이브러리.]

| Library | Purpose | Use When |
|---------|---------|----------|
| Zod | Runtime type validation | Any external data ingestion or API input |
| Pino | Structured logging | All services that emit logs |
| Axios | HTTP client | Outbound HTTP calls from services |

### Prohibited Libraries
[사용해서는 안 되는 라이브러리. 선호되는 대안을 포함하세요.]

| Library | Reason | Alternative |
|---------|--------|-------------|
| Moment.js | Deprecated, large bundle size | date-fns or Luxon |
| Lodash (full) | Bundle size | Native JS or lodash-es for specific imports |
| Request | Deprecated | Axios or native fetch |

### Library Approval Process
[필수 또는 선호 리스트에 없는 라이브러리 사용 승인을 어떻게 받는가?]

- [승인 프로세스를 기술하세요. 예: "정당화, 라이선스 확인, 유지 보수
  상태 평가를 포함한 기술 리뷰 요청을 아키텍처 팀에 제출."]
```

---

### 4. Cloud Environment and Services (Both)

```markdown
## Cloud Environment

### Cloud Provider
- **Primary Provider**: [AWS / Azure / GCP]
- **Account Structure**: [Single account / Multi-account / Organization]
- **Regions**: [주 리전과 재해 복구 리전]

### Service Allow List
[사용이 승인된 서비스. 이 리스트의 서비스만 추가 승인 없이 사용할 수
있습니다.]

| Service | Approved Use Cases | Constraints |
|---------|-------------------|-------------|
| AWS Lambda | Event-driven compute, API handlers | Max 15 min timeout, 10GB memory |
| Amazon DynamoDB | Key-value and document storage | On-demand capacity for dev, provisioned for prod |
| Amazon S3 | Object storage, static assets | Must enable versioning and encryption |
| Amazon SQS | Asynchronous message queuing | Standard queues preferred; FIFO only when ordering required |
| Amazon CloudWatch | Monitoring, logging, alarms | All services must emit structured logs |
| AWS Secrets Manager | Secrets storage | All credentials and API keys |
| Amazon API Gateway | REST and HTTP API exposure | HTTP APIs preferred over REST for new services |
| Amazon ECR | Container image registry | Required for all container-based services |
| AWS ECS Fargate | Container compute | Preferred over EC2-based ECS |
| Amazon RDS PostgreSQL | Relational data storage | Aurora Serverless v2 for variable workloads |

### Service Disallow List
[사용해서는 안 되는 서비스와 그 이유, 승인된 대안.]

| Service | Reason | Alternative |
|---------|--------|-------------|
| Amazon EC2 (direct) | Prefer managed/serverless compute | Lambda or ECS Fargate |
| Amazon ElastiCache | Cost and operational overhead for current scale | DynamoDB DAX or application-level caching |
| AWS Elastic Beanstalk | Does not fit IaC workflow | CDK with ECS or Lambda |
| Amazon Kinesis | Complexity exceeds current needs | SQS or EventBridge |

### Service Approval Process
[allow list에 없는 서비스 사용 승인을 어떻게 받는가?]

- [프로세스를 기술하세요. 예: "비즈니스 정당화, 비용 추정, 보안 리뷰,
  운영 계획을 포함한 Cloud Service Request 제출. 아키텍처 팀 승인 필요."]
```

---

### 5. Preferred Technologies and Patterns (Both)

```markdown
## Preferred Technologies and Patterns

### Architecture Patterns
| Pattern | When to Use | When Not to Use |
|---------|-------------|-----------------|
| Serverless-first | Default for all new services | Workloads requiring persistent connections or >15 min processing |
| Event-driven | Asynchronous workflows, decoupled services | Simple CRUD with no downstream effects |
| Microservices | Independently deployable domains | Small projects with single-team ownership |
| Monolith (modular) | Single-team projects, early-stage MVPs | Multi-team or independently scalable domains |

### API Design Standards
- **Style**: [REST / GraphQL / gRPC] - [각각을 언제 사용하는가]
- **Versioning**: [URL path versioning (v1/v2) / Header-based]
- **Documentation**: [모든 REST API에 대해 OpenAPI 3.x 스펙 필수]
- **Naming Convention**: [URL은 kebab-case, JSON 필드는 camelCase]
- **Pagination**: [커서 기반 선호, 어드민 API에는 offset 기반 허용]
- **Error Format**: [표준 에러 응답 구조]

### Data Patterns
- **Primary Data Store**: [서비스 소유 데이터에 DynamoDB]
- **Relational Data**: [관계형 쿼리가 필요할 때 RDS PostgreSQL]
- **Caching Strategy**: [캐싱 접근 방식 기술]
- **Data Ownership**: [각 서비스가 자체 데이터 소유; 공유 데이터베이스 없음]

### Messaging and Events
- **Synchronous**: [요청-응답을 위한 서비스 간 HTTP/REST]
- **Asynchronous**: [태스크 큐잉을 위한 SQS, 이벤트 분배를 위한 EventBridge]
- **Event Schema**: [이벤트 스키마 표준 기술, 예: CloudEvents 형식]

### Frontend Patterns (해당하는 경우)
- **Component Library**: [예: 내부 디자인 시스템, Material UI, Shadcn]
- **State Management**: [예: 로컬에 React Context, 글로벌에 Zustand]
- **Routing**: [예: React Router v6]
- **Build Tool**: [예: Vite]
```

---

### 6. Security Requirements (Both)

```markdown
## Security Requirements

### Authentication and Authorization
- **Authentication Method**: [예: Amazon Cognito, OIDC, SAML]
- **Authorization Model**: [예: RBAC, ABAC, 커스텀 정책 엔진]
- **Token Format**: [예: RS256 서명을 사용하는 JWT]
- **Session Management**: [예: 토큰 만료, refresh 토큰 회전]

### Data Protection
- **Encryption at Rest**: [모든 데이터 저장소에 대해 필수. KMS 키 관리 명시.]
- **Encryption in Transit**: [모든 통신에 TLS 1.2+ 필수]
- **PII Handling**: [PII 필드 식별, 마스킹 요구사항, 보존 정책]
- **Data Classification**: [Public / Internal / Confidential / Restricted]

### Network Security
- **VPC Requirements**: [VPC에서 실행되어야 하는 서비스]
- **Security Groups**: [최소 권한 룰, 0.0.0.0/0 인그레스 금지]
- **WAF**: [모든 공개 엔드포인트에 필수]
- **Private Endpoints**: [AWS 서비스 접근에 가능한 경우 VPC endpoint 사용]

### Secrets Management
- **Secrets Storage**: [AWS Secrets Manager / Parameter Store]
- **Rotation Policy**: [N일마다 자동 회전]
- **Access Policy**: [서비스별 최소 권한 IAM 정책]
- **Prohibited Practices**:
  - 소스 코드, 빌드 시점 환경 변수, 설정 파일의 시크릿 금지
  - 서비스 간 공유 자격 증명 금지
  - 장기 액세스 키 금지

### Compliance Requirements
- **Standards**: [SOC 2, HIPAA, PCI-DSS, GDPR, FedRAMP, 또는 "특정 없음"]
- **Audit Logging**: [모든 API 호출 로깅, CloudTrail 활성화, 로그 보존 기간]
- **Vulnerability Scanning**: [컨테이너 이미지 스캐닝, 의존성 스캐닝 도구]

### Dependency Security
- **Dependency Scanning**: [도구와 빈도, 예: Dependabot 주간, Snyk on PR]
- **License Policy**: [허용: MIT, Apache 2.0, BSD. 금지: GPL, AGPL]
- **Update Policy**: [Critical 취약점은 N일 이내 패치]

### Security Compliance Framework

모든 프로젝트는 보안 위험 프레임워크를 채택하고 프로젝트가 그
프레임워크의 각 위험 카테고리를 어떻게 다루는지 문서화해야 합니다.
프레임워크 선택은 프로젝트의 도메인, 규제 환경, 조직 표준에
따라 다릅니다.

**하나 이상의 프레임워크를 선택하고 카테고리별 컴플라이언스를 문서화하세요:**

- **Framework chosen**: [이름과 버전, 예: OWASP Top 10 (2021),
  NIST 800-53, CIS Controls v8, AWS Well-Architected Security Pillar,
  SANS Top 25, 또는 내부 조직 프레임워크]
- **Rationale**: [이 프레임워크가 선택된 이유. 해당하는 경우 규제
  요건, 고객 계약, 또는 조직 정책을 참조하세요.]

**컨텍스트별 일반적인 프레임워크:**

| Context | 일반적인 프레임워크 선택 |
|---------|------------------------|
| Web applications and APIs | OWASP Top 10, OWASP API Security Top 10 |
| Cloud-native infrastructure | AWS/Azure/GCP Well-Architected Security Pillar, CIS Benchmarks |
| Government / regulated | NIST 800-53, FedRAMP, ISO 27001 |
| General software | CIS Controls v8, SANS Top 25 |
| Internal / low-risk | 조직 보안 체크리스트 (여기에 문서화) |

**선택한 프레임워크의 각 위험 카테고리에 대해 다음을 문서화하세요:**

1. **프로젝트가 이를 어떻게 다루는가** - 위험을 완화하는 구체적인
   컨트롤, 패턴, 도구
2. **Not Applicable 정당화** - 카테고리가 적용되지 않으면 명시적으로
   이유를 기술. 카테고리를 비워두지 마세요.
3. **연기된 항목** - 컨트롤이 이후 단계로 계획된 경우, 현재 간극과
   시정을 위한 타겟 단계를 문서화

**상세 컴플라이언스 매트릭스를 어디에 둘 것인가:**

작은 프레임워크(10개 이하의 카테고리)의 경우, 이 문서의 이 헤딩
아래에 전체 매트릭스를 포함하세요.

큰 프레임워크(NIST 800-53, ISO 27001)의 경우, 별도의 파일을 생성하고
여기에서 참조하세요:
- `security/[framework-name]-compliance.md`

선택된 프레임워크로 OWASP Top 10 (2021)을 사용하는 완전한 작업 예제는
CalcEngine 예제를 참고하세요.
```

---

### 7. Testing Requirements (Both)

```markdown
## Testing Requirements

### Test Strategy Overview
| Test Type | Required | Coverage Target | Tooling |
|-----------|----------|----------------|---------|
| Unit Tests | Yes | 라인 커버리지 최소 80% | Jest / pytest |
| Integration Tests | Yes | 모든 서비스 간 상호작용 | Jest + Testcontainers / pytest |
| End-to-End Tests | Conditional | 핵심 사용자 여정 | Playwright / Cypress |
| Contract Tests | Conditional | 모든 서비스 간 API | Pact |
| Performance Tests | Conditional | SLA 목표가 정의된 경우 | k6 / Artillery |
| Security Tests | Yes | 모든 공개 엔드포인트 | OWASP ZAP / Snyk |

### Unit Testing Standards
- **Coverage Minimum**: [라인 커버리지 80%, 브랜치 커버리지 70%]
- **Mocking Policy**: [외부 의존성을 모킹, 내부 비즈니스 로직은 모킹하지 않음]
- **Naming Convention**: [describe/it 패턴, 예: "describe('OrderService') > it('should calculate total with tax')"]
- **Test Location**: [소스와 함께 (예: `__tests__/`) 또는 별도 트리 (예: `tests/unit/`)]

### Integration Testing Standards
- **Scope**: [실제 서비스 상호작용, 데이터베이스 쿼리, API 계약 테스트]
- **Environment**: [Docker Compose / Testcontainers를 통한 로컬 컨테이너]
- **Data Management**: [테스트 픽스처, 데이터베이스 시딩과 정리 접근]

### End-to-End Testing Standards
- **Scope**: [핵심 사용자 여정만, 포괄적인 UI 테스트가 아님]
- **Environment**: [배포된 스테이징 환경]
- **Data-testid Requirements**: [모든 상호작용 요소는 안정적인 data-testid 속성을 가져야 함]

### Performance Testing Standards
- **Baseline Requirements**: [SLA 목표 정의: 응답 시간, 처리량, 에러율]
- **Test Scenarios**: [부하 테스트, 스트레스 테스트, 소크 테스트]
- **Tooling**: [k6 / Artillery / JMeter]

### CI/CD Testing Gates
[파이프라인의 각 단계에서 어떤 테스트가 통과해야 하는지 정의하세요.]

| Pipeline Stage | Required Tests | Failure Action |
|---------------|---------------|----------------|
| Pre-commit | Linting, type checking | Block commit |
| Pull Request | Unit tests, integration tests | Block merge |
| Pre-deploy (staging) | E2E tests, contract tests | Block deploy |
| Post-deploy (production) | Smoke tests, health checks | Auto-rollback |
```

---

### 8. Example and Template Code Guidance (Both)

이 섹션은 AI-DLC와 개발 팀에게 프로젝트 컨벤션을 정립하는 예제 또는 템플릿 코드를 어떻게 제공, 사용, 유지할지를 알려줍니다.

````markdown
## Example and Template Code Guidance

### Purpose of Example Code
예제 코드는 프로젝트의 **정규(canonical) 패턴**을 정립합니다. AI-DLC가 코드를
생성할 때, 새 패턴을 발명하기보다 이 패턴들을 따라야 합니다.
개발자가 코드를 작성할 때, 일관성을 위해 이 예제들을 참조합니다.

### When to Provide Example Code
다음에 대해 예제 또는 템플릿 코드를 제공하세요:

- **Project structure setup** - 디렉토리 레이아웃, 파일 네이밍, 모듈 조직
- **API endpoint pattern** - 라우트에서 응답까지 표준 엔드포인트가 어떻게 구조화되는지
- **Database access pattern** - 쿼리, 트랜잭션, 연결이 어떻게 처리되는지
- **Error handling pattern** - 표준 에러 타입, 에러 응답 형식, 로깅
- **Authentication/authorization integration** - 엔드포인트에 auth가 어떻게 적용되는지
- **Testing pattern** - 표준 유닛 테스트와 통합 테스트가 어떻게 구조화되는지
- **Logging pattern** - 구조화된 로그 형식, 각 레벨에서 무엇을 로깅할지
- **Configuration pattern** - 환경별 설정이 어떻게 로드되는지
- **Infrastructure as Code pattern** - 표준 CDK construct 또는 Terraform 모듈이 어떻게 보이는지

### How to Structure Example Code

#### Location
AI-DLC와 개발자가 참조할 수 있는 전용 디렉토리에 예제 코드를 저장하세요:

```
project-root/
  examples/                        # 또는 선호하면 "templates/"
    api-endpoint/
      handler.ts                   # 예제 API 핸들러
      handler.test.ts              # 대응하는 테스트
      README.md                    # 패턴과 사용 시점 설명
    database-access/
      repository.ts                # 예제 repository 패턴
      repository.test.ts
      README.md
    infrastructure/
      standard-lambda-stack.ts     # 예제 CDK 스택
      README.md
```

#### Structure of Each Example
모든 예제는 다음을 포함해야 합니다:

1. **Working code** - 의사 코드(pseudocode)가 아님. 컴파일/실행되어야 함.
2. **Corresponding test** - 패턴을 어떻게 테스트하는지 보여줌.
3. **README.md** - 다음을 설명:
   - 이 패턴이 무엇을 보여주는지
   - 언제 사용해야 하는지
   - 언제 사용하지 말아야 하는지
   - 무엇을 커스터마이즈하고 무엇을 그대로 유지할지
   - 이 Technical Environment Document의 관련 표준에 대한 참조

#### Example README Template

```
# [Pattern Name] Example

## What This Demonstrates
[패턴을 기술하는 한 단락.]

## When to Use
- [조건 1]
- [조건 2]

## When Not to Use
- [조건 1 - 대안 참조 포함]

## File Inventory
| File            | Purpose                |
| --------------- | ---------------------- |
| handler.ts      | 예제 구현              |
| handler.test.ts | 테스트 패턴            |

## Customization Guide
| Element                  | Customize?  | Notes                                       |
| ------------------------ | ----------- | ------------------------------------------- |
| Error handling structure | No          | 프로젝트 표준을 따라야 함                   |
| Business logic           | Yes         | 실제 도메인 로직으로 교체                   |
| Route path               | Yes         | API 네이밍 컨벤션을 따름                    |
| Logging calls            | No          | 구조화된 로깅 형식 유지                     |

## Related Standards
- [API Design Standards 섹션 링크]
- [Error Handling 패턴 링크]
```

### How AI-DLC Uses Example Code

Code Generation 동안, AI-DLC는 다음을 해야 합니다:

1. **Read examples first** - 어떤 코드를 생성하기 전에 examples/ 디렉토리에서
   관련 예제를 먼저 읽음
2. **Follow established patterns** - 예제에서 보여준 구조, 네이밍, 에러 처리,
   테스팅 패턴에 일치시킴
3. **Do not invent alternatives** - 패턴에 대한 예제가 존재하면 그것을 사용함.
   예제가 명시적으로 적용되지 않는 경우가 아니면 다른 접근을 만들지 않음.
4. **Reference examples in plans** - Code Generation Plan은 각 단계에 어떤
   예제가 적용되는지 참조해야 함

### Maintaining Example Code

- **Update examples when standards change** - 예제는 이 Technical Environment
  Document와 함께 최신 상태를 유지해야 함
- **Review examples during onboarding** - 새 팀원은 코드를 기여하기 전에
  모든 예제를 읽어야 함
- **Version examples with the project** - 예제는 같은 저장소에 살며
  프로덕션 코드와 같은 리뷰 프로세스를 거침
- **Mark deprecated examples** - 패턴이 대체되면 디렉토리 이름을 "deprecated-"
  접두사로 변경하고 대체에 대한 노트를 추가
````

---

### 9. Brownfield-Specific Sections

브라운필드 프로젝트에 대해서만 이 섹션들을 포함하세요.

```markdown
## Brownfield: Existing Technical Inventory

### Current State Assessment
[가능한 경우 Reverse Engineering 아티팩트를 참조하거나, 현재 기술 상태의
요약을 제공하세요.]

- **Current Languages**: [버전과 함께 나열]
- **Current Frameworks**: [버전과 함께 나열]
- **Current Infrastructure**: [클라우드 서비스, 배포 모델]
- **Current Test Coverage**: [퍼센트 또는 정성적 평가]
- **Known Technical Debt**: [핵심 항목]

### Migration and Modernization Rules

#### What to Keep
[변경되지 말아야 할 기술과 패턴.]

| Technology | Reason to Keep |
|-----------|---------------|
| [Tech] | [근거] |

#### What to Migrate
[교체되어야 할 기술과 타겟, 타임라인.]

| Current | Target | Priority | Approach |
|---------|--------|----------|----------|
| JavaScript | TypeScript | High | 점진적 파일별 마이그레이션 |
| REST API v1 | REST API v2 | Medium | 새 엔드포인트는 v2 사용, 기존은 Phase 2에서 마이그레이션 |

#### What to Remove
[제거되어야 할 기술, 패턴, 의존성.]

| Item | Reason | Removal Timeline |
|------|--------|-----------------|
| [Deprecated library] | [보안/유지 보수 우려] | [언제] |

### Coexistence Rules
[기존 패턴과 새 패턴이 공존해야 할 때 룰을 정의하세요.]

- **API versioning during migration**: [v1과 v2가 어떻게 공존하는가]
- **Database schema migration**: [기존 데이터와 함께 스키마 변경이 어떻게 관리되는가]
- **Feature flags**: [전환 동안 새 기능이 어떻게 게이팅되는가]
- **Dependency conflicts**: [충돌하는 라이브러리 버전이 어떻게 관리되는가]
```

---

## 이 문서가 AI-DLC에 어떻게 공급되는가

| Technical Environment 섹션          | AI-DLC Stage                           | 어떻게 사용되는가                                  |
| ----------------------------------- | -------------------------------------- | -------------------------------------------------- |
| Project Technical Summary           | Workspace Detection                    | 프로젝트 분류를 위한 컨텍스트                      |
| Programming Languages               | Code Generation                        | 언어 선택과 버전 제약                              |
| Frameworks and Libraries            | Code Generation, NFR Design            | 의존성 선택과 금지 라이브러리 체크                 |
| Cloud Services Allow/Disallow Lists | Infrastructure Design                  | 서비스 선택 경계                                   |
| Preferred Patterns                  | Application Design, Functional Design  | 아키텍처와 디자인 패턴 결정                        |
| Security Requirements               | NFR Requirements, NFR Design           | 보안 패턴 선택과 컴플라이언스 체크                 |
| Testing Requirements                | Code Generation, Build and Test        | 테스트 전략, 도구, 커버리지 목표                   |
| Example Code                        | Code Generation                        | 코드 생성 동안 패턴 레퍼런스                       |
| Brownfield Inventory                | Reverse Engineering, Workflow Planning | 마이그레이션 결정과 공존 룰                        |
