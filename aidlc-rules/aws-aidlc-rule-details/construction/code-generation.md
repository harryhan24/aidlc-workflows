# Code Generation - 상세 절차

## 개요
이 스테이지는 통합된 두 파트를 통해 각 작업 단위에 대한 코드를 생성합니다.
- **Part 1 - Planning**: 명시적 단계가 포함된 상세 코드 생성 계획 수립
- **Part 2 - Generation**: 승인된 계획을 실행하여 코드, 테스트, 아티팩트 생성

**Note**: 브라운필드 프로젝트의 경우, "generate"는 적절한 시점에 기존 파일을 수정하는 것을 의미하며 중복본을 만드는 것이 아닙니다.

## Prerequisites
- 해당 단위에 대해 Unit Design Generation이 완료되어 있어야 합니다.
- (실행된 경우) 해당 단위에 대한 NFR Implementation이 완료되어 있어야 합니다.
- 모든 단위 디자인 아티팩트가 이용 가능해야 합니다.
- 단위가 코드 생성 준비 상태여야 합니다.

---

# PART 1: PLANNING

## Step 1: 단위 컨텍스트 분석
- [ ] Unit Design Generation에서 생성된 단위 디자인 아티팩트 읽기
- [ ] 할당된 스토리를 이해하기 위해 단위 스토리 맵 읽기
- [ ] 단위 의존성과 인터페이스 식별
- [ ] 단위가 코드 생성 준비 상태인지 검증

## Step 2: 상세한 단위 코드 생성 계획 수립
- [ ] `aidlc-docs/aidlc-state.md`에서 워크스페이스 루트와 프로젝트 유형 읽기
- [ ] 코드 위치 결정 (구조 패턴은 Critical Rules 참조)
- [ ] **Brownfield only**: 수정할 기존 파일에 대해 리버스 엔지니어링 code-structure.md 검토
- [ ] 정확한 경로 문서화 (aidlc-docs/는 절대 사용하지 않음)
- [ ] 단위 생성에 대한 명시적 단계 작성:
  - Project Structure Setup (greenfield only)
  - Business Logic Generation
  - Business Logic Unit Testing
  - Business Logic Summary
  - API Layer Generation
  - API Layer Unit Testing
  - API Layer Summary
  - Repository Layer Generation
  - Repository Layer Unit Testing
  - Repository Layer Summary
  - Frontend Components Generation (해당되는 경우)
  - Frontend Components Unit Testing (해당되는 경우)
  - Frontend Components Summary (해당되는 경우)
  - Database Migration Scripts (데이터 모델이 존재하는 경우)
  - Documentation Generation (API 문서, README 업데이트)
  - Deployment Artifacts Generation
- [ ] 각 단계에 순차 번호 부여
- [ ] 스토리 매핑 참조 포함
- [ ] 각 단계에 체크박스 [ ] 추가

## Step 3: 단위 생성 컨텍스트 포함
- [ ] 이 단위에 대해 다음을 포함:
  - 이 단위가 구현하는 스토리
  - 다른 단위/서비스에 대한 의존성
  - 예상되는 인터페이스 및 계약
  - 이 단위가 소유하는 데이터베이스 엔티티
  - 서비스 경계 및 책임

## Step 4: 단위 계획 문서 작성
- [ ] 완성된 계획을 `aidlc-docs/construction/plans/{unit-name}-code-generation-plan.md`로 저장
- [ ] 단계 번호 포함 (Step 1, Step 2 등)
- [ ] 단위 컨텍스트 및 의존성 포함
- [ ] 스토리 추적성 포함
- [ ] 계획이 단계별로 실행 가능하도록 보장
- [ ] 이 계획이 Code Generation의 단일 진실 공급원(single source of truth)임을 명시

## Step 5: 단위 계획 요약
- [ ] 사용자에게 단위 코드 생성 계획 요약 제공
- [ ] 단위 생성 접근 방식 강조
- [ ] 단계 순서 및 스토리 커버리지 설명
- [ ] 총 단계 수와 예상 범위 명시

## Step 6: 승인 프롬프트 로깅
- [ ] 승인을 요청하기 전에 `aidlc-docs/audit.md`에 타임스탬프와 함께 프롬프트 로깅
- [ ] 완전한 단위 코드 생성 계획에 대한 참조 포함
- [ ] ISO 8601 타임스탬프 형식 사용

## Step 7: 명시적 승인 대기
- [ ] 사용자가 단위 코드 생성 계획을 명시적으로 승인할 때까지 진행하지 않음
- [ ] 승인은 전체 계획과 생성 시퀀스를 모두 포함해야 함
- [ ] 사용자가 변경을 요청하면 계획을 업데이트하고 승인 과정을 반복

## Step 8: 승인 응답 기록
- [ ] 사용자의 승인 응답을 타임스탬프와 함께 `aidlc-docs/audit.md`에 로깅
- [ ] 사용자 응답 텍스트를 정확히 포함
- [ ] 승인 상태를 명확히 표시

## Step 9: 진행 상황 업데이트
- [ ] `aidlc-state.md`에 Code Generation Part 1 (Planning) 완료 표시
- [ ] "Current Status" 섹션 업데이트
- [ ] Code Generation으로 전환할 준비

---

# PART 2: GENERATION

## Step 10: 단위 코드 생성 계획 로드
- [ ] `aidlc-docs/construction/plans/{unit-name}-code-generation-plan.md`에서 완전한 계획 읽기
- [ ] 다음 미완료 단계 식별 (첫 번째 [ ] 체크박스)
- [ ] 해당 단계에 대한 컨텍스트(단위, 의존성, 스토리) 로드

## Step 11: 현재 단계 실행
- [ ] 계획에서 대상 디렉터리 확인 (aidlc-docs/는 절대 사용하지 않음)
- [ ] **Brownfield only**: 대상 파일 존재 여부 확인
- [ ] 현재 단계에서 기술한 대로 정확히 생성:
  - **파일이 존재하는 경우**: in-place 수정 (`ClassName_modified.java`, `ClassName_new.java` 등을 절대 생성하지 않음)
  - **파일이 존재하지 않는 경우**: 새 파일 생성
- [ ] 올바른 위치에 작성:
  - **Application Code**: 프로젝트 구조에 따라 워크스페이스 루트
  - **Documentation**: `aidlc-docs/construction/{unit-name}/code/` (markdown만)
  - **Build/Config Files**: 워크스페이스 루트
- [ ] 단위 스토리 요구사항 준수
- [ ] 의존성과 인터페이스 준수

## Step 12: 진행 상황 업데이트
- [ ] 단위 코드 생성 계획에서 완료된 단계를 [x]로 표시
- [ ] 관련 단위 스토리의 생성이 끝나면 [x]로 표시
- [ ] `aidlc-docs/aidlc-state.md`의 현재 상태 업데이트
- [ ] **Brownfield only**: 중복 파일이 생성되지 않았는지 확인 (예: `ClassName.java`와 함께 `ClassName_modified.java`가 존재하지 않을 것)
- [ ] 생성된 모든 아티팩트 저장

## Step 13: 생성 계속 또는 완료
- [ ] 더 진행할 단계가 남아 있으면 Step 10으로 돌아가기
- [ ] 모든 단계가 완료되면 완료 메시지 제시로 진행

## Step 14: 완료 메시지 제시
- 다음 구조로 완료 메시지를 제시합니다.
     1. **Completion Announcement** (mandatory): 항상 다음으로 시작합니다.

```markdown
# 💻 Code Generation Complete - [unit-name]
```

     2. **AI Summary** (optional): 구조화된 글머리 기호 요약 제공
        - **Brownfield**: 수정된 파일과 생성된 파일을 구분 (예: "• Modified: `src/services/user-service.ts`", "• Created: `src/services/auth-service.ts`")
        - **Greenfield**: 생성된 파일을 경로와 함께 나열 (예: "• Created: `src/services/user-service.ts`")
        - 테스트, 문서, 배포 아티팩트를 경로와 함께 나열
        - 사실 위주로 유지하고 워크플로우 지시문은 넣지 않을 것
     3. **Formatted Workflow Message** (mandatory): 항상 다음 형식으로 정확히 종료합니다.

```markdown
> **📋 <u>**REVIEW REQUIRED:**</u>**  
> Please examine the generated code at:
> - **Application Code**: `[actual-workspace-path]`
> - **Documentation**: `aidlc-docs/construction/[unit-name]/code/`



> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - Ask for modifications to the generated code based on your review  
> ✅ **Continue to Next Stage** - Approve code generation and proceed to **[next-unit/Build & Test]**

---
```

## Step 15: 명시적 승인 대기
- 사용자가 생성된 코드를 명시적으로 승인할 때까지 진행하지 않음
- 승인은 명확하고 모호하지 않아야 함
- 사용자가 변경을 요청하면 코드를 업데이트하고 승인 과정을 반복

## Step 16: 승인 기록 및 진행 상황 업데이트
- 승인을 audit.md에 타임스탬프와 함께 로깅
- 사용자의 승인 응답을 타임스탬프와 함께 기록
- 이 단위에 대해 aidlc-state.md에서 Code Generation 스테이지를 완료로 표시

---

## Critical Rules

### Code Location Rules
- **Application code**: 워크스페이스 루트만 사용 (절대 aidlc-docs/ 사용하지 않음)
- **Documentation**: aidlc-docs/만 사용 (markdown 요약)
- 코드 생성 전에 aidlc-state.md에서 **워크스페이스 루트를 읽을 것**

**프로젝트 유형별 구조 패턴**:
- **Brownfield**: 기존 구조 사용 (예: `src/main/java/`, `lib/`, `pkg/`)
- **Greenfield single unit**: 워크스페이스 루트에 `src/`, `tests/`, `config/`
- **Greenfield multi-unit (microservices)**: `{unit-name}/src/`, `{unit-name}/tests/`
- **Greenfield multi-unit (monolith)**: `src/{unit-name}/`, `tests/{unit-name}/`

### Brownfield File Modification Rules
- 생성 전에 파일 존재 여부 확인
- 존재할 경우: in-place 수정 (`ClassName_modified.java`와 같은 복사본 생성 금지)
- 존재하지 않을 경우: 새 파일 생성
- 생성 후 중복 파일이 없는지 확인 (Step 12)

### Planning Phase Rules
- 모든 생성 활동에 대해 명시적이고 번호가 매겨진 단계 작성
- 계획에 스토리 추적성 포함
- 단위 컨텍스트 및 의존성 문서화
- 생성 전에 명시적인 사용자 승인 획득

### Generation Phase Rules
- **NO HARDCODED LOGIC**: 단위 계획에 작성된 것만 실행
- **FOLLOW PLAN EXACTLY**: 단계 순서에서 이탈하지 않음
- **UPDATE CHECKBOXES**: 각 단계 완료 직후 [x]로 표시
- **STORY TRACEABILITY**: 기능이 구현되면 단위 스토리를 [x]로 표시
- **RESPECT DEPENDENCIES**: 단위 의존성이 충족된 경우에만 구현

### Automation Friendly Code Rules
UI 코드(web, mobile, desktop) 생성 시 요소가 자동화 친화적이 되도록 보장하십시오.
- 인터랙티브 요소(buttons, inputs, links, forms)에 `data-testid` 속성 추가
- 일관된 네이밍 사용: `{component}-{element-role}` (예: `login-form-submit-button`, `user-list-search-input`)
- 렌더 간에 변하는 동적/자동 생성 ID 회피
- 코드 변경 시에도 `data-testid` 값을 안정적으로 유지 (요소 목적이 바뀔 때만 변경)

## Completion Criteria
- 완전한 단위 코드 생성 계획이 작성되고 승인됨
- 단위 코드 생성 계획의 모든 단계가 [x]로 표시됨
- 계획에 따라 모든 단위 스토리가 구현됨
- 모든 코드와 테스트가 생성됨 (테스트는 Build & Test 단계에서 실행됨)
- 배포 아티팩트가 생성됨
- 완성된 단위가 빌드 및 검증 준비 완료
