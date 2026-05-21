# AI-DLC Quick Start

AI-DLC(AI-Driven Development Life Cycle)는 AI 어시스턴트가 소프트웨어를 계획, 설계, 빌드하는 과정을 가이드하는 구조화된 워크플로우입니다. 프로젝트를 시작하기 전에 AI에게 **무엇을 만들지**와 **어떤 도구를 사용할지**를 알려주는 두 개의 문서를 제공합니다.

---

## 제공해야 하는 것

### 1. Vision Document -- 무엇을 왜 만드는가

| 섹션                              | 작성할 내용                                                                | 분량                                                       |
| --------------------------------- | -------------------------------------------------------------------------- | ---------------------------------------------------------- |
| **Executive Summary**             | 한 단락: 무엇인지, 누구를 위한 것인지, 왜 중요한지                         | 3-5 문장                                                   |
| **Problem Statement**             | 이 프로젝트가 해결하는 구체적인 비즈니스 문제                              | 1-2 단락                                                   |
| **Target Users**                  | 누가 사용할지, 각 사용자 유형이 무엇을 필요로 하는지                       | 사용자 유형당 한 행씩 테이블                               |
| **Success Metrics**               | 이 프로젝트가 성공했는지를 어떻게 측정할 것인가                            | 측정 가능한 목표가 있는 테이블                             |
| **Full Scope Vision**             | 성숙 단계에서 제품이 될 수 있는 모든 것, 기능 영역별로 정리                | 필요한 만큼의 기능 영역                                    |
| **MVP Scope -- Features IN**      | 첫 릴리스에 포함되는 모든 기능과 근거                                      | 테이블. 나열되지 않으면 MVP에 없는 것입니다.               |
| **MVP Scope -- Features OUT**     | MVP에서 의도적으로 제외된 기능, 이유와 타겟 단계                           | 테이블. 스코프 크리프를 방지합니다.                        |
| **Risks and Open Questions**      | 무엇이 잘못될 수 있는지, 아직 결정되지 않은 것                             | 테이블과 글머리표 리스트                                   |

**핵심 원칙**: 전체 비전과 MVP를 분리하세요. 전체 비전은 열망적입니다. MVP는 가치를 전달하는 가장 작은 것입니다.

전체 가이드: [vision-document-guide.md](vision-document-guide.md)
작업 예제: [example-vision-scientific-calculator-api.md](example-vision-scientific-calculator-api.md)

---

### 2. Technical Environment Document -- 어떤 도구를 사용할 것인가

| 섹션                              | 작성할 내용                                                                                                                                              | 분량                                                  |
| --------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------- |
| **Languages**                     | 필수, 허용, 금지 언어와 버전                                                                                                                             | 카테고리별 테이블                                     |
| **Frameworks and Libraries**      | 필수, 선호, 금지 — 근거와 대안 포함                                                                                                                      | 카테고리별 테이블                                     |
| **Cloud Services**                | 허용 리스트와 거부 리스트, 제약 포함                                                                                                                     | 리스트별 테이블                                       |
| **Architecture and Patterns**     | API 스타일, 데이터 패턴, 메시징, 프로젝트 구조                                                                                                           | 테이블이 포함된 짧은 섹션                             |
| **Security**                      | 인증 방법, 암호화, 입력 검증, 시크릿 관리, 선택한 보안 컴플라이언스 프레임워크와 카테고리별 문서화된 컨트롤                                               | 여러 서브섹션                                         |
| **Testing**                       | 테스트 유형, 커버리지 목표, 도구, CI/CD 게이트                                                                                                           | 테이블                                                |
| **Example Code**                  | 엔드포인트, 함수, 테스트, 인프라의 정규(canonical) 패턴을 보여주는 템플릿 코드                                                                           | `examples/` 디렉토리의 동작하는 코드 파일             |

**핵심 원칙**: 무엇이 허용되고 무엇이 그렇지 않은지에 대해 명시적이어야 합니다. 허용 리스트와 거부 리스트는 AI가 가정하는 것을 방지합니다.

전체 가이드: [technical-environment-guide.md](technical-environment-guide.md)
작업 예제: [example-tech-env-scientific-calculator-api.md](example-tech-env-scientific-calculator-api.md)

---

## 최소 입력(Minimum Viable Input)

빠르게 시작하고 세부 사항을 나중에 채우고 싶다면, 최소한 다음을 제공하세요.

### Vision (최소)

```text
1. 무엇을 누구를 위해 만들고 있는지 말하는 한 단락
2. MVP 기능 리스트 (무엇이 스코프 안인지)
3. MVP에 없는 것의 리스트
4. 미해결 질문(Open questions) -- 이미 불확실하거나 해결되지 않은 것을 알고 있는 사항
```

미해결 질문은 선택이지만 가치가 있습니다. 사전 선언된 모호함으로서 Requirements Analysis에 직접 공급되므로, AI-DLC가 설계 중간에 깜짝 등장시키기보다 일찍 다룹니다.

작업 예제는 [example-minimal-vision-scientific-calculator-api.md](example-minimal-vision-scientific-calculator-api.md)를 참고하세요.

### Technical Environment (최소)

```text
1. 언어와 버전
2. 패키지 매니저
3. 웹 프레임워크 (해당하는 경우)
4. 클라우드 제공자와 배포 모델 (또는 "local only")
5. 테스트 프레임워크
6. 금지된 라이브러리와 서비스 -- 테이블 사용: prohibited | reason | use instead
7. Security basics (인증 방법, 입력 검증 접근, 시크릿 관리)
8. 예제 코드 패턴 -- 전형적인 엔드포인트, 함수, 테스트에 대한 짧은 예제 각각 하나
```

**항목 6에 대해**: 이유와 권장 대안을 포함하는 것이 중요합니다. 그것들이 없으면 AI-DLC가 금지를 따를 수는 있어도 좋은 치환 결정을 내릴 만큼 의도를 이해하지 못할 수 있습니다.

**항목 8에 대해**: 짧은 예제 한두 개만으로도 AI-DLC가 코드 생성 중 자체적으로 발명하지 않고 따라야 할 구체적인 패턴을 제공합니다. 이것이 기본을 넘어 단일로 가장 레버리지가 높은 추가입니다.

두 가지 모두에 대한 작업 예제는 [example-minimal-tech-env-scientific-calculator-api.md](example-minimal-tech-env-scientific-calculator-api.md)를 참고하세요.

다른 모든 것은 Inception 단계 동안 AI-DLC의 명확화 질문을 통해 답할 수 있습니다. 사전에 더 많이 제공할수록 AI가 더 적게 물어보게 됩니다.

---

## 브라운필드 프로젝트

기존 코드베이스에 추가하거나 수정하는 경우, 입력 자료는 다른 질문 집합에 답해야 합니다. 전체 가이드는 브라운필드를 자세히 다루지만, 최소는 다음과 같습니다.

### Vision (브라운필드 최소)

```text
1. Current state -- 시스템이 오늘 무엇을 하는지 설명하는 한 단락
2. 추가하거나 변경하는 것 -- 변경에 대한 명확한 설명
3. 이번 이터레이션의 스코프에 있는 기능
4. 이번 이터레이션의 스코프 밖 기능
5. 변경되어서는 안 되는 것 -- 새 작업이 건드리지 말아야 할 기존 컴포넌트, API, 데이터
6. Open questions
```

"변경되어서는 안 되는 것" 섹션이 중요합니다. AI-DLC는 기존 코드베이스를 분석하기 위해 Reverse Engineering 스테이지를 실행하겠지만, 경계에 대해 명시적으로 적어두면 시스템의 작동하는 부분을 불안정하게 만들 변경을 제안하는 것을 막아줍니다.

작업 예제는 [example-minimal-vision-brownfield.md](example-minimal-vision-brownfield.md)를 참고하세요.

### Technical Environment (브라운필드 최소)

```text
1. 기존 스택 -- 언어, 프레임워크, 데이터베이스, 인프라 -- 버전 포함
2. 추가할 것 (새 서비스, 테이블, 컴포넌트)
3. 변경되지 말아야 할 것 -- 건드리지 말아야 할 서비스, 스키마, 계약, 설정
4. 금지된 패턴 -- 기존 코드베이스와 충돌하는 라이브러리나 접근 방식
5. Security basics -- 기존 시스템에서 auth와 시크릿이 어떻게 작동하는지
6. 기존 코드베이스의 예제 코드 패턴
```

예제 코드 패턴은 브라운필드에 특히 중요합니다. AI-DLC는 기존 코드베이스에 속하는 코드를 생성해야지, 오래된 컨벤션 옆에 새 컨벤션을 도입해서는 안 됩니다. 예제는 실제 기존 파일에서 가져오세요.

작업 예제는 [example-minimal-tech-env-brownfield.md](example-minimal-tech-env-brownfield.md)를 참고하세요.

---

## 이 문서들을 제공한 후 무슨 일이 일어나는가

AI-DLC는 두 개의 주요 단계를 거칩니다.

**Inception** -- 이해하고 계획

1. 워크스페이스를 감지 (새 프로젝트 또는 기존 코드)
2. 요구사항 분석 (불명확한 부분이 있으면 명확화 질문)
3. 사용자 스토리 생성 (프로젝트가 필요로 하는 경우)
4. 실행 계획 수립 (어떤 스테이지를 실행하고 어떤 것을 건너뛸지)
5. 컴포넌트와 unit of work 설계 (복잡도가 필요로 하는 경우)

**Construction** -- 설계하고 빌드 (unit of work별)

1. Functional design (비즈니스 로직, 도메인 모델)
2. NFR requirements 및 design (성능, 보안, 확장성)
3. Infrastructure design (실제 클라우드 서비스에 매핑)
4. Code generation (코드, 테스트, 배포 아티팩트 작성)
5. Build and test (빌드 지침, 테스트 실행, 검증)

모든 스테이지는 진행하기 전에 당신의 승인이 필요합니다. 어떤 게이트에서든 변경 요청, 건너뛴 스테이지 추가, 또는 방향 전환을 할 수 있습니다.

---

## 파일 개요

```text
docs/writing-inputs/
  inputs-quickstart.md                               <-- 현재 위치
  vision-document-guide.md                           <-- 비전 문서 작성 방법
  technical-environment-guide.md                     <-- 기술 환경 문서 작성 방법

  -- 그린필드 예제 (밑바닥부터 시작하는 새 프로젝트) --
  example-vision-scientific-calculator-api.md        <-- 전체 예제: CalcEngine 비전
  example-tech-env-scientific-calculator-api.md      <-- 전체 예제: CalcEngine 기술 환경
  example-minimal-vision-scientific-calculator-api.md<-- 최소 예제: CalcEngine 비전
  example-minimal-tech-env-scientific-calculator-api.md<-- 최소 예제: CalcEngine 기술 환경

  -- 브라운필드 예제 (기존 시스템에 추가) --
  example-minimal-vision-brownfield.md               <-- 최소 예제: 기존 플랫폼의 반품 모듈
  example-minimal-tech-env-brownfield.md             <-- 최소 예제: 기존 플랫폼의 반품 모듈
```
