# Infrastructure Design

## Prerequisites
- 해당 단위에 대해 Functional Design이 완료되어 있어야 합니다.
- NFR Design 권장 (매핑할 논리 컴포넌트를 제공)
- 실행 계획이 Infrastructure Design 스테이지 실행을 지시해야 합니다.

## Overview
논리적 소프트웨어 컴포넌트를 배포 환경의 실제 인프라 선택지에 매핑합니다.

## Steps to Execute

### Step 1: 설계 아티팩트 분석
- `aidlc-docs/construction/{unit-name}/functional-design/`에서 functional design 읽기
- `aidlc-docs/construction/{unit-name}/nfr-design/`에서 NFR design 읽기 (존재할 경우)
- 인프라가 필요한 논리 컴포넌트 식별

### Step 2: Infrastructure Design 계획 수립
- infrastructure design을 위한 체크박스 []가 포함된 계획 생성
- 실제 서비스(AWS, Azure, GCP, 온프레미스) 매핑에 초점
- 각 단계에 체크박스 [] 포함

### Step 3: 컨텍스트에 적합한 명확화 질문 생성
**DIRECTIVE**: functional 및 NFR design을 철저히 분석하여 인프라 결정을 개선할 수 있는 모든 영역을 식별하십시오. 종합적인 인프라 커버리지를 위해 적극적으로 질문하십시오.

**CRITICAL**: 인프라 품질에 영향을 줄 수 있는 어떠한 모호함이나 누락된 세부사항이 있을 때는 기본적으로 질문을 합니다. 잘못된 인프라 가정을 하느니 질문을 많이 하는 편이 낫습니다.

**MANDATORY**: 다음 모든 카테고리를 평가하고 각각에 대해 목표 지향적인 질문을 하십시오. 각 카테고리에 대해 functional 및 NFR design 아티팩트로부터의 근거에 기반하여 적용 여부를 판단하십시오 — 명시적 정당화 없이 카테고리를 건너뛰지 마십시오.

- [Answer]: 태그 형식을 사용하여 질문을 임베드
- 모호함, 누락된 정보, 명확화가 필요한 부분에 초점
- 사용자 입력이 인프라 결정을 개선할 수 있는 모든 곳에서 질문 생성
- **의심스러우면 질문할 것** - 과신은 잘못된 인프라 선택으로 이어집니다.

**평가할 질문 카테고리** (모든 카테고리 고려):
- **Deployment Environment** - 클라우드 프로바이더 선호, 환경 구성, 배포 대상에 대해 질문
- **Compute Infrastructure** - 컴퓨트 서비스 선택, 사이징, 확장 요구사항에 대해 질문
- **Storage Infrastructure** - 데이터베이스 선택, 스토리지 패턴, 데이터 라이프사이클 요구사항에 대해 질문
- **Messaging Infrastructure** - 메시징/큐잉 서비스, 이벤트 기반 패턴, 비동기 처리에 대해 질문
- **Networking Infrastructure** - 로드 밸런싱, API 게이트웨이 방식, 네트워크 토폴로지에 대해 질문
- **Monitoring Infrastructure** - 관측 가능성 도구, 알림 전략, 로깅 요구사항에 대해 질문
- **Shared Infrastructure** - 인프라 공유 전략, 멀티 테넌시, 리소스 격리에 대해 질문

### Step 4: 계획 저장
- `aidlc-docs/construction/plans/{unit-name}-infrastructure-design-plan.md`로 저장
- 사용자 입력용 [Answer]: 태그 모두 포함

### Step 5: 답변 수집 및 분석
- 사용자가 모든 [Answer]: 태그를 완료할 때까지 대기
- 모호하거나 불명확한 응답 검토
- 필요 시 후속 질문 추가

### Step 6: Infrastructure Design 아티팩트 생성
- `aidlc-docs/construction/{unit-name}/infrastructure-design/infrastructure-design.md` 생성
- `aidlc-docs/construction/{unit-name}/infrastructure-design/deployment-architecture.md` 생성
- 공유 인프라가 있는 경우: `aidlc-docs/construction/shared-infrastructure.md` 생성

### Step 7: 완료 메시지 제시
- 다음 구조로 완료 메시지를 제시합니다.
     1. **Completion Announcement** (mandatory): 항상 다음으로 시작합니다.

```markdown
# 🏢 Infrastructure Design Complete - [unit-name]
```

     2. **AI Summary** (optional): infrastructure design의 구조화된 글머리 기호 요약 제공
        - 형식: "Infrastructure design has mapped [description]:"
        - 주요 인프라 서비스 및 컴포넌트 나열 (글머리 기호)
        - 배포 아키텍처 결정 및 근거 나열
        - 클라우드 프로바이더 선택 및 서비스 매핑 언급
        - 워크플로우 지시문("please review", "let me know", "proceed to next phase", "before we proceed")은 포함하지 않을 것
        - 사실 위주, 내용 중심으로 유지
     3. **Formatted Workflow Message** (mandatory): 항상 다음 형식으로 정확히 종료합니다.

```markdown
> **📋 <u>**REVIEW REQUIRED:**</u>**  
> Please examine the infrastructure design at: `aidlc-docs/construction/[unit-name]/infrastructure-design/`



> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - Ask for modifications to the infrastructure design based on your review  
> ✅ **Continue to Next Stage** - Approve infrastructure design and proceed to **Code Generation**

---
```

### Step 8: 명시적 승인 대기
- 사용자가 infrastructure design을 명시적으로 승인할 때까지 진행하지 않음
- 승인은 명확하고 모호하지 않아야 함
- 사용자가 변경을 요청하면 설계를 업데이트하고 승인 과정을 반복

### Step 9: 승인 기록 및 진행 상황 업데이트
- 승인을 audit.md에 타임스탬프와 함께 로깅
- 사용자의 승인 응답을 타임스탬프와 함께 기록
- aidlc-state.md에서 Infrastructure Design 스테이지를 완료로 표시
