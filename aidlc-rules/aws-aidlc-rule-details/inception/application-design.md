# Application Design - 상세 단계

## 목적
**상위 컴포넌트 식별과 서비스 레이어 설계**

Application Design은 다음에 집중합니다:
- 주요 기능 컴포넌트와 그 책임 식별
- 컴포넌트 인터페이스 정의 (상세 비즈니스 로직 아님)
- 오케스트레이션을 위한 서비스 레이어 설계
- 컴포넌트 의존성과 통신 패턴 수립

**참고**: 상세 비즈니스 로직 설계는 이후 Functional Design(단위별, CONSTRUCTION 단계)에서 진행됩니다.

## 전제 조건
- Workspace Detection이 완료되어야 함
- Requirements Analysis 권장 (기능적 맥락 제공)
- User Stories 권장 (사용자 스토리가 설계 결정을 안내)
- 실행 계획에 Application Design 스테이지가 실행되도록 표시되어 있어야 함

## 단계별 실행

### 1. 컨텍스트 분석
- `aidlc-docs/inception/requirements/requirements.md`와 `aidlc-docs/inception/user-stories/stories.md`를 읽음
- 핵심 비즈니스 역량과 기능 영역 식별
- 설계 범위와 복잡도 결정

### 2. Application Design 계획 작성
- 애플리케이션 설계를 위한 체크박스 [] 가 포함된 계획 생성
- 컴포넌트, 책임, 메서드, 비즈니스 룰, 서비스에 집중
- 각 단계/하위 단계에 체크박스 [] 가 있어야 함

### 3. 필수 설계 아티팩트를 계획에 포함
- 다음 필수 아티팩트들을 설계 계획에 **항상** 포함:
  - [ ] components.md 생성 — 컴포넌트 정의와 상위 책임
  - [ ] component-methods.md 생성 — 메서드 시그니처 (비즈니스 룰 세부는 이후 Functional Design에서)
  - [ ] services.md 생성 — 서비스 정의와 오케스트레이션 패턴
  - [ ] component-dependency.md 생성 — 의존 관계와 통신 패턴
  - [ ] 설계의 완전성과 일관성 검증

### 4. 컨텍스트에 맞는 질문 생성
**DIRECTIVE**: 요구사항과 스토리를 분석해 이 구체적 애플리케이션 설계에 관련된 질문을 생성합니다. 아래 카테고리를 가이드로 사용하세요. 각 카테고리를 평가하되, 적용 가능성이 애매하면 질문을 하세요 — 과신은 나쁜 결과로 이어집니다(overconfidence-prevention.md 참고).

- 질문은 [Answer]: 태그 포맷으로 임베드
- 모호함, 누락 정보, 명확화가 필요한 영역에 집중
- 사용자 입력이 설계 결정을 개선할 수 있는 곳이면 어디든 질문 생성
- **애매할 때는 질문하세요** — 과신은 부실한 설계로 이어집니다.

**평가할 질문 카테고리** (모든 카테고리 고려):
- **Component Identification** — 컴포넌트 경계, 조직, 그룹핑 전략에 대해 질문
- **Component Methods** — 메서드 시그니처, 입력/출력 기대, 인터페이스 계약에 대해 질문 (상세 비즈니스 룰은 이후)
- **Service Layer Design** — 서비스 오케스트레이션, 경계, 조율 패턴에 대해 질문
- **Component Dependencies** — 통신 패턴, 의존성 관리, 결합도 이슈에 대해 질문
- **Design Patterns** — 아키텍처 스타일 선호, 패턴 선택, 설계 제약에 대해 질문

### 5. Application Design 계획 저장
- `aidlc-docs/inception/plans/application-design-plan.md`에 저장
- 사용자 입력용 [Answer]: 태그 모두 포함
- 계획이 모든 설계 측면을 다루는지 확인

### 6. 사용자 입력 요청
- 사용자가 계획 문서의 [Answer]: 태그에 직접 답을 채우도록 요청
- 설계 결정의 중요성을 강조
- [Answer]: 태그 작성법에 대한 명확한 안내 제공

### 7. 답변 수집
- 사용자가 문서의 [Answer]: 태그로 모든 질문에 답할 때까지 대기
- 모든 [Answer]: 태그가 채워질 때까지 진행하지 마세요
- [Answer]: 태그 중 비어 있는 것이 없도록 문서를 검토

### 8. 답변 분석 (MANDATORY)
진행 전에 모든 사용자 답변을 다음 관점에서 꼼꼼히 검토해야 합니다:
- **모호하거나 애매한 응답**: "mix of", "somewhere between", "not sure", "depends"
- **정의되지 않은 기준/용어**: 명확한 정의 없는 개념 참조
- **모순된 답변**: 서로 상충하는 응답
- **누락된 설계 세부**: 구체적 가이드가 부족한 답변
- **옵션을 섞은 답변**: 명확한 결정 규칙 없이 여러 접근을 섞은 응답

### 9. 후속 질문 (MANDATORY)
Step 8의 분석에서 모호한 답변이 발견되면, 다음을 반드시 수행해야 합니다:
- 계획 문서에 [Answer]: 태그를 사용한 구체적 후속 질문 추가
- 모든 모호성이 해소될 때까지 승인으로 진행하지 마세요
- 필요한 후속 질문 예시:
  - "A와 B의 혼합이라고 하셨는데, 언제 A를 쓰고 언제 B를 쓸지 구체적 기준은 무엇인가요?"
  - "A와 B 사이라고 하셨는데, 정확한 중간 지점 접근 방식을 정의해 주실 수 있나요?"
  - "잘 모르겠다고 하셨는데, 결정에 도움이 될 추가 정보는 무엇인가요?"
  - "복잡도에 따라 다르다고 하셨는데, 복잡도 수준을 어떻게 정의하시나요?"

### 10. Application Design 아티팩트 생성
- 승인된 계획을 실행해 설계 아티팩트 생성
- `aidlc-docs/inception/application-design/components.md`에 다음을 포함해 생성:
  - 컴포넌트 이름과 목적
  - 컴포넌트 책임
  - 컴포넌트 인터페이스
- `aidlc-docs/inception/application-design/component-methods.md`에 다음을 포함해 생성:
  - 각 컴포넌트의 메서드 시그니처
  - 각 메서드의 상위 목적
  - 입력/출력 유형
  - 참고: 상세 비즈니스 룰은 Functional Design(단위별, CONSTRUCTION 단계)에서 정의됩니다.
- `aidlc-docs/inception/application-design/services.md`에 다음을 포함해 생성:
  - 서비스 정의
  - 서비스 책임
  - 서비스 상호작용과 오케스트레이션
- `aidlc-docs/inception/application-design/component-dependency.md`에 다음을 포함해 생성:
  - 관계를 보여주는 의존성 매트릭스
  - 컴포넌트 간 통신 패턴
  - 데이터 흐름 다이어그램
- 위에서 만든 여러 설계 문서를 하나로 통합한 `aidlc-docs/inception/application-design/application-design.md`를 생성.

### 11. 승인 로깅
- 승인 프롬프트를 타임스탬프와 함께 `aidlc-docs/audit.md`에 로깅
- 완전한 승인 프롬프트 텍스트 포함
- ISO 8601 타임스탬프 포맷 사용

### 12. 완료 메시지 제시

```markdown
# 🏗️ Application Design Complete

[AI가 생성한 application design 아티팩트 요약을 불릿으로]

> **📋 <u>**REVIEW REQUIRED:**</u>**  
> Please examine the application design artifacts at: `aidlc-docs/inception/application-design/`

> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - Ask for modifications to the application design if required
> [IF Units Generation is skipped:]
> 📝 **Add Units Generation** - Choose to include **Units Generation** stage (currently skipped)
> ✅ **Approve & Continue** - Approve design and proceed to **[Units Generation/CONSTRUCTION PHASE]**
```

### 13. 명시적 승인 대기
- 사용자가 명시적으로 application design을 승인할 때까지 진행하지 마세요.
- 승인은 명확하고 모호하지 않아야 합니다.
- 사용자가 변경을 요청하면 설계를 업데이트하고 승인 과정을 반복합니다.

### 14. 승인 응답 기록
- 사용자의 승인 응답을 타임스탬프와 함께 `aidlc-docs/audit.md`에 로깅
- 정확한 사용자 응답 텍스트 포함
- 승인 상태를 명확히 표시

### 15. 진행 상태 업데이트
- `aidlc-docs/aidlc-state.md`에서 Application Design 스테이지를 완료로 표시
- "Current Status" 섹션 업데이트
- 다음 스테이지로의 전환을 준비
