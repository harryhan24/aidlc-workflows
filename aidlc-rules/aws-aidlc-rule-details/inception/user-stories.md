# User Stories - 상세 단계

## 목적
**요구사항을 수락 기준(acceptance criteria)이 있는 사용자 중심 스토리로 변환**

User Stories는 다음에 집중합니다:
- 비즈니스 요구사항을 사용자 중심 내러티브로 번역
- 각 스토리에 명확한 수락 기준 정의
- 다양한 이해관계자 유형을 대표하는 사용자 페르소나(persona) 생성
- 팀 간 공통 이해 수립
- 구현을 위한 테스트 가능한 명세 제공

## 전제 조건
- Workspace Detection이 완료되어야 함
- Requirements Analysis 권장 (가능 시 요구사항을 참조)
- Workflow Planning이 User Stories 스테이지의 실행을 표시하고 있어야 함

## 지능형 평가 가이드라인

**언제 User Stories를 실행할까**: 진행 전 다음 향상된 평가를 사용하세요:

### 높은 우선순위 실행 (ALWAYS 실행)
- **New User Features**: 사용자가 직접 상호작용할 새 기능
- **User Experience Changes**: 기존 사용자 워크플로우/인터페이스 수정
- **Multi-Persona Systems**: 여러 유형의 사용자를 위한 애플리케이션
- **Customer-Facing APIs**: 외부 사용자/시스템이 사용하는 서비스
- **Complex Business Logic**: 여러 시나리오 또는 비즈니스 룰을 가진 요구사항
- **Cross-Team Projects**: 여러 팀의 공통 이해가 필요한 작업

### 중간 우선순위 실행 (복잡도 평가)
- **Backend User Impact**: 사용자 경험에 간접 영향을 주는 내부 변경
- **Performance Improvements**: 사용자에게 보이는 효과가 있는 개선
- **Integration Work**: 사용자 워크플로우에 영향을 주는 시스템 연결
- **Data Changes**: 사용자 데이터/리포트/분석에 영향을 주는 수정
- **Security Enhancements**: 사용자 인증/권한에 영향을 주는 변경

### 복잡도 평가 요인
중간 우선순위 케이스에서, 다음 중 어느 하나라도 해당되면 user stories를 실행:
- **Scope**: 변경이 여러 컴포넌트나 사용자 접점에 걸침
- **Ambiguity**: 요구사항에 스토리로 명확화할 수 있는 모호한 측면이 있음
- **Risk**: 비즈니스 영향이 크거나 오해 가능성이 있음
- **Stakeholders**: 요구사항에 여러 비즈니스 이해관계자가 관여
- **Testing**: 사용자 수락 테스트가 필요할 것임
- **Options**: 유효한 구현 접근이 여러 가지 존재

### 단순 케이스에만 건너뜀
- **Pure Refactoring**: 사용자 영향이 전혀 없는 내부 코드 개선
- **Isolated Bug Fixes**: 명확한 범위의 단순하고 잘 정의된 수정
- **Infrastructure Only**: 사용자에게 보이는 영향이 없는 변경
- **Developer Tooling**: 빌드 프로세스, CI/CD, 개발 환경 변경
- **Documentation**: 기능에 영향이 없는 업데이트

### 기본 결정 규칙
**애매할 때는 user stories를 포함하고 명확화 질문을 하세요.** 적절한 명확화와 함께 종합 스토리를 만드는 오버헤드는 대개 다음 이점에 의해 상쇄됩니다:
- 더 명확한 요구사항 이해
- 더 나은 팀 정렬
- 개선된 테스트 기준
- 향상된 이해관계자 커뮤니케이션
- 구현 리스크 감소
- 개발 중 값비싼 변경 감소
- 더 나은 사용자 경험 결과

---

# PART 1: PLANNING

## Step 1: User Stories의 필요성 검증 (MANDATORY)

**CRITICAL**: user stories로 진행하기 전에 다음 평가를 수행하세요:

### 평가 절차
1. **요청 컨텍스트 분석**:
   - 원 사용자 요청과 요구사항을 검토
   - 사용자 대상 변경 vs 내부 전용 변경을 식별
   - 작업의 복잡도와 범위 평가
   - 비즈니스 이해관계자의 관여도 평가

2. **평가 기준 적용**:
   - High Priority 지표(항상 실행)와 대조
   - Medium Priority 요인(복잡도 기반 결정) 평가
   - 건너뛰어야 할 단순 케이스가 아님을 확인

3. **평가 결정 문서화**:
   - `aidlc-docs/inception/plans/user-stories-assessment.md` 생성
   - 이 요청에 user stories가 왜 가치 있는지 사유 포함
   - 해당하는 구체적 평가 기준 참조
   - 기대 이익(명확성, 테스트, 이해관계자 정렬) 설명

4. **정당화될 때만 진행**:
   - User stories가 프로젝트에 분명한 가치를 더해야 함
   - 평가가 구체적 이익이 오버헤드를 능가함을 보여야 함
   - 결정은 프로젝트 이해관계자에게 방어 가능해야 함

### 평가 문서 템플릿
```markdown
# User Stories Assessment

## Request Analysis
- **Original Request**: [간단 요약]
- **User Impact**: [Direct/Indirect/None]
- **Complexity Level**: [Simple/Medium/Complex]
- **Stakeholders**: [관련자 목록]

## Assessment Criteria Met
- [ ] High Priority: [해당 기준 목록]
- [ ] Medium Priority: [복잡도 정당화와 함께 해당 기준]
- [ ] Benefits: [user stories에서 기대되는 가치]

## Decision
**Execute User Stories**: [Yes/No]
**Reasoning**: [상세 정당화]

## Expected Outcomes
- [user stories가 제공할 구체적 이익 목록]
- [스토리가 프로젝트 성공을 어떻게 개선하는지]
```

## Step 2: 스토리 계획 작성
- 프로덕트 오너의 역할을 가정
- 스토리 개발을 위한 단계별 실행 체크리스트가 있는 종합 계획 생성
- 각 단계/하위 단계에 체크박스 []
- 요구사항을 user stories로 변환하는 방법론과 접근에 집중

## Step 3: 컨텍스트에 맞는 질문 생성
**DIRECTIVE**: 요구사항과 컨텍스트를 철저히 분석해 스토리 품질과 팀 이해를 높일 수 있는 영역을 모두 식별합니다. 종합적인 user story 개발을 위해 사전적으로 질문하세요.

**CRITICAL**: 스토리 품질에 영향을 줄 수 있는 모호함이나 누락이 있으면 기본적으로 질문하세요. 불완전하거나 불명확한 스토리를 만드느니 질문을 많이 하는 편이 낫습니다.

**질문 포맷팅 룰은 `common/question-format-guide.md` 참고**

- 질문은 [Answer]: 태그 포맷으로 임베드
- 모호함, 누락 정보, 명확화가 필요한 영역에 집중
- 사용자 입력이 스토리 작성 결정을 개선할 수 있는 곳이면 어디든 질문
- **애매할 때는 질문하세요** — 과신은 부실한 스토리로 이어집니다.

**평가할 질문 카테고리** (모든 카테고리 고려):
- **User Personas** — 사용자 유형, 역할, 특성, 동기에 대해 질문
- **Story Granularity** — 적절한 세부 수준, 스토리 크기, 분해 접근에 대해 질문
- **Story Format** — 포맷 선호, 템플릿 사용, 문서 표준에 대해 질문
- **Breakdown Approach** — 조직 방식, 우선순위, 그룹핑 전략에 대해 질문
- **Acceptance Criteria** — 세부 수준, 포맷, 테스트 접근, 검증 방식에 대해 질문
- **User Journeys** — 사용자 워크플로우, 상호작용 패턴, 경험 흐름에 대해 질문
- **Business Context** — 비즈니스 목표, 성공 지표, 이해관계자 요구에 대해 질문
- **Technical Constraints** — 기술적 한계, 통합 요구사항, 시스템 경계에 대해 질문

## Step 4: 필수 스토리 아티팩트를 계획에 포함
- 다음 필수 아티팩트들을 스토리 계획에 **항상** 포함:
  - [ ] INVEST 기준을 따르는 user stories로 stories.md 생성
  - [ ] 사용자 원형과 특성을 담은 personas.md 생성
  - [ ] 스토리가 Independent, Negotiable, Valuable, Estimable, Small, Testable 한지 확인
  - [ ] 각 스토리에 수락 기준 포함
  - [ ] 페르소나를 관련 user stories에 매핑

## Step 5: 스토리 옵션 제시
- 스토리 분해를 위한 다양한 접근을 계획 문서에 포함:
  - **User Journey-Based**: 스토리가 사용자 워크플로우/상호작용을 따름
  - **Feature-Based**: 시스템 기능과 역량을 중심으로 스토리 조직
  - **Persona-Based**: 다양한 사용자 유형과 그 요구로 스토리 그룹화
  - **Domain-Based**: 비즈니스 도메인/컨텍스트로 스토리 조직
  - **Epic-Based**: 하위 스토리를 가진 계층적 epic 구조
- 각 접근의 트레이드오프와 이익 설명
- 명확한 결정 기준이 있는 하이브리드 접근 허용

## Step 6: 스토리 계획 저장
- 임베드된 질문이 있는 완전한 스토리 계획을 `aidlc-docs/inception/plans/` 디렉터리에 저장
- 파일명: `story-generation-plan.md`
- 사용자 입력용 [Answer]: 태그 모두 포함
- 계획이 종합적이며 스토리 개발의 모든 측면을 다루는지 확인

## Step 7: 사용자 입력 요청
- 사용자가 스토리 계획 문서의 [Answer]: 태그에 직접 답을 채우도록 요청
- 감사 추적과 결정 문서화의 중요성을 강조
- [Answer]: 태그 작성법에 대한 명확한 안내 제공
- 진행 전에 모든 질문에 답해야 함을 설명

## Step 8: 답변 수집
- 사용자가 문서의 [Answer]: 태그로 모든 질문에 답할 때까지 대기
- 모든 [Answer]: 태그가 채워질 때까지 진행하지 마세요
- [Answer]: 태그 중 비어 있는 것이 없도록 문서를 검토

## Step 9: 답변 분석 (MANDATORY)
진행 전에 모든 사용자 답변을 다음 관점에서 꼼꼼히 검토해야 합니다:
- **모호하거나 애매한 응답**: "mix of", "somewhere between", "not sure", "depends", "maybe", "probably"
- **정의되지 않은 기준/용어**: 명확한 정의 없는 개념 참조
- **모순된 답변**: 서로 상충하는 응답
- **누락된 생성 세부**: 구현을 위한 구체적 가이드가 부족한 답변
- **옵션을 섞은 답변**: 명확한 결정 규칙 없이 여러 접근을 섞은 응답
- **불완전한 설명**: 외부 요인을 정의 없이 참조하는 답변
- **가정 기반 응답**: 명시되지 않은 지식을 가정한 답변

## Step 10: 후속 질문 (MANDATORY)
Step 9의 분석에서 모호한 답변이 발견되면, 다음을 반드시 수행해야 합니다:
- [Answer]: 태그를 사용한 별도의 명확화 질문 파일 생성
- **모든** 모호성이 완전히 해소될 때까지 승인으로 진행하지 마세요
- **CRITICAL**: 철저히 — 불명확한 응답마다 후속 질문을 하세요
- 필요한 후속 질문 예시:
  - "A와 B의 혼합이라고 하셨는데, 언제 A를 쓰고 언제 B를 쓸지 구체적 기준은 무엇인가요?"
  - "A와 B 사이라고 하셨는데, 정확한 중간 지점 접근을 정의해 주실 수 있나요?"
  - "잘 모르겠다고 하셨는데, 결정에 도움이 될 추가 정보는 무엇인가요?"
  - "복잡도에 따라 다르다고 하셨는데, 복잡도 수준과 임계치를 어떻게 정의하시나요?"
  - "하이브리드 접근을 선택하셨는데, 어느 방법을 언제 쓸지에 대한 구체적 룰은 무엇인가요?"
  - "아마 X라고 하셨는데, 어떤 요인이 있으면 확실히 X이고 어떤 요인이 있으면 확실히 X가 아닌가요?"
  - "'표준 관행'을 언급하셨는데, 그 표준 관행이 무엇인지 정의해 주실 수 있나요?"

## Step 11: 구현 세부는 피할 것
- 우선순위나 개발 작업이 아니라 스토리 작성 방법론에 집중
- 이 단계에서 기술적 생성을 논의하지 마세요
- 개발 일정이나 스프린트 기획을 만들지 마세요
- 스토리 구조와 포맷 결정에 초점 유지

## Step 12: 승인 프롬프트 로깅
- 승인을 요청하기 전, 프롬프트를 타임스탬프와 함께 `aidlc-docs/audit.md`에 로깅
- 완전한 승인 프롬프트 텍스트 포함
- ISO 8601 타임스탬프 포맷 사용

## Step 13: 계획에 대한 명시적 승인 대기
- 사용자가 명시적으로 스토리 접근을 승인할 때까지 진행하지 마세요
- 승인은 명확하고 모호하지 않아야 함
- 사용자가 변경을 요청하면 계획을 업데이트하고 승인 과정을 반복

## Step 14: 승인 응답 기록
- 사용자의 승인 응답을 타임스탬프와 함께 `aidlc-docs/audit.md`에 로깅
- 정확한 사용자 응답 텍스트 포함
- 승인 상태를 명확히 표시

---

# PART 2: GENERATION

## Step 15: 스토리 생성 계획 로드
- [ ] `aidlc-docs/inception/plans/story-generation-plan.md`에서 전체 계획을 읽음
- [ ] 다음 미완료 단계 식별 (첫 [ ] 체크박스)
- [ ] 해당 단계의 컨텍스트와 요구사항 로드

## Step 16: 현재 단계 실행
- [ ] 현재 단계가 기술하는 바를 정확히 수행
- [ ] 계획에 명시된 대로 스토리 아티팩트 생성
- [ ] Planning에서 승인된 방법론과 포맷을 따름
- [ ] 계획에 명시된 스토리 분해 접근을 사용

## Step 17: 진행 상태 업데이트
- [ ] 스토리 생성 계획에서 완료된 단계를 [x]로 표시
- [ ] `aidlc-docs/aidlc-state.md`의 current status 업데이트
- [ ] 생성된 모든 아티팩트 저장

## Step 18: 계속 또는 완료
- [ ] 남은 단계가 있으면 Step 15로 돌아감
- [ ] 모든 단계가 완료되면 스토리가 다음 스테이지에 준비되었는지 검증
- [ ] 모든 필수 아티팩트가 생성되었는지 확인

## Step 19: 승인 프롬프트 로깅
- 승인을 요청하기 전, 프롬프트를 타임스탬프와 함께 `aidlc-docs/audit.md`에 로깅
- 완전한 승인 프롬프트 텍스트 포함
- ISO 8601 타임스탬프 포맷 사용

## Step 20: 완료 메시지 제시
- 다음 구조로 완료 메시지를 제시:
     1. **완료 알림** (필수): 항상 다음으로 시작:

```markdown
# 📚 User Stories Complete
```

     2. **AI Summary** (선택): 생성된 스토리를 구조화된 불릿으로 요약 제공
        - 형식: "User stories generation has created [description]:"
        - 생성된 핵심 페르소나(불릿)
        - 생성된 user stories와 그 개수, 조직 방식
        - 스토리 구조와 준수(INVEST 기준, 수락 기준) 언급
        - 워크플로우 안내 문구는 **포함하지 마세요** ("please review", "let me know", "proceed to next phase", "before we proceed")
        - 사실/내용 중심으로 유지
     3. **포맷팅된 워크플로우 메시지** (필수): 항상 정확히 이 형식으로 끝맺으세요:

```markdown
> **📋 <u>**REVIEW REQUIRED:**</u>**  
> Please examine the user stories and personas at: `aidlc-docs/inception/user-stories/stories.md` and `aidlc-docs/inception/user-stories/personas.md`



> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** -  Ask for modifications to the stories or personas based on your review  
> ✅ **Approve & Continue** - Approve user stories and proceed to **Workflow Planning**

---
```

## Step 21: 생성된 스토리에 대한 명시적 승인 대기
- 사용자가 명시적으로 생성된 스토리를 승인할 때까지 진행하지 마세요
- 승인은 명확하고 모호하지 않아야 함
- 사용자가 변경을 요청하면 스토리를 업데이트하고 승인 과정을 반복

## Step 22: 승인 응답 기록
- 사용자의 승인 응답을 타임스탬프와 함께 `aidlc-docs/audit.md`에 로깅
- 정확한 사용자 응답 텍스트 포함
- 승인 상태를 명확히 표시

## Step 23: 진행 상태 업데이트
- `aidlc-state.md`에서 User Stories 스테이지를 완료로 표시
- "Current Status" 섹션 업데이트
- 다음 스테이지로의 전환 준비

---

# 핵심 룰

## Planning 단계 룰
- **CONTEXT-APPROPRIATE QUESTIONS**: 이 구체적 컨텍스트에 관련된 질문만 하세요.
- **MANDATORY ANSWER ANALYSIS**: 진행 전에 항상 답변에서 모호성을 분석하세요.
- **NO PROCEEDING WITH AMBIGUITY**: 생성 전에 모든 모호한 답변을 해소해야 합니다.
- **EXPLICIT APPROVAL REQUIRED**: 생성 시작 전 사용자가 계획을 승인해야 합니다.

## Generation 단계 룰
- **NO HARDCODED LOGIC**: 스토리 생성 계획에 적힌 내용만 실행
- **FOLLOW PLAN EXACTLY**: 단계 순서에서 벗어나지 마세요.
- **UPDATE CHECKBOXES**: 각 단계 완료 즉시 [x]로 표시
- **USE APPROVED METHODOLOGY**: Planning에서의 스토리 접근을 따름
- **VERIFY COMPLETION**: 진행 전에 모든 스토리 아티팩트가 완전한지 확인

## 완료 기준
- 모든 기획 질문에 답이 있고 모호성이 해소됨
- 스토리 계획이 사용자에 의해 명시적으로 승인됨
- 스토리 생성 계획의 모든 단계가 [x]로 표시됨
- 모든 스토리 아티팩트가 계획대로 생성됨 (stories.md, personas.md)
- 생성된 스토리가 사용자에 의해 명시적으로 승인됨
- 스토리가 검증되어 다음 스테이지에 준비 완료
