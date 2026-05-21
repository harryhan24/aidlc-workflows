# Vision Document 작성 가이드

## 목적

Vision Document는 프로젝트가 AI-DLC 워크플로우에 진입하기 전에 **비즈니스 목표**, **타겟 결과(target outcomes)**, **스코프 경계**를 정의합니다. Inception 단계의 주 입력으로 기능하며, AI 모델과 팀에게 프로젝트가 무엇을 달성하려 하고 왜 중요한지에 대한 공유된 이해를 제공합니다.

잘 작성된 Vision Document는 Requirements Analysis 동안의 모호함을 줄이고, User Story 품질을 개선하며, Construction 동안의 스코프 크리프를 방지합니다.

## 언제 Vision Document를 작성하는가

- 새 프로젝트나 주요 이니셔티브를 시작하기 전
- 새 제품, 기능 집합, 플랫폼을 제안할 때
- 기존 제품을 새로운 방향으로 피벗할 때
- 개발 시작 전에 여러 스테이크홀더가 목표에 정렬되어야 할 때

## 문서 구조

### 1. Executive Summary

프로젝트의 본질을 포착하는 짧은 단락(3-5 문장). 이 섹션만 읽은 사람도 프로젝트가 무엇인지, 누구를 위한 것인지, 왜 존재하는지를 이해할 수 있어야 합니다.

**Template:**

```markdown
## Executive Summary

[Project Name] is a [type of system/product] that enables [target users] to [core capability].
It addresses [business problem or opportunity] by [approach or differentiation].
The expected outcome is [measurable business result].
```

**Example:**

```markdown
## Executive Summary

OrderFlow is a web-based order management platform that enables mid-size retailers to
track inventory, process customer orders, and manage supplier relationships in a single
interface. It addresses the fragmented tooling problem that causes fulfillment delays
and inventory mismatches. The expected outcome is a 30% reduction in order processing
time and elimination of manual inventory reconciliation.
```

---

### 2. Business Context

비즈니스 환경, 해결하려는 문제, 그리고 지금 그것을 해결하는 것이 왜 중요한지를 설명합니다.

**포함할 섹션:**

```markdown
## Business Context

### Problem Statement
[이 프로젝트가 해결하는 구체적인 비즈니스 문제나 페인 포인트는 무엇인가?
구체적이어야 합니다. "효율성 향상" 같은 모호한 표현은 피하세요.]

### Business Drivers
[왜 이 프로젝트가 지금 추진되고 있는가? 어떤 시장 상황, 경쟁 압력,
규제 변화 또는 내부 필요가 이를 시기적절하게 만드는가?]

### Target Users and Stakeholders
[누가 시스템을 사용할 것인가? 누가 그 성공에 이해관계를 가지는가?
사용자 유형과 각각에 대한 간단한 설명을 나열하세요.]

| User Type | Description | Primary Need |
|-----------|-------------|--------------|
| [Role]    | [Who they are] | [What they need from this system] |

### Business Constraints
[예산 한계, 규제 요건, 조직 정책, 일정 압박, 또는 기타
협상 불가능한 경계.]

### Success Metrics
[비즈니스가 이 프로젝트가 성공했는지 어떻게 측정할 것인가?
구체적이고 측정 가능한 기준을 사용하세요.]

| Metric | Current State | Target State | Measurement Method |
|--------|--------------|--------------|-------------------|
| [Metric name] | [Baseline] | [Goal] | [How measured] |
```

---

### 3. Full Scope Vision

이 섹션은 제품 또는 시스템에 대한 **완전한 장기 비전**을 기술합니다. 의도적으로 열망적이며, 처음 만들어질 것뿐만 아니라 프로젝트가 될 수 있는 모든 것을 다룹니다.

**포함할 섹션:**

```markdown
## Full Scope Vision

### Product Vision Statement
[제품의 장기 열망 상태를 포착하는 한 문장 또는 짧은 단락. 이 제품이
완전히 실현되었을 때 세계는 어떤 모습인가?]

### Feature Areas
[전체 기능 집합을 논리적 그룹으로 정리하세요. 각 영역에 대해
완전히 성숙한 상태에서 시스템이 무엇을 할지 기술하세요.]

#### Feature Area 1: [Name]
- **Description**: [이 영역이 다루는 내용]
- **Key Capabilities**:
  - [Capability 1]
  - [Capability 2]
  - [Capability 3]
- **User Value**: [이것이 사용자에게 중요한 이유]

#### Feature Area 2: [Name]
[같은 구조]

### Integration Points
[완전 성숙 단계에서 시스템이 통합될 외부 시스템, API, 데이터 소스는
무엇인가?]

- [System/Service] - [Purpose of integration]

### User Journeys (Full Vision)
[완전한 제품 경험을 대표하는 2-3개의 종단 간 사용자 여정을 기술하세요.
MVP가 아니라 전체 스코프를 반영해야 합니다.]

#### Journey 1: [Name]
1. [Step]
2. [Step]
3. [Step]
**Outcome**: [사용자가 달성하는 것]

### Scalability and Growth
[제품은 어떻게 성장할 것으로 예상되는가? 새 시장, 사용자 유형, 지역,
데이터 볼륨, 기능 카테고리?]

### Long-Term Roadmap (Optional)
[알려진 경우 MVP 이후의 고수준 단계나 마일스톤을 개략적으로
기술하세요. 방향성이며, 확정이 아닙니다.]

| Phase | Focus | Timeframe (if known) |
|-------|-------|---------------------|
| MVP | [Core scope] | [Target] |
| Phase 2 | [Expansion area] | [Target] |
| Phase 3 | [Further expansion] | [Target] |
```

---

### 4. MVP Scope

이 섹션은 **minimum viable product**를 정의합니다: 측정 가능한 가치를 전달하고 핵심 비즈니스 가설을 검증하는 가장 작은 기능 집합. 여기 나열된 모든 것은 제품이 출시되거나 평가될 수 있기 전에 만들어져야 합니다.

**포함할 섹션:**

```markdown
## MVP Scope

### MVP Objective
[MVP가 증명하거나 전달해야 할 가장 중요한 단 한 가지는 무엇인가?
1-2 문장으로 유지하세요.]

### MVP Success Criteria
[MVP가 성공했다는 것을 어떻게 알 수 있는가? 이는 테스트 가능하고
구체적이어야 합니다.]

- [ ] [Criterion 1]
- [ ] [Criterion 2]
- [ ] [Criterion 3]

### Features In Scope (MVP)
[MVP에 포함된 모든 기능을 나열하세요. 명시적이어야 합니다. 여기 나열되지
않으면 MVP에 없는 것입니다.]

| Feature | Description | Priority | Rationale for Inclusion |
|---------|-------------|----------|------------------------|
| [Feature name] | [Brief description] | Must Have | [연기될 수 없는 이유] |

### Features Explicitly Out of Scope (MVP)
[Full Scope Vision에서 MVP에서 의도적으로 제외된 기능을 나열하세요.
각각이 왜 연기되는지 명시하세요. 스코프 크리프를 방지합니다.]

| Feature | Reason for Deferral | Target Phase |
|---------|-------------------|--------------|
| [Feature name] | [기다릴 수 있는 이유] | [Phase 2/3/TBD] |

### MVP User Journeys
[MVP가 지원해야 할 사용자 여정을 기술하세요. 이는 Full Vision 여정의
부분집합 또는 단순화된 버전입니다.]

#### Journey 1: [Name]
1. [Step]
2. [Step]
3. [Step]
**Outcome**: [사용자가 달성하는 것]
**Limitation vs Full Vision**: [전체 스코프 대비 단순화되거나 빠진 것]

### MVP Constraints and Assumptions
[MVP는 어떤 가정 위에 만들어졌는가? 어떤 알려진 제한이 수용되는가?]

- **Assumption**: [Statement] - **Risk if wrong**: [Consequence]
- **Accepted Limitation**: [의도적으로 제한된 것과 그 이유]

### MVP Definition of Done
[MVP가 완료되고 평가 또는 출시 준비가 되었다고 간주되려면 무엇이
참이어야 하는가?]

- [ ] 모든 "Must Have" 기능이 구현되고 테스트됨
- [ ] [이 프로젝트에 특정한 추가 기준]
- [ ] [배포 또는 접근성 요구사항]
- [ ] [스테이크홀더 사인-오프 요구사항]
```

---

### 5. Risks and Dependencies

```markdown
## Risks and Dependencies

### Key Risks
| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| [Risk description] | High/Medium/Low | High/Medium/Low | [Mitigation strategy] |

### External Dependencies
[팀의 통제 밖에 있으면서 프로젝트가 의존하는 것을 나열하세요.]

- [Dependency] - [Owner] - [Status]

### Open Questions
[개발 전 또는 중에 답이 필요한 미해결 질문을 나열하세요.
이는 Requirements Analysis의 명확화 질문에 직접 공급됩니다.]

- [ ] [Question]
- [ ] [Question]
```

---

## 작성 가이드라인

### 권장

- 구체적이고 측정 가능하게. "주문 처리 시간을 30% 단축"이 "더 빠르게 만들자"보다 낫습니다.
- 전체 비전과 MVP를 명확히 분리하세요. 이를 섞으면 스코프 크리프를 야기합니다.
- "스코프 밖" 리스트를 포함하세요. "스코프 안" 리스트만큼 가치 있습니다.
- 임원이 아닌 팀을 위해 작성하세요. 마케팅 언어를 피하세요.
- 가정을 명시적으로 진술하여 도전받을 수 있게 하세요.
- 실제로 테스트할 수 있는 성공 기준을 포함하세요.

### 비권장

- 모호한 언어 사용: "world-class," "seamless," "intuitive," "best-in-class."
- 기술이나 구현 세부 사항을 나열. 그건 Technical Environment Document에 속합니다.
- MVP 섹션 건너뛰기. 모든 프로젝트는 정의된 시작 경계가 필요합니다.
- 기능과 사용자 여정을 결합. 기능은 시스템이 무엇을 하는지를 기술하고, 여정은 사용자가 어떻게 그것을 경험하는지를 기술합니다.
- 독자가 비즈니스 컨텍스트를 안다고 가정. 명백해 보이더라도 Problem Statement를 작성하세요.

---

## 이 문서가 AI-DLC에 어떻게 공급되는가

| Vision Document 섹션     | AI-DLC Stage                     | 어떻게 사용되는가                                            |
| ------------------------ | -------------------------------- | ------------------------------------------------------------ |
| Executive Summary        | Workspace Detection              | 프로젝트 분류를 위한 초기 컨텍스트                           |
| Business Context         | Requirements Analysis            | 명확화 질문과 요구사항 깊이를 견인                           |
| Full Scope Vision        | User Stories, Application Design | 페르소나 생성, 컴포넌트 식별에 정보 제공                     |
| MVP Scope                | Workflow Planning                | 어떤 스테이지가 실행되는지, 스코프 경계 결정                 |
| Features In/Out of Scope | Code Generation                  | 이 이터레이션에서 무엇이 만들어지는지 정의                   |
| Risks and Dependencies   | All stages                       | 위험 평가와 에러 처리에 정보 제공                            |
| Open Questions           | Requirements Analysis            | 질문 파일의 명확화 질문이 됨                                 |
