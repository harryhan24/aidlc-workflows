---
name: aidlc-infrastructure-design
description: |
  AI-DLC infrastructure-design skill. 단일 unit에 대해 논리적 component와 NFR design pattern을 구체적인 인프라 service, 구성, 배포 topology로 매핑합니다. `infrastructure-design.md`와 `deployment-architecture.md`를 생성합니다.

  모든 upstream artifact가 존재하고 이 단계만 필요할 때 `/skill aidlc-infrastructure-design --unit <unit>`으로 명시적으로 호출합니다. 새로운 개발 intent의 경우에는 `aidlc-orchestrator`를 활성화합니다.
metadata:
  phase: construction
  stage: infrastructure-design
  per-unit: "true"
  human-clarification: "true"
  plan-creation: "true"
  plan-verification: "true"
  artefact-verification: "true"
---

# Infrastructure Design

논리적 소프트웨어 component를 배포를 위한 구체적인 인프라 선택으로 매핑합니다. 이 skill은 nfr-assessment의 기술 결정과 nfr-design의 design pattern을 입력으로 받아, 운영 환경에서 unit을 실행하는 데 필요한 실제 service 선택, 구성, sizing, 배포 topology를 만들어 냅니다.

nfr-assessment가 "어떤 기술"에 답하고 nfr-design이 "어떤 pattern"에 답했다면, infrastructure-design은 "어디서, 어떻게 실행되는가"에 답합니다.

## Prerequisites

- 이 unit에 대한 NFR assessment가 완료되어야 합니다 — `nfr-requirements.md`, `tech-stack-decisions.md`가 승인되어야 합니다
- (해당한다면) NFR design이 완료되어야 합니다 — `nfr-design-patterns.md`, `logical-components.md`
- functional design이 완료되어야 합니다 — `business-logic-model.md`, `domain-entities.md`, `business-rules.md`
- application design artifact가 사용 가능해야 합니다 — `components.md`, `component-dependencies.md`, `services.md`, `cross-cutting.md`

## Input

- `nfr-requirements.md`, `tech-stack-decisions.md`(nfr-assessment에서)
- `nfr-design-patterns.md`, `logical-components.md`(nfr-design에서, 존재한다면)
- `business-logic-model.md`, `domain-entities.md`(functional-design에서)
- `components.md`, `component-dependencies.md`, `services.md`, `cross-cutting.md`(application-design에서)
- `units-of-work.md`, `units-of-work-dependency.md`(unit 간 integration 지점을 위해)

## Question Guidance

다음 항목을 중심으로 명확화 질문을 집중합니다:

- **Target platform** — 이 unit이 배포되는 환경은 무엇인가? 기존 Kubernetes cluster, serverless platform, VM 기반, container service, bare metal? 이미 프로비저닝된 것과 새로 생성해야 하는 것은 무엇인가?
- **Cloud provider** (tech-stack-decisions에서 아직 결정되지 않았다면) — AWS, Azure, GCP, 온프레미스, 하이브리드? 특정 계정/구독 제약은?
- **Compute** — 각 component에 어떤 특정 compute service를 쓸 것인가?(예: Lambda vs ECS vs EKS, App Service vs AKS, Cloud Run vs GKE). 인스턴스 sizing, scaling 정책.
- **Storage** — 어떤 특정 database/storage service인가?(예: RDS vs DynamoDB, Cosmos DB vs SQL Database). 인스턴스 클래스, 스토리지 할당, 백업 전략.
- **Messaging** — 비동기 통신을 위한 어떤 특정 messaging service인가?(예: SQS vs EventBridge vs Kafka, Service Bus vs Event Grid). queue/topic 구성.
- **Networking** — load balancer 유형, API gateway, DNS, CDN. component별 내부 vs 외부 노출.
- **Security infrastructure** — secret 관리 service, 인증서 프로비저닝, IAM/RBAC 모델, 암호화 키 관리.
- **Observability infrastructure** — 어떤 모니터링, 로깅, tracing service를 사용할 것인가? 보존 정책, 알림 채널.
- **Cost constraints** — 예산이 service tier나 sizing 선택에 영향을 주는가? Reserved vs on-demand?
- **Environment strategy** — 몇 개의 환경(dev, staging, prod)이 있는가? 환경 간 동등성은 어떻게 유지하는가?
- **Inter-unit integration** — 이 unit이 인프라 수준에서 다른 unit과 어떻게 연결되는가? Service mesh, 직접 DNS, message broker?

무엇을 만들지, 어떤 기술을 쓸지, 어떤 pattern을 적용할지에 대해서는 묻지 마세요 — 그것들은 upstream에서 결정됩니다. infrastructure-design은 기존 결정을 실제로 배포 가능한 service에 매핑합니다.

## Output

두 가지 artifact입니다:

### infrastructure-design.md

(`logical-components.md` 또는 `components.md` 기준의) 논리적 component별:

- **Component name** — upstream에서
- **Infrastructure service** — 구체적 service(예: "AWS RDS PostgreSQL 15", "Azure Service Bus Standard")
- **Configuration** — 인스턴스 타입/크기, 스토리지, 복제본, scaling 정책, 주요 설정
- **Networking** — 어떻게 노출되는가(내부 전용, LB를 통한 공개, API gateway 뒤), security group/방화벽 규칙
- **Security** — 저장 시/전송 시 암호화, IAM 역할/정책, secret 참조
- **Observability** — 방출되는 메트릭, 로그 목적지, trace integration
- **Cost estimate** — 예상 부하에서의 대략적 월별 비용(nfr-requirements 기준)
- **Rationale** — 대안 대비 이 service를 선택한 이유, `tech-stack-decisions.md` 참조
- **Platform assumptions** — 이것이 동작하기 위해 이미 존재해야 하는 것(예: "private subnet이 있는 VPC가 존재한다고 가정", "EKS cluster v1.28+를 가정")

### deployment-architecture.md

unit의 인프라가 어떻게 결합되는지에 대한 시스템 수준의 관점:

- **Topology** — 텍스트 기반 아키텍처 설명: 무엇이 어디서 실행되는가, component 연결, 트래픽 흐름
- **Environments** — dev/staging/prod 간 차이(sizing, 복제본, feature flag)
- **Scaling strategy** — component별: 트리거, min/max, cooldown
- **Failover and recovery** — multi-AZ/region 전략, 백업/복원 절차, 실제 메커니즘에 대응되는 RTO/RPO
- **Deployment pipeline** — repo에서 실행 환경까지 코드가 도달하는 방식(CI/CD 단계, 승인 gate, 롤백 전략)
- **Inter-unit connectivity** — 이 unit이 다른 unit에 어떻게 도달하고, 다른 unit이 어떻게 이 unit에 도달하는가(service discovery, DNS, 메시지 라우팅)
- **Infrastructure-as-code notes** — 권장 IaC 접근(Terraform, CDK, Pulumi, CloudFormation)과 모듈/stack 경계

## Validation

이 skill의 출력에 대한 검증 규칙은 skill 루트의 `validation-spec.md`에 있습니다. 적용 방식은 `aidlc-common/protocols/aidlc-validator-protocol.md`를 참조하세요.
