---
name: aidlc-nfr-assessment
description: |
  AI-DLC nfr-assessment skill. 승인된 functional-design artifact를 기반으로 단일 construction unit에 대한 `nfr-requirements.md`와 `tech-stack-decisions.md`를 생성합니다. construction phase에서 처음으로 기술 선택이 명시화되는 지점입니다.

  모든 upstream artifact가 존재하고 이 단계만 필요할 때 `/skill aidlc-nfr-assessment --unit <unit>`으로 명시적으로 호출합니다. 새로운 개발 intent의 경우에는 `aidlc-orchestrator`를 활성화합니다.
metadata:
  phase: construction
  stage: nfr-assessment
  per-unit: "true"
  human-clarification: "true"
  plan-creation: "true"
  plan-verification: "true"
  artefact-verification: "true"
---

# NFR Assessment

unit에 대한 NFR을 평가하고 기술 스택 결정을 내립니다. 이 skill은 기술 중립적인 functional design과 기술 종속적인 NFR design을 잇는 다리 역할을 합니다 — unit이 충족해야 할 품질 속성과 그것을 달성하기 위해 사용할 기술이 무엇인지를 결정합니다.

functional design이 "unit이 비즈니스 용어로 무엇을 하는가"에 답했다면, NFR assessment는 "얼마나 잘 해야 하며, 어떤 기술로 할 것인가"에 답합니다. construction phase에서 기술 선택이 명시화되는 첫 번째 지점입니다.

## Prerequisites

- 이 unit에 대한 functional design이 완료되어야 합니다 — `business-logic-model.md`, `domain-entities.md`, `business-rules.md`가 승인되어 있어야 합니다
- application design artifact가 사용 가능해야 합니다
- `requirements.md`가 사용 가능해야 합니다(NFR traceability를 위해)
- units generation이 완료되어야 합니다 — `units-of-work.md`와 `units-of-work-story-map.md`가 사용 가능해야 합니다

## Input

- Unit 컨텍스트: `units-of-work.md`, `units-of-work-story-map.md`
- functional design artifact: `business-logic-model.md`, `domain-entities.md`, `business-rules.md`
- application design artifact: `components.md`, `component-methods.md`, `component-dependencies.md`, `services.md`, `cross-cutting.md`
- 존재한다면 조건부 application design artifact: `data-models.md`, `api-contracts.md`, `event-catalog.md`, `external-dependencies.md`
- `requirements.md`

## Question Guidance

functional design artifact, application design, 그리고 `requirements.md`의 NFR을 먼저 분석합니다. NFR이나 기술 결정에 실질적으로 영향을 줄 만한 진정한 모호성이 남아 있을 때만 질문합니다. 다음 항목에 집중합니다:

- **Performance requirements**: 응답 시간 목표, throughput, latency 민감 경로.
- **Scalability requirements**: 부하 패턴(steady, bursty, seasonal), 성장 전망, 스케일링 트리거, 용량 상한.
- **Availability requirements**: 가동률, RTO, RPO. operation별 가용성 티어.
- **Security requirements**: 데이터 분류, `cross-cutting.md`를 넘어서는 인증/인가, 이 unit에 특화된 컴플라이언스 의무(GDPR, HIPAA, PCI-DSS, SOC2).
- **Reliability requirements**: fault tolerance, 의존성 실패 시 동작, error budget.
- **Observability requirements**: 메트릭, 로그, trace, 알림 임계값, SLA 보고.
- **Data requirements**: 볼륨 추정, 보존, 아카이빙, 데이터 거주성.
- **Technology preferences and constraints**: 조직적 명령, 기존 투자, 제외 항목.
- **Integration constraints**: 외부 시스템이 부과하는 프로토콜이나 포맷 요구사항.
- **Brownfield considerations** (해당하는 경우): 기존 기술 스택, 변경 가능 범위, 마이그레이션 리스크.

## Output

두 가지 artifact입니다.

### nfr-requirements.md

이 unit에 특화된 non-functional requirement로, `requirements.md`에서 파생되고 functional design을 기반으로 정교화됩니다:

- **Unit scope** — unit 이름, 소유 component, functional 복잡도 요약
- **Performance requirements** — 주요 operation별: 목표 응답 시간, throughput, latency 제약. `business-logic-model.md`의 비즈니스 workflow를 참조합니다.
- **Scalability requirements** — 예상 부하, 성장 전망, scaling 차원, scaling 트리거
- **Availability requirements** — 가동률 목표, RTO, RPO, operation별 티어
- **Security requirements** — entity별 데이터 분류, `cross-cutting.md`를 넘어서는 인증/인가 요구, 컴플라이언스 의무, 암호화(저장 시, 전송 시)
- **Reliability requirements** — fault tolerance, integration 접점별 의존성 실패 처리, error budget
- **Observability requirements** — 주요 메트릭, 로깅 분류 체계(`cross-cutting.md`를 확장), tracing, 알림
- **Data requirements** — 볼륨 추정, 보존, 아카이빙, 데이터 거주성
- **NFR traceability** — 각 요구사항은 자신이 정교화하거나 충족하는 `requirements.md`의 NFR 식별자를 참조해야 함

### tech-stack-decisions.md

이 unit에 대한 기술 선택과 근거. 각 결정은 다음을 포함합니다:

- **Decision ID** — `TSD-<n>`, unit 내에서 순차적으로 부여
- **Category** — language, framework, database, messaging, API protocol, authentication, observability, 또는 기타
- **Choice** — 선택된 기술
- **Alternatives considered** — 평가된 다른 옵션
- **Rationale** — 이 선택을 한 이유, `nfr-requirements.md`의 특정 NFR을 참조
- **Trade-offs** — 얻는 것과 희생하는 것
- **Constraints** — 도입되는 제한이나 리스크
- **Brownfield impact** (해당하는 경우) — 이 선택이 기존 기술 스택과 어떻게 상호작용하는가

## Validation

이 skill의 출력에 대한 검증 규칙은 skill 루트의 `validation-spec.md`에 있습니다. 적용 방식은 `aidlc-common/protocols/aidlc-validator-protocol.md`를 참조하세요.
