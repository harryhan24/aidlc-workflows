# Units Generation - 상세 단계

## 개요
이 스테이지는 시스템을 관리 가능한 작업 단위(unit of work)들로 분해하며, 통합된 두 파트로 구성됩니다:
- **Part 1 - Planning**: 질문이 포함된 분해 계획을 만들고, 답변을 수집하고, 모호성을 분석하고, 승인을 받습니다.
- **Part 2 - Generation**: 승인된 계획을 실행해 단위 아티팩트를 생성합니다.

**DEFINITION**: 작업 단위(unit of work)는 개발 목적의 스토리 논리 묶음입니다. 마이크로서비스의 경우 각 단위는 독립 배포 가능한 서비스가 됩니다. 모놀리스의 경우 단일 단위가 논리 모듈을 가진 애플리케이션 전체를 나타냅니다.

**Terminology**: 독립 배포 가능한 컴포넌트는 "Service", 서비스 내 논리 묶음은 "Module", 기획 맥락에서는 "Unit of Work"를 사용합니다.

## 전제 조건
- Workspace Detection이 완료되어야 함
- Requirements Analysis 권장 (기능 범위 제공)
- User Stories 권장 (스토리가 단위에 매핑됨)
- Application Design 스테이지 **필수** (컴포넌트, 메서드, 서비스 결정)
- 실행 계획이 Design 스테이지의 실행을 표시하고 있어야 함

---

# PART 1: PLANNING

## Step 1: Unit of Work 계획 작성
- 시스템을 작업 단위로 분해하기 위한 체크박스 [] 가 포함된 계획 생성
- 시스템을 관리 가능한 개발 단위로 쪼개는 데 집중
- 각 단계/하위 단계에 체크박스 [] 가 있어야 함

## Step 2: 필수 단위 아티팩트를 계획에 포함
다음 필수 아티팩트들을 단위 계획에 **항상** 포함:
- [ ] `aidlc-docs/inception/application-design/unit-of-work.md` 생성 — 단위 정의와 책임
- [ ] `aidlc-docs/inception/application-design/unit-of-work-dependency.md` 생성 — 의존성 매트릭스
- [ ] `aidlc-docs/inception/application-design/unit-of-work-story-map.md` 생성 — 스토리를 단위에 매핑
- [ ] **Greenfield 전용**: `unit-of-work.md`에 코드 조직 전략 문서화 (구조 패턴은 code-generation.md 참고)
- [ ] 단위 경계와 의존성 검증
- [ ] 모든 스토리가 단위에 배정되었는지 확인

## Step 3: 컨텍스트에 맞는 질문 생성
**DIRECTIVE**: 요구사항, 스토리, 애플리케이션 설계를 철저히 분석해 단위 분해 품질을 높일 수 있는 영역을 모두 식별합니다. 분해 관련 우려가 종합적으로 다뤄지도록 사전적으로 질문하세요.

**CRITICAL**: 단위 경계나 분해 품질에 영향을 줄 수 있는 모호함/누락이 있으면 기본적으로 질문하세요. 시스템 분해 방식에 대해 잘못된 가정을 하느니 질문을 많이 하는 편이 낫습니다.

**MANDATORY**: 아래 모든 카테고리를 평가하고, 각각에 대해 표적 질문을 합니다. 각 카테고리의 적용 가능성은 요구사항·스토리·애플리케이션 설계의 증거에 기반해 판단하세요 — 명시적 정당화 없이 카테고리를 건너뛰지 마세요:

- 질문은 [Answer]: 태그 포맷으로 임베드
- 모호함, 누락 정보, 명확화가 필요한 영역에 집중
- 사용자 입력이 분해 결정을 개선할 수 있는 곳이면 어디든 질문 생성
- **애매할 때는 질문하세요** — 과신은 부실한 단위 경계로 이어집니다.

**평가할 질문 카테고리** (모든 카테고리 고려):
- **Story Grouping** — 그룹핑 전략, 스토리 친화성, 논리적 클러스터링 접근에 대해 질문
- **Dependencies** — 통합 접근, 공유 리소스, 단위 간 통신 패턴에 대해 질문
- **Team Alignment** — 팀 구조, 소유권 경계, 협업 모델에 대해 질문
- **Technical Considerations** — 단위마다 다를 수 있는 확장성/배포 요구사항에 대해 질문
- **Business Domain** — 도메인 경계, bounded context, 비즈니스 역량 정렬에 대해 질문
- **Code Organization (Greenfield 멀티 유닛 전용)** — 배포 모델과 디렉터리 구조 선호에 대해 질문

## Step 4: UOW 계획 저장
- `aidlc-docs/inception/plans/unit-of-work-plan.md`로 저장
- 사용자 입력용 [Answer]: 태그 모두 포함
- 계획이 시스템 분해의 모든 측면을 다루는지 확인

## Step 5: 사용자 입력 요청
- 사용자가 계획 문서의 [Answer]: 태그에 직접 답을 채우도록 요청
- 분해 결정의 중요성을 강조
- [Answer]: 태그 작성법에 대한 명확한 안내 제공

## Step 6: 답변 수집
- 사용자가 문서의 [Answer]: 태그로 모든 질문에 답할 때까지 대기
- 모든 [Answer]: 태그가 채워질 때까지 진행하지 마세요
- [Answer]: 태그 중 비어 있는 것이 없도록 문서를 검토

## Step 7: 답변 분석 (MANDATORY)
진행 전에 모든 사용자 답변을 다음 관점에서 꼼꼼히 검토해야 합니다:
- **모호하거나 애매한 응답**: "mix of", "somewhere between", "not sure", "depends"
- **정의되지 않은 기준/용어**: 명확한 정의 없는 개념 참조
- **모순된 답변**: 서로 상충하는 응답
- **누락된 생성 세부**: 구체적 가이드가 부족한 답변
- **옵션을 섞은 답변**: 명확한 결정 규칙 없이 여러 접근을 섞은 응답

## Step 8: 후속 질문 (MANDATORY)
Step 7의 분석에서 모호한 답변이 발견되면, 다음을 반드시 수행해야 합니다:
- 계획 문서에 [Answer]: 태그를 사용한 구체적 후속 질문 추가
- 모든 모호성이 해소될 때까지 승인으로 진행하지 마세요
- 필요한 후속 질문 예시:
  - "A와 B의 혼합이라고 하셨는데, 언제 A를 쓰고 언제 B를 쓸지 구체적 기준은 무엇인가요?"
  - "A와 B 사이라고 하셨는데, 정확한 중간 지점 접근 방식을 정의해 주실 수 있나요?"
  - "잘 모르겠다고 하셨는데, 결정에 도움이 될 추가 정보는 무엇인가요?"
  - "복잡도에 따라 다르다고 하셨는데, 복잡도 수준을 어떻게 정의하시나요?"

## Step 9: 승인 요청
- 다음과 같이 질문: "**Unit of work plan complete. Review the plan in aidlc-docs/inception/plans/unit-of-work-plan.md. Ready to proceed to generation?**"
- 사용자가 확인할 때까지 진행하지 마세요.

## Step 10: 승인 로깅
- audit.md에 프롬프트와 응답을 타임스탬프와 함께 로깅
- ISO 8601 타임스탬프 포맷 사용
- 완전한 승인 프롬프트 텍스트 포함

## Step 11: 진행 상태 업데이트
- aidlc-state.md에서 Units Generation Part 1 (Planning)을 완료로 표시
- "Current Status" 섹션 업데이트
- Units Generation Part 2 (Generation)으로의 전환 준비

---

# PART 2: GENERATION

## Step 12: Unit of Work 계획 로드
- [ ] `aidlc-docs/inception/plans/unit-of-work-plan.md`에서 전체 계획을 읽음
- [ ] 다음 미완료 단계 식별 (첫 [ ] 체크박스)
- [ ] 해당 단계의 컨텍스트와 요구사항 로드

## Step 13: 현재 단계 실행
- [ ] 현재 단계가 기술하는 바를 정확히 수행
- [ ] 계획에 명시된 대로 단위 아티팩트 생성
- [ ] Planning에서 승인된 분해 접근을 따름
- [ ] 계획에 명시된 기준과 경계를 사용

## Step 14: 진행 상태 업데이트
- [ ] unit of work 계획에서 완료된 단계를 [x]로 표시
- [ ] `aidlc-docs/aidlc-state.md`의 current status 업데이트
- [ ] 생성된 모든 아티팩트 저장

## Step 15: 계속 또는 완료
- [ ] 남은 단계가 있으면 Step 12로 돌아감
- [ ] 모든 단계가 완료되면 단위가 design 스테이지를 위한 준비가 되었는지 검증
- [ ] Units Generation 스테이지를 완료로 표시

## Step 16: 완료 메시지 제시

```markdown
# 🔧 Units Generation Complete

[AI가 생성한 단위와 분해 결과 요약을 불릿으로]

> **📋 <u>**REVIEW REQUIRED:**</u>**  
> Please examine the units generation artifacts at: `aidlc-docs/inception/application-design/`

> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - Ask for modifications to the units generation if required
> ✅ **Approve & Continue** - Approve units and proceed to **CONSTRUCTION PHASE**
```

## Step 17: 명시적 승인 대기
- 사용자가 명시적으로 units generation을 승인할 때까지 진행하지 마세요.
- 승인은 명확하고 모호하지 않아야 합니다.
- 사용자가 변경을 요청하면 단위를 업데이트하고 승인 과정을 반복합니다.

## Step 18: 승인 응답 기록
- 사용자의 승인 응답을 타임스탬프와 함께 `aidlc-docs/audit.md`에 로깅
- 정확한 사용자 응답 텍스트 포함
- 승인 상태를 명확히 표시

## Step 19: 진행 상태 업데이트
- `aidlc-docs/aidlc-state.md`에서 Units Generation 스테이지를 완료로 표시
- "Current Status" 섹션 업데이트
- CONSTRUCTION PHASE로의 전환 준비

---

## 핵심 룰

### Planning 단계 룰
- 컨텍스트에 관련된 질문만 생성
- 모든 질문에 [Answer]: 태그 포맷 사용
- 진행 전 모든 답변에서 모호성 분석
- 후속 질문으로 **모든** 모호성 해소
- 생성 전에 명시적 사용자 승인 받기

### Generation 단계 룰
- **NO HARDCODED LOGIC**: unit of work 계획에 적힌 내용만 실행
- **FOLLOW PLAN EXACTLY**: 단계 순서에서 벗어나지 마세요.
- **UPDATE CHECKBOXES**: 각 단계 완료 즉시 [x]로 표시
- **USE APPROVED APPROACH**: Planning에서의 분해 방법론을 따름
- **VERIFY COMPLETION**: 진행 전에 모든 단위 아티팩트가 완전한지 확인

## 완료 기준
- 모든 기획 질문에 답이 있고 모호성이 해소됨
- 사용자가 계획을 승인함
- unit of work 계획의 모든 단계가 [x]로 표시됨
- 모든 단위 아티팩트가 계획대로 생성됨:
  - `unit-of-work.md` (단위 정의)
  - `unit-of-work-dependency.md` (의존성 매트릭스)
  - `unit-of-work-story-map.md` (스토리 매핑)
- 단위가 검증되어 단위별 설계 스테이지를 진행할 준비 완료
