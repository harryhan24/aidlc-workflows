# Requirements Analysis (Adaptive, 요구사항 분석)

**역할 가정**: 프로덕트 오너(product owner)의 역할을 맡으세요.

**적응형 단계 (Adaptive Phase)**: 항상 실행됩니다. 세부 수준은 문제 복잡도에 따라 달라집니다.

**적응형 깊이 설명은 [depth-levels.md](../common/depth-levels.md) 참고.**

## 전제 조건
- Workspace Detection이 완료되어야 함
- (brownfield인 경우) Reverse Engineering이 완료되어야 함

## 실행 단계

### Step 1: 리버스 엔지니어링 컨텍스트 로드 (가능한 경우)

**brownfield 프로젝트라면**:
- `aidlc-docs/inception/reverse-engineering/architecture.md` 로드
- `aidlc-docs/inception/reverse-engineering/component-inventory.md` 로드
- `aidlc-docs/inception/reverse-engineering/technology-stack.md` 로드
- 요청을 분석할 때 기존 시스템을 이해하는 데 사용

### Step 2: 사용자 요청 분석 (Intent Analysis)

#### 2.1 요청 명확성
- **Clear (명확)**: 구체적이고 잘 정의되어 있으며 실행 가능
- **Vague (모호)**: 일반적, 모호함, 명확화 필요
- **Incomplete (불완전)**: 핵심 정보 누락

#### 2.2 요청 유형
- **New Feature**: 새 기능 추가
- **Bug Fix**: 기존 이슈 수정
- **Refactoring**: 코드 구조 개선
- **Upgrade**: 의존성 또는 프레임워크 업데이트
- **Migration**: 다른 기술로 이전
- **Enhancement**: 기존 기능 개선
- **New Project**: 처음부터 시작

#### 2.3 초기 범위 추정
- **Single File**: 한 파일에만 변경
- **Single Component**: 한 컴포넌트/패키지에 변경
- **Multiple Components**: 여러 컴포넌트에 걸친 변경
- **System-wide**: 시스템 전체에 영향을 주는 변경
- **Cross-system**: 여러 시스템에 영향을 주는 변경

#### 2.4 초기 복잡도 추정
- **Trivial**: 단순하고 직관적인 변경
- **Simple**: 구현 경로가 명확
- **Moderate**: 일정 수준의 복잡도, 여러 고려사항
- **Complex**: 상당한 복잡도, 많은 고려사항

### Step 3: 요구사항 깊이 결정

**요청 분석을 기반으로 깊이를 결정합니다:**

**Minimal Depth** - 다음일 때 사용:
- 요청이 명확하고 단순함
- 상세 요구사항이 필요 없음
- 기본 이해만 문서화

**Standard Depth** - 다음일 때 사용:
- 요청에 명확화가 필요
- 기능/비기능 요구사항이 필요
- 보통 복잡도

**Comprehensive Depth** - 다음일 때 사용:
- 이해관계자가 여러 명인 복잡한 프로젝트
- 고위험 또는 중요(critical) 시스템
- 추적성을 갖춘 상세 요구사항이 필요

### Step 4: 현재 요구사항 평가

사용자가 제공한 모든 것을 분석합니다:
   - 의도 표현 또는 설명 (이미 audit.md에 로깅되어 있음)
   - 기존 요구사항 문서 (언급되었다면 워크스페이스 검색)
   - 붙여넣은 콘텐츠 또는 파일 참조
   - 마크다운이 아닌 문서는 마크다운 포맷으로 변환

### Step 5: 철저한 완전성 분석

**CRITICAL**: 요구사항의 완전성을 평가할 때 종합 분석을 사용합니다. 조금이라도 모호하거나 누락된 세부가 있으면 기본적으로 질문하세요.

**MANDATORY**: 아래 영역을 **모두** 평가하고, 불명확한 부분이 있으면 질문하세요:
- **Functional Requirements**: 핵심 기능, 사용자 상호작용, 시스템 동작
- **Non-Functional Requirements**: 성능, 보안, 확장성, 사용성
- **User Scenarios**: 유스케이스, 사용자 여정, 엣지 케이스, 에러 시나리오
- **Business Context**: 목표, 제약, 성공 기준, 이해관계자 요구
- **Technical Context**: 통합 지점, 데이터 요구사항, 시스템 경계
- **Quality Attributes**: 신뢰성, 유지보수성, 테스트 용이성, 접근성

**애매할 때는 질문하세요** — 불완전한 요구사항은 부실한 구현으로 이어집니다.

### Step 5.1: 익스텐션 Opt-In 프롬프트

**MANDATORY**: 워크플로우 시작 시 `extensions/` 하위 디렉터리에서 로드된 모든 `*.opt-in.md` 파일에서 `## Opt-In Prompt` 섹션을 스캔합니다. 그것을 선언한 각 익스텐션에 대해, Step 6에서 만드는 명확화 질문 파일에 해당 질문을 포함하세요. 각 opt-in 질문은 사용자와의 대화와 동일한 언어로 제시합니다.

답변을 받으면:
1. 각 익스텐션의 활성화 상태를 `aidlc-docs/aidlc-state.md`의 `## Extension Configuration`에 기록:

```markdown
## Extension Configuration
| Extension | Enabled | Decided At |
|---|---|---|
| [Extension Name] | [Yes/No] | Requirements Analysis |
```

2. **Deferred Rule Loading**: 사용자가 옵트인한 각 익스텐션에 대해, 이제 전체 룰 파일을 로드합니다. 룰 파일은 명명 규약으로 도출됩니다 — opt-in 파일명에서 `.opt-in.md`를 제거하고 `.md`를 붙입니다(예: `security-baseline.opt-in.md` → `security-baseline.md`). 사용자가 옵트아웃한 익스텐션은 전체 룰 파일을 로드하지 **마세요.**

### Step 6: 명확화 질문 생성 (사전적 접근)
   - 요구사항이 예외적으로 명확하고 완전한 경우가 아니라면 **항상** `aidlc-docs/inception/requirements/requirement-verification-questions.md`를 생성합니다.
   - 누락/불명확/모호한 영역에 대해 질문하세요.
   - 기능 요구사항, 비기능 요구사항, 사용자 시나리오, 비즈니스 맥락에 집중하세요.
   - 사용자에게 질문 문서의 [Answer]: 태그에 직접 답변을 채우도록 요청합니다.
   - 객관식 옵션을 제시하는 경우:
     - 옵션은 A, B, C, D 등으로 라벨링
     - 옵션은 상호 배타적이며 겹치지 않도록
     - 사용자 정의 응답을 위한 옵션을 항상 포함: "X) Other (please describe after [Answer]: tag below)"
   - 문서에서 사용자 답변을 기다립니다.
   - **MANDATORY**: 모든 답변을 모호성 관점에서 분석하고, 필요하면 후속 질문을 만드세요.
   - **MANDATORY**: 모든 모호성이 해소되거나 사용자가 명시적으로 진행을 요청할 때까지 계속 질문하세요.

### ⛔ GATE: 사용자 답변 대기
requirement-verification-questions.md의 모든 질문이 답변되고 검증될 때까지 Step 7로 진행하지 마세요.
질문 파일을 사용자에게 제시한 뒤 멈추세요.

### Step 7: 요구사항 문서 생성
   - **PREREQUISITE**: Step 6의 게이트가 통과되어야 합니다 — 모든 답변이 수신·분석되어야 함.
   - `aidlc-docs/inception/requirements/requirements.md`를 생성합니다.
   - 상단에 intent 분석 요약을 포함:
     - 사용자 요청
     - 요청 유형
     - 범위 추정
     - 복잡도 추정
   - 기능/비기능 요구사항을 모두 포함
   - 명확화 질문에 대한 사용자 답변을 반영
   - 핵심 요구사항의 간단한 요약 제공

### Step 8: 상태 추적 업데이트

`aidlc-docs/aidlc-state.md` 업데이트:

```markdown
## Stage Progress
### 🔵 INCEPTION PHASE
- [x] Workspace Detection
- [x] Reverse Engineering (해당 시)
- [x] Requirements Analysis
```

### Step 9: 로깅 후 진행
   - `aidlc-docs/audit.md`에 승인 프롬프트를 타임스탬프와 함께 로깅합니다.
   - 다음 구조로 완료 메시지를 제시:
     1. **완료 알림** (필수): 항상 다음으로 시작:

```markdown
# 🔍 Requirements Analysis Complete
```

     2. **AI Summary** (선택): 요구사항을 구조화된 불릿으로 요약 제공
        - 형식: "Requirements analysis has identified [project type/complexity]:"
        - 핵심 기능 요구사항을 불릿으로 나열
        - 핵심 비기능 요구사항을 불릿으로 나열
        - 관련 있다면 아키텍처 고려사항이나 기술 결정 언급
        - 워크플로우 안내 문구는 **포함하지 마세요** ("please review", "let me know", "proceed to next phase", "before we proceed")
        - 사실/내용 중심으로 유지
     3. **포맷팅된 워크플로우 메시지** (필수): 항상 정확히 이 형식으로 끝맺으세요:

```markdown
> **📋 <u>**REVIEW REQUIRED:**</u>**  
> Please examine the requirements document at: `aidlc-docs/inception/requirements/requirements.md`



> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** -  Ask for modifications to the requirements if required based on your review 
> [IF User Stories will be skipped, add this option:]
> 📝 **Add User Stories** - Choose to Include **User Stories** stage (currently skipped based on project simplicity)  
> ✅ **Approve & Continue** - Approve requirements and proceed to **[User Stories/Workflow Planning]**

---
```

**참고**: "Add User Stories" 옵션은 User Stories 스테이지가 건너뛰어질 때만 포함합니다. [User Stories/Workflow Planning] 부분은 실제 다음 스테이지 이름으로 치환하세요.

   - 진행 전에 명시적 사용자 승인을 기다립니다.
   - 승인 응답을 타임스탬프와 함께 기록합니다.
   - aidlc-state.md에서 Requirements Analysis 스테이지를 완료로 업데이트합니다.
