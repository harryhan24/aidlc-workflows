# Functional Design

## Purpose
**단위(unit)별 상세 비즈니스 로직 설계**

Functional Design은 다음에 초점을 둡니다.
- 단위의 상세 비즈니스 로직 및 알고리즘
- 엔티티와 관계가 포함된 도메인 모델
- 상세 비즈니스 룰, 검증 로직, 제약 조건
- 기술 중립적 설계 (인프라 관심사 없음)

**Note**: 이는 Application Design (INCEPTION 단계)에서 정의한 고수준 컴포넌트 디자인을 기반으로 합니다.

## Prerequisites
- Units Generation이 완료되어 있어야 합니다.
- 작업 단위(unit of work) 아티팩트가 이용 가능해야 합니다.
- Application Design 권장 (고수준 컴포넌트 구조 제공)
- 실행 계획이 Functional Design 스테이지 실행을 지시해야 합니다.

## Overview
단위에 대한 상세 비즈니스 로직을 설계하되, 기술 중립적이며 순수하게 비즈니스 기능에 집중합니다.

## Steps to Execute

### Step 1: 단위 컨텍스트 분석
- `aidlc-docs/inception/application-design/unit-of-work.md`에서 단위 정의 읽기
- `aidlc-docs/inception/application-design/unit-of-work-story-map.md`에서 할당된 스토리 읽기
- 단위의 책임과 경계 이해

### Step 2: Functional Design 계획 수립
- functional design을 위한 체크박스 []가 포함된 계획 생성
- 비즈니스 로직, 도메인 모델, 비즈니스 룰에 초점
- 각 단계에 체크박스 [] 포함

### Step 3: 컨텍스트에 적합한 명확화 질문 생성
**DIRECTIVE**: 단위 정의와 functional design 아티팩트를 철저히 분석하여 functional design 품질을 향상시킬 수 있는 모든 영역을 식별하십시오. 종합적인 이해를 보장하기 위해 적극적으로 질문하십시오.

**CRITICAL**: functional design 품질에 영향을 줄 수 있는 어떠한 모호함이나 누락된 세부사항이 있을 때는 기본적으로 질문을 합니다. 잘못된 가정을 하느니 질문을 많이 하는 편이 낫습니다.

- [Answer]: 태그 형식을 사용하여 질문을 임베드
- 모호함, 누락된 정보, 명확화가 필요한 부분에 초점
- 사용자 입력이 functional design 결정을 개선할 수 있는 모든 곳에서 질문 생성
- **의심스러우면 질문할 것** - 과신은 잘못된 설계로 이어집니다.

**고려할 질문 카테고리** (모든 카테고리 평가):
- **Business Logic Modeling** - 핵심 엔티티, 워크플로우, 데이터 변환, 비즈니스 프로세스에 대해 질문
- **Domain Model** - 도메인 개념, 엔티티 관계, 데이터 구조, 비즈니스 객체에 대해 질문
- **Business Rules** - 의사결정 룰, 검증 로직, 제약 조건, 비즈니스 정책에 대해 질문
- **Data Flow** - 데이터 입력, 출력, 변환, 영속화 요구사항에 대해 질문
- **Integration Points** - 외부 시스템 상호작용, API, 데이터 교환에 대해 질문
- **Error Handling** - 오류 시나리오, 검증 실패, 예외 처리에 대해 질문
- **Business Scenarios** - 엣지 케이스, 대안 흐름, 복잡한 비즈니스 상황에 대해 질문
- **Frontend Components** (해당되는 경우) - UI 컴포넌트 구조, 사용자 인터랙션, 상태 관리, 폼 처리에 대해 질문

### Step 4: 계획 저장
- `aidlc-docs/construction/plans/{unit-name}-functional-design-plan.md`로 저장
- 사용자 입력용 [Answer]: 태그 모두 포함

### Step 5: 답변 수집 및 분석
- 사용자가 모든 [Answer]: 태그를 완료할 때까지 대기
- **MANDATORY**: 모든 응답을 신중히 검토하여 모호하거나 불명확한 답변 확인
- **CRITICAL**: 불분명한 응답에 대해서는 후속 질문 추가 - 모호한 상태로 진행하지 않음
- "depends", "maybe", "not sure", "mix of", "somewhere between"과 같은 응답을 주의 깊게 살핌
- 모호함이 발견되면 명확화 질문 파일 생성
- **모든 모호함이 해소될 때까지 진행하지 않음**

### Step 6: Functional Design 아티팩트 생성
- `aidlc-docs/construction/{unit-name}/functional-design/business-logic-model.md` 생성
- `aidlc-docs/construction/{unit-name}/functional-design/business-rules.md` 생성
- `aidlc-docs/construction/{unit-name}/functional-design/domain-entities.md` 생성
- 단위가 frontend/UI를 포함하는 경우: `aidlc-docs/construction/{unit-name}/functional-design/frontend-components.md` 생성
  - 컴포넌트 계층 및 구조
  - 각 컴포넌트의 props 및 state 정의
  - 사용자 인터랙션 흐름
  - 폼 검증 룰
  - API 통합 지점 (각 컴포넌트가 사용하는 백엔드 엔드포인트)

### Step 7: 완료 메시지 제시
- 다음 구조로 완료 메시지를 제시합니다.
     1. **Completion Announcement** (mandatory): 항상 다음으로 시작합니다.

```markdown
# 🔧 Functional Design Complete - [unit-name]
```

     2. **AI Summary** (optional): functional design의 구조화된 글머리 기호 요약 제공
        - 형식: "Functional design has created [description]:"
        - 주요 비즈니스 로직 모델 및 엔티티 나열 (글머리 기호)
        - 정의된 비즈니스 룰 및 검증 로직 나열
        - 도메인 모델 구조 및 관계 언급
        - 워크플로우 지시문("please review", "let me know", "proceed to next phase", "before we proceed")은 포함하지 않을 것
        - 사실 위주, 내용 중심으로 유지
     3. **Formatted Workflow Message** (mandatory): 항상 다음 형식으로 정확히 종료합니다.

```markdown
> **📋 <u>**REVIEW REQUIRED:**</u>**  
> Please examine the functional design artifacts at: `aidlc-docs/construction/[unit-name]/functional-design/`



> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - Ask for modifications to the functional design based on your review  
> ✅ **Continue to Next Stage** - Approve functional design and proceed to **[next-stage-name]**

---
```

### Step 8: 명시적 승인 대기
- 사용자가 functional design을 명시적으로 승인할 때까지 진행하지 않음
- 승인은 명확하고 모호하지 않아야 함
- 사용자가 변경을 요청하면 설계를 업데이트하고 승인 과정을 반복

### Step 9: 승인 기록 및 진행 상황 업데이트
- 승인을 audit.md에 타임스탬프와 함께 로깅
- 사용자의 승인 응답을 타임스탬프와 함께 기록
- aidlc-state.md에서 Functional Design 스테이지를 완료로 표시
