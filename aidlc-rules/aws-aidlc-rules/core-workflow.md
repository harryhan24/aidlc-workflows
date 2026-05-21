# PRIORITY: 이 워크플로우는 다른 모든 내장 워크플로우를 무시(OVERRIDE)합니다
# 사용자가 소프트웨어 개발을 요청하면 항상 이 워크플로우를 가장 먼저 따르세요

## 적응형 워크플로우 원칙 (Adaptive Workflow Principle)
**워크플로우가 작업에 맞춰 적응하는 것이지, 그 반대가 아닙니다.**

AI 모델은 다음을 기반으로 어떤 스테이지가 필요한지 지능적으로 판단합니다:
1. 사용자의 명시된 의도와 명확성
2. 기존 코드베이스 상태 (있는 경우)
3. 변경의 복잡도와 범위
4. 리스크와 영향도 평가

## MANDATORY: 룰 디테일 로딩 (Rule Details Loading)
**CRITICAL**: 어떤 단계를 수행하든 룰 디테일 파일에서 관련 내용을 반드시 읽어와 사용해야 합니다. 다음 경로들을 순서대로 확인하고, 어떤 IDE/설치 방식이 사용되었든 가장 먼저 존재하는 경로를 사용하세요:
- `.aidlc/aidlc-rules/aws-aidlc-rule-details/` (AI 보조 설치에서 일반적)
- `.aidlc-rule-details/` (Cursor, Cline, Claude Code, GitHub Copilot, OpenAI Codex에서 일반적)
- `.kiro/aws-aidlc-rule-details/` (Kiro IDE와 CLI에서 일반적)
- `.amazonq/aws-aidlc-rule-details/` (Amazon Q Developer에서 일반적)

이후 모든 룰 디테일 파일 참조(예: `common/process-overview.md`, `inception/workspace-detection.md`)는 위에서 결정된 룰 디테일 디렉터리를 기준으로 한 상대 경로입니다.

**Common Rules**: 워크플로우 시작 시 다음 공통 룰을 항상 로드합니다:
- 워크플로우 개요를 위해 `common/process-overview.md`를 로드
- 세션 재개 가이드를 위해 `common/session-continuity.md`를 로드
- 콘텐츠 검증 요구사항을 위해 `common/content-validation.md`를 로드
- 질문 포맷팅 룰을 위해 `common/question-format-guide.md`를 로드
- 워크플로우 실행 전반에 걸쳐 이들을 참조

## MANDATORY: 익스텐션 로딩 (Extensions Loading, 컨텍스트 최적화)
**CRITICAL**: 워크플로우 시작 시 `extensions/` 디렉터리를 재귀적으로 스캔하되, 가벼운 opt-in 파일만 로드합니다 — 전체 룰 파일은 로드하지 않습니다. 전체 룰 파일은 사용자가 옵트인한 뒤 필요할 때(on-demand) 로드됩니다.

**로딩 절차**:
1. `extensions/` 아래의 모든 하위 디렉터리를 나열합니다(예: `extensions/security/`, `extensions/compliance/`).
2. 각 하위 디렉터리에서 `*.opt-in.md` 파일만 로드합니다 — 이 파일에는 익스텐션의 opt-in 프롬프트가 들어 있습니다. 해당하는 룰 파일은 규약으로 도출됩니다: `.opt-in.md` 접미사를 제거하고 `.md`를 붙입니다(예: `security-baseline.opt-in.md` → `security-baseline.md`).
3. 이 단계에서는 전체 룰 파일(예: `security-baseline.md`)을 로드하지 마세요.

**지연 룰 로딩 (Deferred Rule Loading)**:
- 요구사항 분석(Requirements Analysis) 단계에서 로드된 `*.opt-in.md` 파일의 opt-in 프롬프트가 사용자에게 제시됩니다.
- 사용자가 익스텐션에 옵트인하면, 그 시점에 해당하는 룰 파일을(명명 규약으로 도출) 로드합니다.
- 사용자가 옵트아웃하면, 전체 룰 파일은 결코 로드되지 않습니다 — 컨텍스트를 절약합니다.
- 짝이 되는 `*.opt-in.md` 파일이 없는 익스텐션은 항상 적용됩니다 — 워크플로우 시작 시 곧바로 룰 파일을 로드합니다.

**적용 (Enforcement, 로드/활성화된 익스텐션에만 적용)**:
- 익스텐션 룰은 단순한 가이드가 아니라 강한(hard) 제약입니다.
- 각 스테이지에서, 모델은 스테이지의 목적, 산출되는 아티팩트, 작업의 맥락을 기반으로 어떤 익스텐션 룰이 적용 가능한지 지능적으로 평가합니다 — 관련 있는 룰만 적용하세요.
- 현재 스테이지에 적용되지 않는 룰은 컴플라이언스 요약에서 N/A로 표시해야 합니다 (이는 차단(blocking) 발견사항이 아닙니다).
- 적용 가능한, 활성화된 익스텐션 룰을 어기는 것은 **차단성 발견사항**입니다 — 해결될 때까지 스테이지 완료를 제시하지 마세요.
- 스테이지 완료를 제시할 때는 익스텐션 룰 준수 요약을 포함하세요(룰별 compliant/non-compliant/N/A, N/A 판단에 대한 간단한 근거 포함).

**조건부 적용 (Conditional Enforcement)**: 익스텐션은 조건부로 활성화/비활성화될 수 있습니다. opt-in 메커니즘은 `inception/requirements-analysis.md`를 참고하세요. 어느 스테이지에서든 익스텐션을 적용하기 전에, `aidlc-docs/aidlc-state.md`의 `## Extension Configuration` 아래에서 그 익스텐션의 `Enabled` 상태를 확인하세요. 비활성화된 익스텐션은 건너뛰고, 건너뛴 사실을 audit.md에 로깅합니다. 설정이 존재하지 않으면 기본적으로 적용합니다.

## MANDATORY: 콘텐츠 검증 (Content Validation)
**CRITICAL**: 어떤 파일을 생성하기 전에든, `common/content-validation.md` 룰에 따라 콘텐츠를 검증해야 합니다:
- Mermaid 다이어그램 문법 검증
- ASCII 아트 다이어그램 검증 (`common/ascii-diagram-standards.md` 참고)
- 특수 문자를 올바르게 이스케이프
- 복잡한 시각 자료에 대한 텍스트 대안 제공
- 콘텐츠 파싱 호환성 테스트

## MANDATORY: 질문 파일 포맷 (Question File Format)
**CRITICAL**: 어떤 단계에서든 질문을 할 때는 질문 포맷 가이드라인을 따라야 합니다.

**전체 질문 포맷팅 룰은 `common/question-format-guide.md`를 참고하세요. 다음이 포함됩니다**:
- 객관식 포맷 (A, B, C, D, E 옵션)
- [Answer]: 태그 사용법
- 답변 검증 및 모호성 해소

## MANDATORY: 커스텀 환영 메시지 (Custom Welcome Message)
**CRITICAL**: 어떤 소프트웨어 개발 요청을 시작하든, 환영 메시지를 반드시 표시해야 합니다.

**환영 메시지 표시 방법**:
1. 결정된 룰 디테일 디렉터리에서 `common/welcome-message.md`의 환영 메시지를 로드합니다.
2. 전체 메시지를 사용자에게 표시합니다.
3. 새 워크플로우 시작 시 단 한 번만 수행합니다.
4. 이후 상호작용에서는 컨텍스트 공간을 절약하기 위해 이 파일을 로드하지 마세요.

# 적응형 소프트웨어 개발 워크플로우

---

# INCEPTION PHASE (인셉션 단계)

**목적**: 기획, 요구사항 수집, 그리고 아키텍처 결정

**초점**: **무엇을(WHAT)** 만들지, **왜(WHY)** 만드는지를 결정

**INCEPTION PHASE의 스테이지**:
- Workspace Detection (ALWAYS)
- Reverse Engineering (CONDITIONAL - Brownfield 전용)
- Requirements Analysis (ALWAYS - 적응형 깊이)
- User Stories (CONDITIONAL)
- Workflow Planning (ALWAYS)
- Application Design (CONDITIONAL)
- Units Generation (CONDITIONAL)

---

## Workspace Detection (ALWAYS EXECUTE)

1. **MANDATORY**: 초기 사용자 요청을 audit.md에 완전한 원본 입력 그대로 로깅
2. `inception/workspace-detection.md`의 모든 단계를 로드
3. workspace detection 실행:
   - 기존 aidlc-state.md가 있는지 확인 (있으면 재개)
   - 워크스페이스에서 기존 코드 스캔
   - 브라운필드(brownfield)인지 그린필드(greenfield)인지 판단
   - 기존 리버스 엔지니어링 아티팩트 확인
4. 다음 단계 결정: Reverse Engineering (brownfield이고 아티팩트가 없는 경우) 또는 Requirements Analysis
5. **MANDATORY**: 발견사항을 audit.md에 로깅
6. 사용자에게 완료 메시지 제시 (메시지 포맷은 workspace-detection.md 참고)
7. 다음 단계로 자동 진행

## Reverse Engineering (CONDITIONAL - Brownfield 전용)

**다음일 때 실행**:
- 기존 코드베이스가 감지됨
- 이전 리버스 엔지니어링 아티팩트가 없음

**다음일 때 건너뜀**:
- 그린필드 프로젝트
- 이전 리버스 엔지니어링 아티팩트가 존재

**실행**:
1. **MANDATORY**: 리버스 엔지니어링 시작을 audit.md에 로깅
2. `inception/reverse-engineering.md`의 모든 단계를 로드
3. 리버스 엔지니어링 실행:
   - 모든 패키지와 컴포넌트 분석
   - 비즈니스 트랜잭션을 다루는 전체 시스템의 비즈니스 개요 생성
   - 아키텍처 문서 생성
   - 코드 구조 문서 생성
   - API 문서 생성
   - 컴포넌트 인벤토리(component inventory) 생성
   - 비즈니스 트랜잭션이 컴포넌트들에 걸쳐 어떻게 구현되는지 보여주는 상호작용 다이어그램(Interaction Diagram) 생성
   - 기술 스택 문서 생성
   - 의존성 문서 생성

4. **명시적 승인 대기**: 상세 완료 메시지 제시(메시지 포맷은 reverse-engineering.md 참고) — 사용자가 확인할 때까지 진행하지 마세요.
5. **MANDATORY**: 사용자 응답을 audit.md에 완전한 원본 입력 그대로 로깅

## Requirements Analysis (ALWAYS EXECUTE - 적응형 깊이)

**항상 실행**되지만, 요청의 명확성과 복잡도에 따라 깊이가 달라집니다:
- **Minimal**: 단순하고 명확한 요청 - 의도 분석만 문서화
- **Standard**: 보통 복잡도 - 기능/비기능 요구사항 수집
- **Comprehensive**: 복잡하고 위험도 높음 - 추적성(traceability)을 갖춘 상세 요구사항

**실행**:
1. **MANDATORY**: 이 단계 중 모든 사용자 입력을 audit.md에 로깅
2. `inception/requirements-analysis.md`의 모든 단계를 로드
3. 요구사항 분석 실행:
   - 리버스 엔지니어링 아티팩트 로드 (brownfield인 경우)
   - 사용자 요청 분석 (의도 분석)
   - 필요한 요구사항 깊이 결정
   - 현재 요구사항 평가
   - 필요 시 명확화 질문(clarifying question) 제시
   - 요구사항 문서 생성
4. 적절한 깊이(minimal/standard/comprehensive)로 실행
5. **명시적 승인 대기**: requirements-analysis.md의 상세 단계에서 정한 승인 형식을 따르세요 — 사용자가 확인할 때까지 진행하지 마세요.
6. **MANDATORY**: 사용자 응답을 audit.md에 완전한 원본 입력 그대로 로깅

## User Stories (CONDITIONAL)

**지능형 평가 (INTELLIGENT ASSESSMENT)**: 사용자 스토리가 가치를 더하는지 판단하기 위해 다요인 분석을 사용:

**다음일 때 항상 실행** (높은 우선순위 지표):
- 새로운 사용자 대상 기능 또는 기능성
- 사용자 워크플로우/상호작용에 영향을 주는 변경
- 여러 사용자 유형이나 페르소나가 관여
- 수락 기준(acceptance criteria)이 필요한 복잡한 비즈니스 요구사항
- 부서 간 협업이 필요함
- 고객 대상 API 또는 서비스 변경
- 새로운 제품 기능 또는 개선

**다음일 때 실행 가능성 높음** (중간 우선순위 - 복잡도 평가 필요):
- 기존 사용자 대상 기능 수정
- 사용자 경험에 간접 영향을 주는 백엔드 변경
- 사용자 워크플로우에 영향을 주는 통합 작업
- 사용자에게 보이는 효과를 동반한 성능 개선
- 사용자 상호작용에 영향을 주는 보안 강화
- 사용자 데이터나 리포트에 영향을 주는 데이터 모델 변경

**복잡도 기반 평가**: 중간 우선순위 케이스에서는 다음 조건이면 사용자 스토리를 실행:
- 요청이 여러 컴포넌트나 서비스에 걸침
- 변경이 여러 사용자 접점(touchpoint)에 걸침
- 비즈니스 로직이 복잡하거나 여러 시나리오가 있음
- 요구사항에 모호성이 있어 스토리가 명확화에 도움이 될 때
- 구현이 여러 사용자 여정(user journey)에 영향
- 변경이 비즈니스에 큰 영향이나 리스크를 줌

**다음일 때만 건너뜀** (낮은 우선순위 - 단순 케이스):
- 사용자 영향이 전혀 없는 순수 내부 리팩터링
- 명확하고 고립된 범위의 단순 버그 수정
- 사용자에게 보이는 영향이 없는 인프라 변경
- 기능 변화가 없는 기술 부채 정리
- 개발 도구 또는 빌드 프로세스 개선
- 문서만 업데이트하는 경우

**평가 기준 (ASSESSMENT CRITERIA)**: 애매할 때는 다음 경우 사용자 스토리를 포함하는 쪽을 선호:
- 비즈니스 이해관계자가 관여하는 요청
- 사용자 수락 테스트(user acceptance testing)가 필요한 변경
- 여러 구현 접근이 가능한 기능
- 팀의 공통 이해(shared understanding)가 도움이 되는 작업
- 요구사항 명확성이 중요한 프로젝트

**평가 절차 (ASSESSMENT PROCESS)**: 
1. 요청의 복잡도와 범위를 분석
2. 사용자 영향(직접/간접)을 식별
3. 비즈니스 맥락과 이해관계자 요구를 평가
4. 팀 협업 측면의 이점을 고려
5. 경계선 케이스에서는 기본적으로 포함 쪽으로

**참고**: Requirements Analysis가 실행되었다면, Stories는 그 요구사항을 참조하고 그 위에 쌓아 올릴 수 있습니다.

**User Stories는 한 스테이지 안에서 두 파트로 구성됩니다**:
1. **Part 1 - Planning**: 질문이 포함된 스토리 계획 작성, 답변 수집, 모호성 분석, 승인 받기
2. **Part 2 - Generation**: 승인된 계획을 실행해 스토리와 페르소나(persona) 생성

**실행**:
1. **MANDATORY**: 이 단계 중 모든 사용자 입력을 audit.md에 로깅
2. `inception/user-stories.md`의 모든 단계를 로드
3. **MANDATORY**: 사용자 스토리가 필요한지 검증하기 위해 지능형 평가 수행 (user-stories.md의 Step 1)
4. 리버스 엔지니어링 아티팩트 로드 (brownfield인 경우)
5. 요구사항이 있으면, 스토리 작성 시 참조
6. 적절한 깊이(minimal/standard/comprehensive)로 실행
7. **PART 1 - Planning**: 질문이 포함된 스토리 계획 작성, 사용자 답변 대기, 모호성 분석, 승인 받기
8. **PART 2 - Generation**: 승인된 계획을 실행해 스토리와 페르소나 생성
9. **명시적 승인 대기**: user-stories.md의 상세 단계에서 정한 승인 형식을 따르세요 — 사용자가 확인할 때까지 진행하지 마세요.
10. **MANDATORY**: 사용자 응답을 audit.md에 완전한 원본 입력 그대로 로깅

## Workflow Planning (ALWAYS EXECUTE)

1. **MANDATORY**: 이 단계 중 모든 사용자 입력을 audit.md에 로깅
2. `inception/workflow-planning.md`의 모든 단계를 로드
3. **MANDATORY**: `common/content-validation.md`의 콘텐츠 검증 룰을 로드
4. 이전의 모든 컨텍스트를 로드:
   - 리버스 엔지니어링 아티팩트 (brownfield인 경우)
   - 의도 분석(intent analysis)
   - 요구사항 (실행되었다면)
   - 사용자 스토리 (실행되었다면)
5. workflow planning 실행:
   - 어떤 단계를 실행할지 결정
   - 각 단계의 깊이 수준을 결정
   - 다중 패키지 변경 시퀀스 생성 (brownfield인 경우)
   - 워크플로우 시각화 생성 (작성 전에 Mermaid 문법을 검증)
6. **MANDATORY**: 파일 생성 전에 content-validation.md 룰에 따라 모든 콘텐츠를 검증
7. **명시적 승인 대기**: workflow-planning.md의 Step 9에 있는 표현을 사용해 권장안을 제시하고, 사용자가 권장안을 덮어쓸 수 있는 통제권을 가진다는 점을 강조하세요 — 사용자가 확인할 때까지 진행하지 마세요.
8. **MANDATORY**: 사용자 응답을 audit.md에 완전한 원본 입력 그대로 로깅

## Application Design (CONDITIONAL)

**다음일 때 실행**:
- 새로운 컴포넌트나 서비스가 필요
- 컴포넌트의 메서드와 비즈니스 룰을 정의해야 함
- 서비스 레이어 설계가 필요
- 컴포넌트 의존성을 명확히 해야 함

**다음일 때 건너뜀**:
- 기존 컴포넌트 경계 내부의 변경
- 새 컴포넌트나 메서드가 없음
- 순수 구현 변경

**실행**:
1. **MANDATORY**: 이 단계 중 모든 사용자 입력을 audit.md에 로깅
2. `inception/application-design.md`의 모든 단계를 로드
3. 리버스 엔지니어링 아티팩트 로드 (brownfield인 경우)
4. 적절한 깊이(minimal/standard/comprehensive)로 실행
5. **명시적 승인 대기**: 상세 완료 메시지를 제시 (메시지 포맷은 application-design.md 참고) — 사용자가 확인할 때까지 진행하지 마세요.
6. **MANDATORY**: 사용자 응답을 audit.md에 완전한 원본 입력 그대로 로깅

## Units Generation (CONDITIONAL)

**다음일 때 실행**:
- 시스템을 여러 작업 단위(unit of work)로 분해해야 함
- 여러 서비스나 모듈이 필요
- 구조적 분해가 필요한 복잡한 시스템

**다음일 때 건너뜀**:
- 단일하고 단순한 단위
- 분해가 필요 없음
- 단일 컴포넌트로 구현되는 단순 구현

**실행**:
1. **MANDATORY**: 이 단계 중 모든 사용자 입력을 audit.md에 로깅
2. `inception/units-generation.md`의 모든 단계를 로드
3. 리버스 엔지니어링 아티팩트 로드 (brownfield인 경우)
4. 적절한 깊이(minimal/standard/comprehensive)로 실행
5. **명시적 승인 대기**: 상세 완료 메시지를 제시 (메시지 포맷은 units-generation.md 참고) — 사용자가 확인할 때까지 진행하지 마세요.
6. **MANDATORY**: 사용자 응답을 audit.md에 완전한 원본 입력 그대로 로깅

---

# 🟢 CONSTRUCTION PHASE (컨스트럭션 단계)

**목적**: 상세 설계, NFR 구현, 그리고 코드 생성

**초점**: **어떻게(HOW)** 만들지를 결정

**CONSTRUCTION PHASE의 스테이지**:
- 단위별 루프 (각 단위마다 실행):
  - Functional Design (CONDITIONAL, 단위별)
  - NFR Requirements (CONDITIONAL, 단위별)
  - NFR Design (CONDITIONAL, 단위별)
  - Infrastructure Design (CONDITIONAL, 단위별)
  - Code Generation (ALWAYS, 단위별)
- Build and Test (ALWAYS - 모든 단위 완료 후)

**참고**: 각 단위는 다음 단위로 넘어가기 전에 완전히(설계 + 코드) 끝냅니다.

---

## 단위별 루프 (Per-Unit Loop, 각 단위마다 실행)

**각 작업 단위에 대해, 아래 스테이지를 순서대로 실행합니다:**

### Functional Design (CONDITIONAL, 단위별)

**다음일 때 실행**:
- 새 데이터 모델이나 스키마
- 복잡한 비즈니스 로직
- 비즈니스 룰의 상세 설계가 필요

**다음일 때 건너뜀**:
- 단순한 로직 변경
- 새로운 비즈니스 로직 없음

**실행**:
1. **MANDATORY**: 이 스테이지 중 모든 사용자 입력을 audit.md에 로깅
2. `construction/functional-design.md`의 모든 단계를 로드
3. 이 단위에 대해 기능 설계 실행
4. **MANDATORY**: functional-design.md에 정의된 표준화된 2-옵션 완료 메시지를 제시하세요 — 즉흥적인 3-옵션 동작을 사용하지 마세요.
5. **명시적 승인 대기**: 사용자는 "Request Changes"와 "Continue to Next Stage" 중에서 선택해야 합니다 — 사용자가 확인할 때까지 진행하지 마세요.
6. **MANDATORY**: 사용자 응답을 audit.md에 완전한 원본 입력 그대로 로깅

### NFR Requirements (CONDITIONAL, 단위별)

**다음일 때 실행**:
- 성능 요구사항이 존재
- 보안 고려사항이 필요
- 확장성 우려가 존재
- 기술 스택 선정이 필요

**다음일 때 건너뜀**:
- NFR 요구사항 없음
- 기술 스택이 이미 결정됨

**실행**:
1. **MANDATORY**: 이 스테이지 중 모든 사용자 입력을 audit.md에 로깅
2. `construction/nfr-requirements.md`의 모든 단계를 로드
3. 이 단위에 대해 NFR 평가 실행
4. **MANDATORY**: nfr-requirements.md에 정의된 표준화된 2-옵션 완료 메시지를 제시하세요 — 즉흥적인 동작을 사용하지 마세요.
5. **명시적 승인 대기**: 사용자는 "Request Changes"와 "Continue to Next Stage" 중에서 선택해야 합니다 — 사용자가 확인할 때까지 진행하지 마세요.
6. **MANDATORY**: 사용자 응답을 audit.md에 완전한 원본 입력 그대로 로깅

### NFR Design (CONDITIONAL, 단위별)

**다음일 때 실행**:
- NFR Requirements가 실행됨
- NFR 패턴을 반영해야 함

**다음일 때 건너뜀**:
- NFR 요구사항 없음
- NFR Requirements를 건너뜀

**실행**:
1. **MANDATORY**: 이 스테이지 중 모든 사용자 입력을 audit.md에 로깅
2. `construction/nfr-design.md`의 모든 단계를 로드
3. 이 단위에 대해 NFR 설계 실행
4. **MANDATORY**: nfr-design.md에 정의된 표준화된 2-옵션 완료 메시지를 제시하세요 — 즉흥적인 동작을 사용하지 마세요.
5. **명시적 승인 대기**: 사용자는 "Request Changes"와 "Continue to Next Stage" 중에서 선택해야 합니다 — 사용자가 확인할 때까지 진행하지 마세요.
6. **MANDATORY**: 사용자 응답을 audit.md에 완전한 원본 입력 그대로 로깅

### Infrastructure Design (CONDITIONAL, 단위별)

**다음일 때 실행**:
- 인프라 서비스를 매핑해야 함
- 배포 아키텍처가 필요
- 클라우드 리소스를 명세해야 함

**다음일 때 건너뜀**:
- 인프라 변경이 없음
- 인프라가 이미 정의됨

**실행**:
1. **MANDATORY**: 이 스테이지 중 모든 사용자 입력을 audit.md에 로깅
2. `construction/infrastructure-design.md`의 모든 단계를 로드
3. 이 단위에 대해 인프라 설계 실행
4. **MANDATORY**: infrastructure-design.md에 정의된 표준화된 2-옵션 완료 메시지를 제시하세요 — 즉흥적인 동작을 사용하지 마세요.
5. **명시적 승인 대기**: 사용자는 "Request Changes"와 "Continue to Next Stage" 중에서 선택해야 합니다 — 사용자가 확인할 때까지 진행하지 마세요.
6. **MANDATORY**: 사용자 응답을 audit.md에 완전한 원본 입력 그대로 로깅

### Code Generation (ALWAYS EXECUTE, 단위별)

**각 단위마다 항상 실행**

**Code Generation은 한 스테이지 안에서 두 파트로 구성됩니다**:
1. **Part 1 - Planning**: 명시적 단계가 포함된 상세 코드 생성 계획 작성
2. **Part 2 - Generation**: 승인된 계획을 실행해 코드, 테스트, 산출물을 생성

**실행**:
1. **MANDATORY**: 이 스테이지 중 모든 사용자 입력을 audit.md에 로깅
2. `construction/code-generation.md`의 모든 단계를 로드
3. **PART 1 - Planning**: 체크박스가 포함된 코드 생성 계획 작성, 사용자 승인 받기
4. **PART 2 - Generation**: 승인된 계획을 실행해 이 단위의 코드를 생성
5. **MANDATORY**: code-generation.md에 정의된 표준화된 2-옵션 완료 메시지를 제시하세요 — 즉흥적인 동작을 사용하지 마세요.
6. **명시적 승인 대기**: 사용자는 "Request Changes"와 "Continue to Next Stage" 중에서 선택해야 합니다 — 사용자가 확인할 때까지 진행하지 마세요.
7. **MANDATORY**: 사용자 응답을 audit.md에 완전한 원본 입력 그대로 로깅

---

## Build and Test (ALWAYS EXECUTE)

1. **MANDATORY**: 이 단계 중 모든 사용자 입력을 audit.md에 로깅
2. `construction/build-and-test.md`의 모든 단계를 로드
3. 종합적인 빌드/테스트 안내 생성:
   - 모든 단위에 대한 빌드 안내
   - 유닛 테스트 실행 안내
   - 통합 테스트 안내 (단위 간 상호작용 테스트)
   - 성능 테스트 안내 (해당되는 경우)
   - 필요한 추가 테스트 안내 (계약 테스트, 보안 테스트, e2e 테스트)
4. build-and-test/ 하위 디렉터리에 안내 파일 생성: build-instructions.md, unit-test-instructions.md, integration-test-instructions.md, performance-test-instructions.md, build-and-test-summary.md
5. **명시적 승인 대기**: 다음과 같이 질문하세요: "**Build and test instructions complete. Ready to proceed to Operations stage?**" — 사용자가 확인할 때까지 진행하지 마세요.
6. **MANDATORY**: 사용자 응답을 audit.md에 완전한 원본 입력 그대로 로깅

---

# 🟡 OPERATIONS PHASE (오퍼레이션 단계)

**목적**: 향후 배포 및 모니터링 워크플로우를 위한 플레이스홀더

**초점**: 어떻게 **배포(DEPLOY)** 하고 **운영(RUN)** 할지 (향후 확장)

**OPERATIONS PHASE의 스테이지**:
- Operations (PLACEHOLDER)

---

## Operations (PLACEHOLDER)

**상태**: 이 스테이지는 현재 향후 확장을 위한 플레이스홀더입니다.

Operations 스테이지에는 최종적으로 다음이 포함될 예정입니다:
- 배포 기획 및 실행
- 모니터링과 옵저버빌리티 셋업
- 인시던트 대응 절차
- 유지보수 및 지원 워크플로우
- 프로덕션 준비 체크리스트

**현재 상태**: 모든 빌드/테스트 활동은 CONSTRUCTION 단계에서 처리됩니다.

## 핵심 원칙

- **Adaptive Execution (적응형 실행)**: 가치를 더하는 스테이지만 실행
- **Transparent Planning (투명한 기획)**: 시작 전에 항상 실행 계획을 제시
- **User Control (사용자 통제)**: 사용자가 스테이지의 포함/제외를 요청할 수 있음
- **Progress Tracking (진행 상태 추적)**: 실행/건너뛴 스테이지를 aidlc-state.md에 업데이트
- **Complete Audit Trail (완전한 감사 추적)**: 모든 사용자 입력과 AI 응답을 타임스탬프와 함께 audit.md에 로깅
  - **CRITICAL**: 사용자의 **완전한 원본 입력**을 제공된 그대로 캡처할 것
  - **CRITICAL**: 감사 로그에서 사용자 입력을 요약하거나 의역하지 말 것
  - **CRITICAL**: 승인뿐만 아니라 모든 상호작용을 로깅할 것
- **Quality Focus (품질 중시)**: 복잡한 변경은 완전한 처리를, 단순한 변경은 효율적인 처리를 받음
- **Content Validation (콘텐츠 검증)**: 파일 생성 전 항상 content-validation.md 룰에 따라 검증
- **NO EMERGENT BEHAVIOR (즉흥적 동작 금지)**: Construction 단계의 각 스테이지는 해당 룰 파일에 정의된 표준화된 2-옵션 완료 메시지를 반드시 사용해야 합니다. 3-옵션 메뉴나 그 밖의 즉흥적인 내비게이션 패턴을 만들지 마세요.

## MANDATORY: 계획 단위 체크박스 강제 (Plan-Level Checkbox Enforcement)

### 계획 실행을 위한 필수 룰
1. **계획 체크박스를 업데이트하지 않은 채로는 어떤 작업도 완료하지 마세요**
2. **계획 파일에 기술된 모든 단계는 완료 즉시 [x]로 표시하세요**
3. **이 작업은 작업이 완료된 동일한 상호작용 안에서 일어나야 합니다**
4. **예외 없음**: 모든 계획 단계의 완료는 반드시 체크박스 업데이트로 추적되어야 합니다.

### 두 단계 체크박스 추적 시스템
- **Plan-Level (계획 단위)**: 각 스테이지 내부의 상세 실행 진행 상태를 추적
- **Stage-Level (스테이지 단위)**: aidlc-state.md에서 전체 워크플로우 진행 상태를 추적
- **즉시 업데이트**: 모든 진행 업데이트는 작업이 완료된 동일한 상호작용 안에서

## 프롬프트 로깅 요구사항
- **MANDATORY**: 모든 사용자 입력(프롬프트, 질문, 응답)을 타임스탬프와 함께 audit.md에 로깅
- **MANDATORY**: 사용자의 완전한 원본 입력을 제공된 그대로 캡처 (절대 요약 금지)
- **MANDATORY**: 사용자에게 묻기 전에 모든 승인 프롬프트를 타임스탬프와 함께 로깅
- **MANDATORY**: 받은 모든 사용자 응답을 타임스탬프와 함께 기록
- **CRITICAL**: audit.md 파일에는 항상 변경 사항을 추가(APPEND/EDIT)하세요 — 내용 전체를 덮어쓰는 도구/명령은 절대 사용하지 마세요.
- **CRITICAL**: audit.md의 전체 내용을 덮어쓰는 파일 쓰기 도구/명령은 절대 사용하지 마세요 — 중복을 유발합니다.
- 타임스탬프에는 ISO 8601 포맷(YYYY-MM-DDTHH:MM:SSZ)을 사용
- 각 항목에 스테이지 컨텍스트를 포함

### 감사 로그 포맷:
```markdown
## [스테이지 이름 또는 상호작용 유형]
**Timestamp**: [ISO timestamp]
**User Input**: "[사용자의 완전한 원본 입력 — 절대 요약하지 않음]"
**AI Response**: "[AI의 응답 또는 수행한 동작]"
**Context**: [스테이지, 동작, 또는 내려진 결정]

---
```

### audit.md 도구 사용법

✅ 올바른 방법:

1. audit.md 파일을 읽음
2. 변경을 위해 파일에 Append/Edit 수행

❌ 잘못된 방법:

1. audit.md 파일을 읽음
2. 읽은 내용과 추가하려는 새 변경을 합쳐서 audit.md 전체를 완전히 덮어씀

## 디렉터리 구조

```text
<WORKSPACE-ROOT>/                   # ⚠️ 애플리케이션 코드는 여기에
├── [project-specific structure]    # 프로젝트마다 다름 (code-generation.md 참고)
│
├── aidlc-docs/                     # 📄 문서 전용
│   ├── inception/                  # 🔵 INCEPTION PHASE
│   │   ├── plans/
│   │   ├── reverse-engineering/    # Brownfield 전용
│   │   ├── requirements/
│   │   ├── user-stories/
│   │   └── application-design/
│   ├── construction/               # 🟢 CONSTRUCTION PHASE
│   │   ├── plans/
│   │   ├── {unit-name}/
│   │   │   ├── functional-design/
│   │   │   ├── nfr-requirements/
│   │   │   ├── nfr-design/
│   │   │   ├── infrastructure-design/
│   │   │   └── code/               # 마크다운 요약만
│   │   └── build-and-test/
│   ├── operations/                 # 🟡 OPERATIONS PHASE (플레이스홀더)
│   ├── aidlc-state.md
│   └── audit.md
```

**CRITICAL RULE (필수 룰)**:
- 애플리케이션 코드: 워크스페이스 루트 (aidlc-docs/에 절대 두지 마세요)
- 문서: aidlc-docs/에만
- 프로젝트 구조: 프로젝트 유형별 패턴은 code-generation.md 참고
