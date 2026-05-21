---
name: aidlc-nfr-design
description: |
  AI-DLC nfr-design skill. NFR 요구사항을 unit design에 반영하기 위해 design pattern(resilience, scalability, performance, security)을 선택하고 NFR 목표를 충족할 논리적 component(cache, queue, circuit breaker, load balancer)를 정의합니다. nfr-assessment(어떤 품질 속성이 필요한가)와 infrastructure-design(어디에서 실행되는가) 사이의 간극을 메웁니다.

  모든 upstream artifact가 존재하고 이 단계만 필요할 때 `/skill aidlc-nfr-design --unit <unit>`으로 명시적으로 호출합니다. 새로운 개발 intent의 경우에는 `aidlc-orchestrator`를 활성화합니다.
metadata:
  phase: construction
  stage: nfr-design
  per-unit: "true"
  human-clarification: "true"
  plan-creation: "true"
  plan-verification: "true"
  artefact-verification: "true"
---

# NFR Design

pattern과 논리적 component를 사용해 NFR 요구사항을 unit design에 반영합니다. 이 skill은 nfr-assessment에서 나온 NFR 목표와 기술 스택 결정을 받아들여, 그 목표를 충족할 design pattern과 논리적 인프라 component를 만들어 냅니다.

nfr-assessment가 "얼마나 잘 수행해야 하며, 어떤 기술로 할 것인가"에 답했다면, NFR design은 "어떤 pattern과 component가 그 목표를 달성하는가"에 답합니다. 출력은 infrastructure-design(논리적 component를 구체적 service로 매핑)과 code-generation(pattern 구현)으로 직접 전달됩니다.

## Prerequisites

- 이 unit에 대한 NFR assessment가 완료되어야 합니다 — `nfr-requirements.md`, `tech-stack-decisions.md`가 승인되어야 합니다
- functional design이 완료되어야 합니다 — `business-logic-model.md`, `domain-entities.md`, `business-rules.md`
- application design artifact가 사용 가능해야 합니다 — `components.md`, `component-methods.md`, `component-dependencies.md`, `services.md`, `cross-cutting.md`

## Input

- `nfr-requirements.md`, `tech-stack-decisions.md`(nfr-assessment에서)
- `business-logic-model.md`, `domain-entities.md`, `business-rules.md`(functional-design에서)
- `components.md`, `component-methods.md`, `component-dependencies.md`, `services.md`, `cross-cutting.md`(application-design에서)
- 존재한다면 조건부 application design artifact: `data-models.md`, `api-contracts.md`, `event-catalog.md`, `external-dependencies.md`

## Question Guidance

NFR 요구사항과 기술 스택 결정을 먼저 분석합니다. 도출 가능한 것은 도출하고, pattern이나 component 선택에 실질적으로 영향을 줄 만한 진정한 모호성이 남아 있을 때만 질문합니다. 다음 항목에 집중합니다:

- **Resilience patterns** — circuit breaker, retry, bulkhead, timeout, fallback. 어떤 integration 접점에 어떤 pattern이 필요한가? failure budget은 무엇인가?
- **Scalability patterns** — horizontal scaling, sharding, partitioning, CQRS, event sourcing. 어떤 component에 어떤 scaling 접근이 필요한가? scaling 트리거와 한계는 무엇인가?
- **Performance patterns** — 캐싱 전략(read-through, write-behind, TTL), connection pooling, batching, 비동기 처리, lazy loading. 어떤 operation이 latency 민감한가?
- **Security patterns** — 인증 흐름, 토큰 관리, 암호화 경계, rate limiting, 입력 sanitisation. 어떤 경계에 어떤 보안 통제가 필요한가?
- **Data patterns** — 복제 전략, 일관성 모델(eventual vs strong), 백업 방식, 데이터 partitioning 스킴.
- **Observability patterns** — health check 설계, 메트릭 수집 지점, 분산 trace propagation, 로그 상관(correlation) 전략.
- **Logical components** — cache, message queue, load balancer, API gateway, service mesh, CDN, secret store. 어떤 것이 필요하며 component topology에서 어디에 위치하는가?

특정 인프라 service(AWS SQS vs RabbitMQ, Redis vs Memcached)에 대해서는 묻지 마세요 — 그것은 infrastructure-design의 영역입니다. NFR design은 논리 수준에서 작동합니다: "여기에 message queue", "이 앞에 cache", "이 호출에 circuit breaker."

## Output

두 가지 artifact입니다:

### nfr-design-patterns.md

이 unit을 위해 선택된 design pattern, NFR 카테고리별로 정리:

- **Pattern name**
- **Category** — resilience, scalability, performance, security, data, observability 중 하나
- **NFR addressed** — 이 pattern이 충족하는 `nfr-requirements.md`의 요구사항(ID 참조)
- **Applied to** — 적용되는 component, service, 또는 integration 접점(`components.md` 또는 `business-logic-model.md` 참조)
- **Behaviour** — 해당 컨텍스트에서 pattern이 동작하는 방식(트리거 조건, 임계값, fallback 동작)
- **Configuration parameters** — 튜닝 가능한 값(timeout 길이, retry 횟수, cache TTL, pool 크기 등)과 NFR 목표에서 파생된 권장 기본값
- **Trade-offs** — 이 pattern이 치르는 비용(latency overhead, 복잡도, eventual consistency 등)
- **Interaction with other patterns** — 같은 component에 적용된 다른 pattern과의 결합 방식(예: retry + circuit breaker 순서)

### logical-components.md

NFR design에서 도입된 논리적 인프라 component. 각각 다음을 포함합니다:

- **Component name** — 서술적인 논리 이름(예: "order-cache", "payment-circuit-breaker", "event-queue")
- **Type** — cache, queue, load balancer, circuit breaker, rate limiter, secret store, CDN, service mesh, health checker, 기타
- **Purpose** — 어떤 NFR 요구사항을 충족하는가
- **Position** — component topology에서의 위치(어떤 component 사이에, 어떤 service 앞에)
- **Inputs/Outputs** — 무엇이 통과하는가
- **Configuration** — 논리적 구성(용량, TTL, 임계값, 정책) — 벤더 종속적이지 않음
- **Failure mode** — 이 component 자체가 실패할 때 어떻게 되는가(graceful degradation, 우회, 알림)
- **Downstream consumers** — 이 component를 알아야 하는 skill/artifact(infrastructure-design은 service에 매핑하고, code-generation은 integration을 구현)

## Validation

이 skill의 출력에 대한 검증 규칙은 skill 루트의 `validation-spec.md`에 있습니다. 적용 방식은 `aidlc-common/protocols/aidlc-validator-protocol.md`를 참조하세요.
