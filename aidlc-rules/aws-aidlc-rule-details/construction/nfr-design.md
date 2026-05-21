# NFR Design

## Prerequisites
- 해당 단위에 대해 NFR Requirements가 완료되어 있어야 합니다.
- NFR 요구사항 아티팩트가 이용 가능해야 합니다.
- 실행 계획이 NFR Design 스테이지 실행을 지시해야 합니다.

## Overview
NFR 요구사항을 패턴과 논리 컴포넌트를 사용하여 단위 설계에 반영합니다.

## Steps to Execute

### Step 1: NFR 요구사항 분석
- `aidlc-docs/construction/{unit-name}/nfr-requirements/`에서 NFR 요구사항 읽기
- 확장성, 성능, 가용성, 보안 요구사항 이해

### Step 2: NFR Design 계획 수립
- NFR design을 위한 체크박스 []가 포함된 계획 생성
- 설계 패턴 및 논리 컴포넌트에 초점
- 각 단계에 체크박스 [] 포함

### Step 3: 컨텍스트에 적합한 명확화 질문 생성
**DIRECTIVE**: NFR 요구사항을 철저히 분석하여 NFR 설계 품질을 향상시킬 수 있는 모든 영역을 식별하십시오. 종합적인 비기능 설계 커버리지를 위해 적극적으로 질문하십시오.

**CRITICAL**: NFR 설계 품질에 영향을 줄 수 있는 어떠한 모호함이나 누락된 세부사항이 있을 때는 기본적으로 질문을 합니다. 비기능 패턴에 대한 잘못된 가정을 하느니 질문을 많이 하는 편이 낫습니다.

**MANDATORY**: 다음 모든 카테고리를 평가하고 각각에 대해 목표 지향적인 질문을 하십시오. 각 카테고리에 대해 NFR 요구사항으로부터의 근거에 기반하여 적용 여부를 판단하십시오 — 명시적 정당화 없이 카테고리를 건너뛰지 마십시오.

- [Answer]: 태그 형식을 사용하여 질문을 임베드
- 모호함, 누락된 정보, 명확화가 필요한 부분에 초점
- 사용자 입력이 패턴 및 컴포넌트 결정을 개선할 수 있는 모든 곳에서 질문 생성
- **의심스러우면 질문할 것** - 과신은 잘못된 비기능 설계로 이어집니다.

**평가할 질문 카테고리** (모든 카테고리 고려):
- **Resilience Patterns** - 결함 허용 접근 방식, 재시도 전략, 장애 복구 기대치에 대해 질문
- **Scalability Patterns** - 확장 메커니즘, 부하 경계, 성장 예측에 대해 질문
- **Performance Patterns** - 최적화 전략, 지연시간 목표, 처리량 요구사항에 대해 질문
- **Security Patterns** - 보안 구현 접근 방식, 위협 모델, 컴플라이언스 제약에 대해 질문
- **Logical Components** - 인프라 컴포넌트(queues, caches, circuit breakers 등)와 통합 패턴에 대해 질문

### Step 4: 계획 저장
- `aidlc-docs/construction/plans/{unit-name}-nfr-design-plan.md`로 저장
- 사용자 입력용 [Answer]: 태그 모두 포함

### Step 5: 답변 수집 및 분석
- 사용자가 모든 [Answer]: 태그를 완료할 때까지 대기
- 모호하거나 불명확한 응답 검토
- 필요 시 후속 질문 추가

### Step 6: NFR Design 아티팩트 생성
- `aidlc-docs/construction/{unit-name}/nfr-design/nfr-design-patterns.md` 생성
- `aidlc-docs/construction/{unit-name}/nfr-design/logical-components.md` 생성

### Step 7: 완료 메시지 제시
- 다음 구조로 완료 메시지를 제시합니다.
     1. **Completion Announcement** (mandatory): 항상 다음으로 시작합니다.

```markdown
# 🎨 NFR Design Complete - [unit-name]
```

     2. **AI Summary** (optional): NFR design의 구조화된 글머리 기호 요약 제공
        - 형식: "NFR design has incorporated [description]:"
        - 적용된 주요 설계 패턴 나열 (글머리 기호)
        - 논리 컴포넌트 및 인프라 요소 나열
        - 적용된 resilience, scalability, performance 패턴 언급
        - 워크플로우 지시문("please review", "let me know", "proceed to next phase", "before we proceed")은 포함하지 않을 것
        - 사실 위주, 내용 중심으로 유지
     3. **Formatted Workflow Message** (mandatory): 항상 다음 형식으로 정확히 종료합니다.

```markdown
> **📋 <u>**REVIEW REQUIRED:**</u>**  
> Please examine the NFR design at: `aidlc-docs/construction/[unit-name]/nfr-design/`



> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - Ask for modifications to the NFR design based on your review  
> ✅ **Continue to Next Stage** - Approve NFR design and proceed to **[next-stage-name]**

---
```

### Step 8: 명시적 승인 대기
- 사용자가 NFR design을 명시적으로 승인할 때까지 진행하지 않음
- 승인은 명확하고 모호하지 않아야 함
- 사용자가 변경을 요청하면 설계를 업데이트하고 승인 과정을 반복

### Step 9: 승인 기록 및 진행 상황 업데이트
- 승인을 audit.md에 타임스탬프와 함께 로깅
- 사용자의 승인 응답을 타임스탬프와 함께 기록
- aidlc-state.md에서 NFR Design 스테이지를 완료로 표시
