# NFR Requirements

## Prerequisites
- 해당 단위에 대해 Functional Design이 완료되어 있어야 합니다.
- 단위 functional design 아티팩트가 이용 가능해야 합니다.
- 실행 계획이 NFR Requirements 스테이지 실행을 지시해야 합니다.

## Overview
단위에 대한 비기능 요구사항을 결정하고 기술 스택 선택을 수행합니다.

## Steps to Execute

### Step 1: Functional Design 분석
- `aidlc-docs/construction/{unit-name}/functional-design/`에서 functional design 아티팩트 읽기
- 비즈니스 로직 복잡도 및 요구사항 이해

### Step 2: NFR Requirements 계획 수립
- NFR 평가를 위한 체크박스 []가 포함된 계획 생성
- 확장성, 성능, 가용성, 보안에 초점
- 각 단계에 체크박스 [] 포함

### Step 3: 컨텍스트에 적합한 명확화 질문 생성
**DIRECTIVE**: functional design을 철저히 분석하여 NFR 명확화가 시스템 품질 및 아키텍처 결정을 향상시킬 수 있는 모든 영역을 식별하십시오. 종합적인 NFR 커버리지를 위해 적극적으로 질문하십시오.

**CRITICAL**: 시스템 품질에 영향을 줄 수 있는 어떠한 모호함이나 누락된 세부사항이 있을 때는 기본적으로 질문을 합니다. 잘못된 NFR 가정을 하느니 질문을 많이 하는 편이 낫습니다.

- [Answer]: 태그 형식을 사용하여 질문을 임베드
- 모호함, 누락된 정보, 명확화가 필요한 부분에 초점
- 사용자 입력이 NFR 및 기술 스택 결정을 개선할 수 있는 모든 곳에서 질문 생성
- **의심스러우면 질문할 것** - 과신은 낮은 시스템 품질로 이어집니다.

**평가할 질문 카테고리** (모든 카테고리 고려):
- **Scalability Requirements** - 예상 부하, 성장 패턴, 확장 트리거, 용량 계획에 대해 질문
- **Performance Requirements** - 응답 시간, 처리량, 지연시간, 성능 벤치마크에 대해 질문
- **Availability Requirements** - 가용 시간 기대치, 재해 복구, 페일오버, 비즈니스 연속성에 대해 질문
- **Security Requirements** - 데이터 보호, 컴플라이언스, 인증, 인가, 위협 모델에 대해 질문
- **Tech Stack Selection** - 기술 선호도, 제약 조건, 기존 시스템, 통합 요구사항에 대해 질문
- **Reliability Requirements** - 오류 처리, 결함 허용, 모니터링, 알림 요구사항에 대해 질문
- **Maintainability Requirements** - 코드 품질, 문서화, 테스팅, 운영 요구사항에 대해 질문
- **Usability Requirements** - 사용자 경험, 접근성, 인터페이스 요구사항에 대해 질문

### Step 4: 계획 저장
- `aidlc-docs/construction/plans/{unit-name}-nfr-requirements-plan.md`로 저장
- 사용자 입력용 [Answer]: 태그 모두 포함

### Step 5: 답변 수집 및 분석
- 사용자가 모든 [Answer]: 태그를 완료할 때까지 대기
- **MANDATORY**: 모든 응답을 신중히 검토하여 모호하거나 불명확한 답변 확인
- **CRITICAL**: 불분명한 응답에 대해서는 후속 질문 추가 - 모호한 상태로 진행하지 않음
- "depends", "maybe", "not sure", "mix of", "somewhere between", "standard", "typical"과 같은 응답을 주의 깊게 살핌
- 모호함이 발견되면 명확화 질문 파일 생성
- **모든 모호함이 해소될 때까지 진행하지 않음**

### Step 6: NFR Requirements 아티팩트 생성
- `aidlc-docs/construction/{unit-name}/nfr-requirements/nfr-requirements.md` 생성
- `aidlc-docs/construction/{unit-name}/nfr-requirements/tech-stack-decisions.md` 생성

### Step 7: 완료 메시지 제시
- 다음 구조로 완료 메시지를 제시합니다.
     1. **Completion Announcement** (mandatory): 항상 다음으로 시작합니다.

```markdown
# 📊 NFR Requirements Complete - [unit-name]
```

     2. **AI Summary** (optional): NFR 요구사항의 구조화된 글머리 기호 요약 제공
        - 형식: "NFR requirements assessment has identified [description]:"
        - 주요 scalability, performance, availability 요구사항 나열 (글머리 기호)
        - 식별된 security 및 compliance 요구사항 나열
        - 기술 스택 결정 및 근거 언급
        - 워크플로우 지시문("please review", "let me know", "proceed to next phase", "before we proceed")은 포함하지 않을 것
        - 사실 위주, 내용 중심으로 유지
     3. **Formatted Workflow Message** (mandatory): 항상 다음 형식으로 정확히 종료합니다.

```markdown
> **📋 <u>**REVIEW REQUIRED:**</u>**  
> Please examine the NFR requirements at: `aidlc-docs/construction/[unit-name]/nfr-requirements/`



> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - Ask for modifications to the NFR requirements based on your review  
> ✅ **Continue to Next Stage** - Approve NFR requirements and proceed to **[next-stage-name]**

---
```

### Step 8: 명시적 승인 대기
- 사용자가 NFR 요구사항을 명시적으로 승인할 때까지 진행하지 않음
- 승인은 명확하고 모호하지 않아야 함
- 사용자가 변경을 요청하면 요구사항을 업데이트하고 승인 과정을 반복

### Step 9: 승인 기록 및 진행 상황 업데이트
- 승인을 audit.md에 타임스탬프와 함께 로깅
- 사용자의 승인 응답을 타임스탬프와 함께 기록
- aidlc-state.md에서 NFR Requirements 스테이지를 완료로 표시
