# 적응형 깊이 (Adaptive Depth)

**목적**: AI-DLC가 문제의 복잡도에 맞춰 세부 수준(detail level)을 어떻게 조정하는지 설명합니다.

## 핵심 원칙

**스테이지가 실행되면, 정의된 모든 아티팩트가 생성됩니다. "깊이(depth)"는 그 아티팩트들 안에서의 세부 수준과 엄밀함(rigor)을 가리키며, 문제의 복잡도에 따라 조정됩니다.**

## 스테이지 선택 vs 세부 수준

### 스테이지 선택 (Binary)
- **Workflow Planning**이 결정: 각 스테이지를 EXECUTE할지 SKIP할지
- **EXECUTE인 경우**: 스테이지가 실행되어 정의된 모든 아티팩트를 생성합니다.
- **SKIP인 경우**: 스테이지가 아예 실행되지 않습니다.

### 세부 수준 (적응형)
- **단순한 문제**: 핵심만 담은 간결한 아티팩트
- **복잡한 문제**: 광범위한 세부를 담은 종합 아티팩트
- **모델이 결정**: 규정된 룰이 아니라 문제 특성을 기반으로

## 세부 수준을 결정하는 요인

모델은 적절한 세부 수준을 결정할 때 다음 요인을 고려합니다:

1. **요청 명확성 (Request Clarity)**: 사용자 요청이 얼마나 명확하고 완전한가?
2. **문제 복잡도 (Problem Complexity)**: 솔루션 공간이 얼마나 복잡한가?
3. **범위 (Scope)**: 단일 파일, 컴포넌트, 다중 컴포넌트, 또는 시스템 전체?
4. **리스크 수준 (Risk Level)**: 오류나 누락의 영향이 얼마나 큰가?
5. **사용 가능한 컨텍스트 (Available Context)**: 그린필드 vs 브라운필드, 기존 문서 유무
6. **사용자 선호 (User Preferences)**: 사용자가 간결함이나 상세함에 대한 선호를 표현했는가?

## 예시: Requirements Analysis 아티팩트

**모든 시나리오에서 동일한 아티팩트를 생성**:
- `requirement-verification-questions.md` (필요한 경우)
- `requirements.md`

**참고**: 사용자의 초기 요청은 `audit.md`에 캡처됩니다(별도의 user-intent.md 불필요).

**세부 수준은 복잡도에 따라 달라집니다**:

### 단순 시나리오 (버그 수정)
- **requirement-verification-questions.md**: 꼭 필요한 명확화 질문
- **requirements.md**: 간결한 기능 요구사항, 최소한의 섹션

### 복잡 시나리오 (시스템 마이그레이션)
- **requirement-verification-questions.md**: 여러 라운드, 10개 이상의 질문
- **requirements.md**: 종합적인 기능/비기능 요구사항, 추적성(traceability), 수락 기준(acceptance criteria)

## 예시: Application Design 아티팩트

**모든 시나리오에서 동일한 아티팩트를 생성**:
- `application-design.md`
- `component-diagram.md`

**세부 수준은 복잡도에 따라 달라집니다**:

### 단순 시나리오 (단일 컴포넌트)
- **application-design.md**: 기본 컴포넌트 설명, 주요 메서드
- **component-diagram.md**: 핵심 관계만 담은 단순 다이어그램

### 복잡 시나리오 (다중 컴포넌트 시스템)
- **application-design.md**: 컴포넌트의 상세 책임, 시그니처를 포함한 모든 메서드, 설계 패턴, 검토한 대안
- **component-diagram.md**: 모든 관계, 데이터 흐름, 통합 지점을 담은 종합 다이어그램

## 모델을 위한 안내 원칙

**"눈앞의 문제에 정확히 필요한 만큼의 세부를 만드세요 — 더도, 덜도 말고."**

- 단순한 문제를 불필요한 세부로 부풀리지 마세요.
- 복잡한 문제에 결정적인 세부를 빠뜨리지 마세요.
- 문제 특성이 세부 수준을 자연스럽게 이끌도록 두세요.
- 스테이지가 실행될 때 필수 아티팩트는 항상 모두 생성됩니다.
