# Adaptive Depth

**Purpose**: AI-DLC가 문제 복잡도에 따라 detail level을 어떻게 조정하는지 설명합니다.

## Core Principle

**stage가 실행되면 그 stage에 정의된 모든 artifact를 생성합니다. "depth"는 artifact 안의 detail과 rigor 수준을 의미하며, 문제의 복잡도에 맞게 조정됩니다.**

## Stage Selection vs Detail Level

### Stage Selection (Binary)

- **Workflow Planning**은 각 stage에 대해 EXECUTE 또는 SKIP을 결정합니다.
- **If EXECUTE**: stage가 실행되고 정의된 모든 artifact를 생성합니다.
- **If SKIP**: stage는 전혀 실행되지 않습니다.

### Detail Level (Adaptive)

- **Simple problems**: 필수 detail만 담은 간결한 artifact
- **Complex problems**: 폭넓은 detail을 담은 포괄적인 artifact
- **Model decides**: 규범적인 rule이 아니라 problem characteristics를 기준으로 결정합니다.

## Factors Influencing Detail Level

모델은 적절한 detail을 결정할 때 다음 요소를 고려합니다.

1. **Request Clarity**: 사용자의 요청이 얼마나 명확하고 완전한가?
2. **Problem Complexity**: solution space가 얼마나 복잡한가?
3. **Scope**: single file, component, multiple components, system-wide 중 어디에 해당하는가?
4. **Risk Level**: error 또는 omission의 영향이 얼마나 큰가?
5. **Available Context**: greenfield vs brownfield, existing documentation
6. **User Preferences**: 사용자가 간결함 또는 상세함에 대한 선호를 표현했는가?

## Example: Requirements Analysis Artifacts

**모든 scenario는 같은 artifact를 생성합니다.**

- `requirement-verification-questions.md`(필요한 경우)
- `requirements.md`

**Note**: 사용자의 initial request는 `audit.md`에 캡처됩니다(별도 user-intent.md 불필요).

**Detail level은 complexity에 따라 달라집니다.**

### Simple Scenario (Bug Fix)

- **requirement-verification-questions.md**: 필요한 clarifying questions
- **requirements.md**: 간결한 functional requirement, 최소 섹션

### Complex Scenario (System Migration)

- **requirement-verification-questions.md**: 여러 차례, 10개 이상의 질문
- **requirements.md**: comprehensive functional + non-functional requirements, traceability, acceptance criteria

## Example: Application Design Artifacts

**모든 scenario는 같은 artifact를 생성합니다.**

- `application-design.md`
- `component-diagram.md`

**Detail level은 complexity에 따라 달라집니다.**

### Simple Scenario (Single Component)

- **application-design.md**: 기본 component description, key methods
- **component-diagram.md**: essential relationships가 담긴 simple diagram

### Complex Scenario (Multi-Component System)

- **application-design.md**: 상세 component responsibilities, signatures가 포함된 모든 methods, design patterns, 검토한 alternatives
- **component-diagram.md**: 모든 relationships, data flows, integration points가 포함된 comprehensive diagram

## Guiding Principle for Model

**"현재 문제에 필요한 만큼만 정확히 detail을 작성합니다. 과하지도 부족하지도 않게."**

- 불필요한 detail로 simple problem을 인위적으로 부풀리지 마세요.
- 중요한 detail을 생략해 complex problem을 축소하지 마세요.
- problem characteristics가 detail level을 자연스럽게 결정하게 하세요.
- stage가 실행되면 모든 required artifacts는 항상 생성합니다.
