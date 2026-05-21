# 생성되는 aidlc-docs/ 레퍼런스

AI-DLC 워크플로우를 실행하면 모든 문서 아티팩트가 워크스페이스 루트의 `aidlc-docs/` 디렉토리에 생성됩니다. 생성되는 정확한 파일은 프로젝트 유형(그린필드 vs 브라운필드), 복잡도, 그리고 워크플로우가 어떤 스테이지를 실행하거나 건너뛰는지에 따라 달라집니다.

아래는 모든 단계(phase)와 스테이지(stage)에 걸친 모든 가능한 파일을 보여주는 완전히 채워진 구조입니다. 조건부 파일에는 언제 나타나는지를 나타내는 주석이 달려 있습니다.

```text
aidlc-docs/
├── aidlc-state.md                                          # 워크플로우 상태 트래커 — 프로젝트 정보, 스테이지 진행 상황, 현재 상태
├── audit.md                                                # 완전한 감사 로그 — 모든 사용자 입력, AI 응답, 승인을 타임스탬프와 함께 기록
│
├── inception/                                              # 🔵 INCEPTION PHASE — 무엇을(WHAT) 왜(WHY) 만들지를 결정
│   ├── plans/
│   │   ├── execution-plan.md                               # 워크플로우 시각화 및 단계 실행 결정 (항상 생성)
│   │   ├── story-generation-plan.md                        # 스토리 개발 방법론 및 질문 (User Stories가 실행되면)
│   │   ├── user-stories-assessment.md                      # 사용자 스토리가 가치를 더하는지에 대한 평가 (User Stories가 실행되면)
│   │   ├── application-design-plan.md                      # 질문을 포함한 컴포넌트 및 서비스 설계 계획 (Application Design이 실행되면)
│   │   └── unit-of-work-plan.md                            # 질문을 포함한 시스템 분해 계획 (Units Generation이 실행되면)
│   │
│   ├── reverse-engineering/                                # 브라운필드 프로젝트에만 생성됨 (기존 코드베이스 감지)
│   │   ├── business-overview.md                            # 비즈니스 컨텍스트, 트랜잭션, 용어집
│   │   ├── architecture.md                                 # 시스템 아키텍처 다이어그램, 컴포넌트 설명, 데이터 플로우
│   │   ├── code-structure.md                               # 빌드 시스템, 주요 클래스/모듈, 디자인 패턴, 파일 인벤토리
│   │   ├── api-documentation.md                            # REST API, 내부 API, 데이터 모델
│   │   ├── component-inventory.md                          # 모든 패키지의 타입별 인벤토리 (application, infrastructure, shared, test)
│   │   ├── technology-stack.md                             # 언어, 프레임워크, 인프라, 빌드 도구, 테스트 도구
│   │   ├── dependencies.md                                 # 내부 및 외부 의존성 그래프와 관계
│   │   ├── code-quality-assessment.md                      # 테스트 커버리지, 코드 품질 지표, 기술 부채, 패턴
│   │   └── reverse-engineering-timestamp.md                # 분석 메타데이터 및 아티팩트 체크리스트
│   │
│   ├── requirements/
│   │   ├── requirements.md                                 # 의도 분석을 포함한 기능 및 비기능 요구사항 (항상 생성)
│   │   └── requirement-verification-questions.md           # 사용자 입력을 위한 [Answer]: 태그가 있는 명확화 질문 (항상 생성)
│   │
│   ├── user-stories/                                       # User Stories 스테이지가 실행되는 경우에만 생성
│   │   ├── stories.md                                      # 인수 기준이 있는 INVEST 기준을 따르는 사용자 스토리
│   │   └── personas.md                                     # 사용자 아키타입, 특성, 페르소나-스토리 매핑
│   │
│   └── application-design/                                 # Application Design 또는 Units Generation이 실행되는 경우에만 생성
│       ├── application-design.md                           # 통합된 설계 문서 (Application Design이 실행되면)
│       ├── components.md                                   # 컴포넌트 정의, 책임, 인터페이스
│       ├── component-methods.md                            # 메서드 시그니처, 목적, 입/출력 타입
│       ├── services.md                                     # 서비스 정의, 책임, 오케스트레이션 패턴
│       ├── component-dependency.md                         # 컴포넌트 간 의존성 매트릭스 및 통신 패턴
│       ├── unit-of-work.md                                 # 유닛 정의 및 책임 (Units Generation이 실행되면)
│       ├── unit-of-work-dependency.md                      # 유닛 간 의존성 매트릭스 (Units Generation이 실행되면)
│       └── unit-of-work-story-map.md                       # 사용자 스토리를 유닛에 매핑 (Units Generation이 실행되면)
│
├── construction/                                           # 🟢 CONSTRUCTION PHASE — 어떻게(HOW) 만들지를 결정
│   ├── plans/
│   │   ├── {unit-name}-functional-design-plan.md           # 질문을 포함한 비즈니스 로직 설계 계획 (유닛별, Functional Design이 실행되면)
│   │   ├── {unit-name}-nfr-requirements-plan.md            # 질문을 포함한 NFR 평가 계획 (유닛별, NFR Requirements가 실행되면)
│   │   ├── {unit-name}-nfr-design-plan.md                  # 질문을 포함한 NFR 설계 패턴 계획 (유닛별, NFR Design이 실행되면)
│   │   ├── {unit-name}-infrastructure-design-plan.md       # 질문을 포함한 인프라 매핑 계획 (유닛별, Infrastructure Design이 실행되면)
│   │   └── {unit-name}-code-generation-plan.md             # 체크박스가 포함된 상세 코드 생성 단계 (유닛별, 항상 생성)
│   │
│   ├── {unit-name}/                                        # 유닛별 아티팩트 — unit of work당 하나의 디렉토리
│   │   ├── functional-design/                              # 이 유닛에 대해 Functional Design이 실행되는 경우에만 생성
│   │   │   ├── business-logic-model.md                     # 상세 비즈니스 로직 및 알고리즘
│   │   │   ├── business-rules.md                           # 비즈니스 룰, 검증 로직, 제약
│   │   │   ├── domain-entities.md                          # 엔티티 및 관계를 포함한 도메인 모델
│   │   │   └── frontend-components.md                      # UI 컴포넌트 계층, props, state, 상호작용 (유닛에 프론트엔드가 있는 경우)
│   │   │
│   │   ├── nfr-requirements/                               # 이 유닛에 대해 NFR Requirements가 실행되는 경우에만 생성
│   │   │   ├── nfr-requirements.md                         # 확장성, 성능, 가용성, 보안 요구사항
│   │   │   └── tech-stack-decisions.md                     # 기술 선택과 근거
│   │   │
│   │   ├── nfr-design/                                     # 이 유닛에 대해 NFR Design이 실행되는 경우에만 생성
│   │   │   ├── nfr-design-patterns.md                      # 회복성, 확장성, 성능, 보안 패턴
│   │   │   └── logical-components.md                       # 논리적 인프라 컴포넌트 (큐, 캐시 등)
│   │   │
│   │   ├── infrastructure-design/                          # 이 유닛에 대해 Infrastructure Design이 실행되는 경우에만 생성
│   │   │   ├── infrastructure-design.md                    # 클라우드 서비스 매핑 및 인프라 컴포넌트
│   │   │   └── deployment-architecture.md                  # 배포 모델, 네트워킹, 스케일링 설정
│   │   │
│   │   └── code/                                           # 생성된 코드의 마크다운 요약 (유닛별 항상 생성)
│   │       └── *.md                                        # 코드 생성 요약 (실제 코드는 워크스페이스 루트로 감)
│   │
│   ├── shared-infrastructure.md                            # 유닛 간 공유 인프라 (해당하는 경우)
│   │
│   └── build-and-test/                                     # 모든 유닛의 코드 생성이 끝난 후 항상 생성
│       ├── build-instructions.md                           # 사전 요구사항, 빌드 단계, 트러블슈팅
│       ├── unit-test-instructions.md                       # 유닛 테스트 실행 커맨드 및 예상 결과
│       ├── integration-test-instructions.md                # 통합 테스트 시나리오, 설정, 실행
│       ├── performance-test-instructions.md                # 부하/스트레스 테스트 설정 및 실행 (성능 NFR이 있는 경우)
│       ├── contract-test-instructions.md                   # 서비스 간 API 계약 검증 (마이크로서비스인 경우)
│       ├── security-test-instructions.md                   # 취약점 스캐닝 및 보안 테스트 (보안 NFR이 있는 경우)
│       ├── e2e-test-instructions.md                        # 종단 간 사용자 워크플로우 테스트 (해당하는 경우)
│       └── build-and-test-summary.md                       # 전체 빌드 상태, 테스트 결과, 준비성 평가
│
└── operations/                                             # 🟡 OPERATIONS PHASE — 향후 확장을 위한 플레이스홀더
```

## 참고 사항

- `{unit-name}`은 실제 유닛 이름(예: `api-service`, `frontend-app`, `data-processor`)으로 치환됩니다. 단일 유닛 프로젝트의 경우 일반적으로 `construction/` 아래에 하나의 유닛 디렉토리가 있습니다.
- 더 단순한 단일 유닛 프로젝트에서는 모델이 네이밍을 단순화할 수 있습니다 — 예를 들어 `construction/plans/{unit-name}-code-generation-plan.md` 대신 `construction/plans/code-generation-plan.md`를 사용하거나, 개별 컴포넌트 파일 없이 `application-design.md`를 단일 통합 파일로 둘 수 있습니다.
- `build-and-test/` 디렉토리는 항상 `build-and-test-summary.md`를 포함합니다. 개별 instruction 파일(`build-instructions.md`, `unit-test-instructions.md`, `integration-test-instructions.md` 등)은 프로젝트 복잡도와 테스트 필요에 따라 생성됩니다.
- `inception/plans/`와 `construction/plans/`의 plan에는 사용자가 입력하는 `[Answer]:` 태그와 실행 진행 상황을 추적하는 `[ ]`/`[x]` 체크박스가 포함됩니다.
- 애플리케이션 코드는 절대 `aidlc-docs/` 안에 배치되지 않습니다 — 워크스페이스 루트로 갑니다. 여기에는 마크다운 문서만 존재합니다.
- `audit.md` 파일은 append-only이며 ISO 8601 타임스탬프와 함께 모든 상호작용을 캡처합니다.
- `aidlc-state.md` 파일은 어떤 스테이지가 완료/스킵/진행 중인지와 익스텐션 설정을 추적합니다.
