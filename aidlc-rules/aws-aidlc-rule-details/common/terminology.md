# AI-DLC 용어집 (Terminology Glossary)

## 핵심 용어

### Phase vs Stage

**Phase (단계)**: AI-DLC의 세 가지 라이프사이클 단계 중 하나
- 🔵 **INCEPTION PHASE** - 기획·아키텍처 (WHAT, WHY)
- 🟢 **CONSTRUCTION PHASE** - 설계·구현·테스트 (HOW)
- 🟡 **OPERATIONS PHASE** - 배포·모니터링 (향후 확장)

**Stage (스테이지)**: phase 안의 개별 워크플로우 활동
- 예: Context Assessment 스테이지, Requirements Assessment 스테이지, Code Generation 스테이지
- 각 스테이지는 고유한 전제 조건, 단계, 출력을 가짐
- 스테이지는 ALWAYS-EXECUTE이거나 CONDITIONAL일 수 있음

**사용 예**:
- ✅ "CONSTRUCTION phase에는 7개의 스테이지가 있다"
- ✅ "Code Generation 스테이지는 항상 실행된다"
- ✅ "지금은 INCEPTION phase에서 Requirements Assessment 스테이지를 실행 중이다"
- ❌ "Requirements Assessment phase" (→ "stage"가 맞음)
- ❌ "CONSTRUCTION stage" (→ "phase"가 맞음)

## 3단계 라이프사이클

### INCEPTION PHASE
**목적**: 기획과 아키텍처 결정  
**초점**: 무엇을(WHAT) 만들지, 왜(WHY) 만드는지를 결정  
**위치**: `inception/` 디렉터리

**스테이지**:
- Workspace Detection (ALWAYS)
- Reverse Engineering (CONDITIONAL - Brownfield 전용)
- Requirements Analysis (ALWAYS - 적응형 깊이)
- User Stories (CONDITIONAL)
- Workflow Planning (ALWAYS)
- Application Design (CONDITIONAL)
- Units Generation (CONDITIONAL)

**출력**: 요구사항, 사용자 스토리, 아키텍처 결정, 단위 정의

### CONSTRUCTION PHASE
**목적**: 상세 설계와 구현  
**초점**: 어떻게(HOW) 만들지 결정  
**위치**: `construction/` 디렉터리

**스테이지**:
- Functional Design (CONDITIONAL, 단위별)
- NFR Requirements (CONDITIONAL, 단위별)
- NFR Design (CONDITIONAL, 단위별)
- Infrastructure Design (CONDITIONAL, 단위별)
- Code Generation (ALWAYS) — Part 1: Planning과 Part 2: Generation 포함
- Build and Test (ALWAYS)

**출력**: 설계 산출물, NFR 구현, 코드, 테스트

### OPERATIONS PHASE
**목적**: 배포와 운영 준비  
**초점**: 어떻게 배포(DEPLOY)하고 운영(RUN)할지  
**위치**: `operations/` 디렉터리

**스테이지**:
- Operations (PLACEHOLDER)

**출력**: 빌드 안내, 배포 가이드, 모니터링 셋업, 검증 절차

---

## 워크플로우 스테이지

### 항상 실행되는 스테이지 (Always-Execute)
- **Workspace Detection**: 워크스페이스 상태와 프로젝트 유형의 초기 분석
- **Requirements Analysis**: 요구사항 수집 (복잡도에 따라 깊이가 달라짐)
- **Workflow Planning**: 어떤 단계를 실행할지에 대한 실행 계획 작성
- **Code Generation**: 한 스테이지에 두 파트 — Part 1(Planning)은 상세 구현 계획, Part 2(Generation)는 계획과 이전 아티팩트에 기반한 실제 코드 생성
- **Build and Test**: 모든 단위를 빌드하고 종합 테스트 실행

### 조건부 스테이지 (Conditional)
- **Reverse Engineering**: 기존 코드베이스 분석 (브라운필드 프로젝트 전용)
- **User Stories**: 사용자 스토리와 페르소나 작성 (Story Planning과 Story Generation 포함)
- **Application Design**: 애플리케이션 컴포넌트, 메서드, 비즈니스 룰, 서비스 설계
- **Units Generation**: 시스템을 작업 단위로 분해 (내부 planning/generation 서브 단계 + 단위별 설계 포함)
- **Functional Design**: 기술 중립적 비즈니스 로직 설계 (단위별)
- **NFR Requirements**: NFR 결정 및 기술 스택 선택 (단위별)
- **NFR Design**: NFR 패턴과 논리 컴포넌트 반영 (단위별)
- **Infrastructure Design**: 실제 인프라 서비스로 매핑 (단위별)

## Application Design 용어

- **Component**: 구체적 책임을 가진 기능 단위
- **Method**: 컴포넌트 내의 함수/연산, 정의된 비즈니스 룰을 가짐
- **Business Rule**: 메서드 동작과 검증을 좌우하는 로직
- **Service**: 컴포넌트들에 걸친 비즈니스 로직을 조율하는 오케스트레이션 레이어
- **Component Dependency**: 컴포넌트 간 관계 및 통신 패턴

## 아키텍처 용어 (인프라)

### Unit of Work (작업 단위)
개발 목적의 사용자 스토리 논리 묶음. 기획·분해 단계에서 사용하는 용어.

**용례**: "시스템을 작업 단위로 분해해야 합니다."

### Service
마이크로서비스 아키텍처에서 독립적으로 배포 가능한 컴포넌트. 각 서비스는 별도의 작업 단위.

**용례**: "Payment Service는 모든 결제 처리를 담당합니다."

### Module
단일 서비스 또는 모놀리스 안의 기능 논리 묶음. 모듈은 독립적으로 배포되지 않음.

**용례**: "User Service 안의 인증 모듈"

### Component
서비스나 모듈 안의 재사용 가능한 빌딩 블록. 구체적 기능을 제공하는 클래스, 함수, 패키지.

**용례**: "EmailValidator 컴포넌트는 이메일 주소를 검증합니다."

## 용어 사용 가이드라인

### 각 용어의 사용 시점

**Unit of Work**:
- Units Generation 스테이지에서
- 시스템 분해를 논의할 때
- 기획 문서와 토론에서
- 예: "이걸 어떤 작업 단위로 분해해야 할까요?"

**Service**:
- 독립적으로 배포 가능한 컴포넌트를 가리킬 때
- 마이크로서비스 아키텍처 맥락에서
- 배포·인프라 논의에서
- 예: "Order Service는 ECS로 배포될 예정입니다."

**Module**:
- 서비스 내 논리적 묶음을 가리킬 때
- 모놀리스 아키텍처 맥락에서
- 내부 조직을 논의할 때
- 예: "리포팅 모듈이 모든 리포트를 생성합니다."

**Component**:
- 구체적인 클래스, 함수, 패키지를 가리킬 때
- 설계·구현 논의에서
- 재사용 가능한 빌딩 블록을 논의할 때
- 예: "DatabaseConnection 컴포넌트가 연결을 관리합니다."

## 스테이지 용어

### Planning vs Generation
- **Planning**: 실행을 위한 질문과 체크박스가 있는 계획 작성
- **Generation**: 계획을 실행해 아티팩트를 만들기

예 (이들은 별개 스테이지가 아니라 한 스테이지 안의 내부 서브 단계입니다):
- Story Planning → Story Generation (User Stories 스테이지 내부)
- Units Planning → Units Generation (Units Generation 스테이지 내부)
- Unit Design Planning → Unit Design Generation (단위별 설계 내부)
- NFR Planning → NFR Generation (NFR Requirements 스테이지 내부)
- Code Generation Part 1 (Planning) → Code Generation Part 2 (Generation)

### 깊이 수준 (Depth Levels)
- **Minimal**: 단순 변경을 위한 빠르고 집중된 실행
- **Standard**: 표준 아티팩트가 있는 일반 깊이, 보통 프로젝트용
- **Comprehensive**: 모든 아티팩트를 갖춘 완전한 깊이, 복잡/고위험 프로젝트용

## 아티팩트 유형

### Plans (계획)
실행을 안내하는 체크박스와 질문이 있는 문서.
- 위치: `aidlc-docs/plans/`
- 예: `story-generation-plan.md`, `unit-of-work-plan.md`

### Artifacts (아티팩트)
계획을 실행해서 생성된 출력물.
- 위치: 다양한 `aidlc-docs/` 하위 디렉터리
- 예: `requirements.md`, `stories.md`, `design.md`

### State Files (상태 파일)
워크플로우 진행과 상태를 추적하는 파일.
- `aidlc-state.md`: 전체 워크플로우 상태
- `audit.md`: 모든 상호작용에 대한 완전한 감사 추적

## 자주 쓰는 약어

- **AI-DLC**: AI-Driven Development Life Cycle
- **NFR**: Non-Functional Requirements (비기능 요구사항)
- **UOW**: Unit of Work (작업 단위)
- **API**: Application Programming Interface
- **CDK**: Cloud Development Kit (AWS)
