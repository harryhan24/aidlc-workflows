# AI-DLC 적응형 워크플로우 개요 (Process Overview)

**목적**: AI 모델과 개발자가 전체 워크플로우 구조를 이해할 수 있도록 하는 기술 레퍼런스.

**참고**: welcome-message.md(사용자 환영 메시지)와 README.md(문서)에 비슷한 내용이 존재합니다. 이 중복은 의도적입니다 — 각 파일이 다른 목적을 가집니다:
- **이 파일**: AI 모델의 컨텍스트 로딩을 위한 Mermaid 다이어그램이 포함된 상세 기술 레퍼런스
- **welcome-message.md**: ASCII 다이어그램이 포함된 사용자 대상 환영 메시지
- **README.md**: 저장소를 위한 사람이 읽는 문서

## 3단계 라이프사이클:
• **INCEPTION PHASE**: 기획과 아키텍처 (Workspace Detection + 조건부 단계 + Workflow Planning)
• **CONSTRUCTION PHASE**: 설계, 구현, 빌드/테스트 (단위별 설계 + Code Generation + Build & Test)
• **OPERATIONS PHASE**: 향후 배포·모니터링 워크플로우를 위한 플레이스홀더

## 적응형 워크플로우:
• **Workspace Detection** (항상) → **Reverse Engineering** (brownfield 전용) → **Requirements Analysis** (항상, 적응형 깊이) → **조건부 단계** (필요 시) → **Workflow Planning** (항상) → **Code Generation** (항상, 단위별) → **Build and Test** (항상)

## 동작 방식:
• **AI가 분석**: 요청, 워크스페이스, 복잡도를 분석해 어떤 스테이지가 필요한지 결정합니다.
• **항상 실행되는 스테이지**: Workspace Detection, Requirements Analysis(적응형 깊이), Workflow Planning, Code Generation(단위별), Build and Test
• **그 외 모든 스테이지는 조건부**: Reverse Engineering, User Stories, Application Design, Units Generation, 단위별 설계 스테이지(Functional Design, NFR Requirements, NFR Design, Infrastructure Design)
• **고정된 순서 없음**: 스테이지는 구체적 작업에 맞는 순서로 실행됩니다.

## 팀의 역할:
• 전용 질문 파일에서 `[Answer]:` 태그와 알파벳 선택지(A, B, C, D, E)를 사용해 **질문에 답합니다.**
• **옵션 E 가능**: 제시된 옵션이 맞지 않으면 "Other"를 선택해 직접 답변을 적습니다.
• 진행 전에 각 단계를 검토·승인하는 **팀 작업**입니다.
• 필요할 때 아키텍처 접근 방식을 **공동으로 결정**합니다.
• **중요**: 이건 팀 작업입니다 — 각 단계에 관련 이해관계자를 참여시키세요.

## AI-DLC 3단계 워크플로우:

```mermaid
flowchart TD
    Start(["User Request"])
    
    subgraph INCEPTION["🔵 INCEPTION PHASE"]
        WD["Workspace Detection<br/><b>ALWAYS</b>"]
        RE["Reverse Engineering<br/><b>CONDITIONAL</b>"]
        RA["Requirements Analysis<br/><b>ALWAYS</b>"]
        Stories["User Stories<br/><b>CONDITIONAL</b>"]
        WP["Workflow Planning<br/><b>ALWAYS</b>"]
        AppDesign["Application Design<br/><b>CONDITIONAL</b>"]
        UnitsG["Units Generation<br/><b>CONDITIONAL</b>"]
    end
    
    subgraph CONSTRUCTION["🟢 CONSTRUCTION PHASE"]
        FD["Functional Design<br/><b>CONDITIONAL</b>"]
        NFRA["NFR Requirements<br/><b>CONDITIONAL</b>"]
        NFRD["NFR Design<br/><b>CONDITIONAL</b>"]
        ID["Infrastructure Design<br/><b>CONDITIONAL</b>"]
        CG["Code Generation<br/><b>ALWAYS</b>"]
        BT["Build and Test<br/><b>ALWAYS</b>"]
    end
    
    subgraph OPERATIONS["🟡 OPERATIONS PHASE"]
        OPS["Operations<br/><b>PLACEHOLDER</b>"]
    end
    
    Start --> WD
    WD -.-> RE
    WD --> RA
    RE --> RA
    
    RA -.-> Stories
    RA --> WP
    Stories --> WP
    
    WP -.-> AppDesign
    WP -.-> UnitsG
    AppDesign -.-> UnitsG
    UnitsG --> FD
    FD -.-> NFRA
    NFRA -.-> NFRD
    NFRD -.-> ID
    
    WP --> CG
    FD --> CG
    NFRA --> CG
    NFRD --> CG
    ID --> CG
    CG -.->|Next Unit| FD
    CG --> BT
    BT -.-> OPS
    BT --> End(["Complete"])
    
    style WD fill:#4CAF50,stroke:#1B5E20,stroke-width:3px,color:#fff
    style RA fill:#4CAF50,stroke:#1B5E20,stroke-width:3px,color:#fff
    style WP fill:#4CAF50,stroke:#1B5E20,stroke-width:3px,color:#fff

    style CG fill:#4CAF50,stroke:#1B5E20,stroke-width:3px,color:#fff
    style BT fill:#4CAF50,stroke:#1B5E20,stroke-width:3px,color:#fff
    style OPS fill:#BDBDBD,stroke:#424242,stroke-width:2px,stroke-dasharray: 5 5,color:#000
    style RE fill:#FFA726,stroke:#E65100,stroke-width:3px,stroke-dasharray: 5 5,color:#000
    style Stories fill:#FFA726,stroke:#E65100,stroke-width:3px,stroke-dasharray: 5 5,color:#000
    style AppDesign fill:#FFA726,stroke:#E65100,stroke-width:3px,stroke-dasharray: 5 5,color:#000

    style UnitsG fill:#FFA726,stroke:#E65100,stroke-width:3px,stroke-dasharray: 5 5,color:#000
    style FD fill:#FFA726,stroke:#E65100,stroke-width:3px,stroke-dasharray: 5 5,color:#000
    style NFRA fill:#FFA726,stroke:#E65100,stroke-width:3px,stroke-dasharray: 5 5,color:#000
    style NFRD fill:#FFA726,stroke:#E65100,stroke-width:3px,stroke-dasharray: 5 5,color:#000
    style ID fill:#FFA726,stroke:#E65100,stroke-width:3px,stroke-dasharray: 5 5,color:#000
    style INCEPTION fill:#BBDEFB,stroke:#1565C0,stroke-width:3px, color:#000
    style CONSTRUCTION fill:#C8E6C9,stroke:#2E7D32,stroke-width:3px, color:#000
    style OPERATIONS fill:#FFF59D,stroke:#F57F17,stroke-width:3px, color:#000
    style Start fill:#CE93D8,stroke:#6A1B9A,stroke-width:3px,color:#000
    style End fill:#CE93D8,stroke:#6A1B9A,stroke-width:3px,color:#000
    
    linkStyle default stroke:#333,stroke-width:2px
```

**스테이지 설명:**

**🔵 INCEPTION PHASE** - 기획과 아키텍처
- Workspace Detection: 워크스페이스 상태와 프로젝트 유형을 분석 (ALWAYS)
- Reverse Engineering: 기존 코드베이스 분석 (CONDITIONAL - Brownfield 전용)
- Requirements Analysis: 요구사항 수집·검증 (ALWAYS - 적응형 깊이)
- User Stories: 사용자 스토리와 페르소나 작성 (CONDITIONAL)
- Workflow Planning: 실행 계획 작성 (ALWAYS)
- Application Design: 상위 컴포넌트 식별과 서비스 레이어 설계 (CONDITIONAL)
- Units Generation: 작업 단위로 분해 (CONDITIONAL)

**🟢 CONSTRUCTION PHASE** - 설계, 구현, 빌드/테스트
- Functional Design: 단위별 상세 비즈니스 로직 설계 (CONDITIONAL, 단위별)
- NFR Requirements: NFR 결정 및 기술 스택 선택 (CONDITIONAL, 단위별)
- NFR Design: NFR 패턴과 논리 컴포넌트 반영 (CONDITIONAL, 단위별)
- Infrastructure Design: 실제 인프라 서비스로 매핑 (CONDITIONAL, 단위별)
- Code Generation: Part 1 - Planning, Part 2 - Generation으로 코드 생성 (ALWAYS, 단위별)
- Build and Test: 모든 단위를 빌드하고 종합 테스트 실행 (ALWAYS)

**🟡 OPERATIONS PHASE** - 플레이스홀더
- Operations: 향후 배포·모니터링 워크플로우를 위한 플레이스홀더 (PLACEHOLDER)

**핵심 원칙:**
- 각 단계는 가치를 더할 때만 실행
- 각 단계는 독립적으로 평가
- INCEPTION은 "무엇/왜"에 집중
- CONSTRUCTION은 "어떻게"와 "빌드/테스트"에 집중
- OPERATIONS는 향후 확장을 위한 플레이스홀더
- 단순한 변경은 조건부 INCEPTION 스테이지를 건너뛸 수 있음
- 복잡한 변경은 INCEPTION과 CONSTRUCTION의 완전한 처리를 받음
