# Workspace Detection (워크스페이스 감지)

**목적**: 워크스페이스 상태를 판단하고 기존 AI-DLC 프로젝트가 있는지 확인합니다.

## Step 1: 기존 AI-DLC 프로젝트 확인

`aidlc-docs/aidlc-state.md`가 존재하는지 확인:
- **존재하면**: 마지막 단계부터 재개 (이전 단계의 컨텍스트를 로드)
- **없으면**: 새 프로젝트 평가로 진행

## Step 2: 기존 코드가 있는지 워크스페이스 스캔

**워크스페이스에 기존 코드가 있는지 판단:**
- 소스 코드 파일을 스캔 (.java, .py, .js, .ts, .jsx, .tsx, .kt, .kts, .scala, .groovy, .go, .rs, .rb, .php, .c, .h, .cpp, .hpp, .cc, .cs, .fs 등)
- 빌드 파일 확인 (pom.xml, package.json, build.gradle 등)
- 프로젝트 구조 지표 확인
- 워크스페이스 루트 디렉터리 식별 (aidlc-docs/ 가 아님)

**발견사항 기록:**
```markdown
## Workspace State
- **Existing Code**: [Yes/No]
- **Programming Languages**: [발견 시 목록]
- **Build System**: [Maven/Gradle/npm 등 발견 시]
- **Project Structure**: [Monolith/Microservices/Library/Empty]
- **Workspace Root**: [절대 경로]
```

## Step 3: 다음 단계 결정

**워크스페이스가 비어 있다면 (기존 코드 없음)**:
- 플래그 설정: `brownfield = false`
- 다음 단계: Requirements Analysis

**워크스페이스에 기존 코드가 있다면**:
- 플래그 설정: `brownfield = true`
- `aidlc-docs/inception/reverse-engineering/`에 기존 리버스 엔지니어링 아티팩트가 있는지 확인
- **리버스 엔지니어링 아티팩트가 있다면**:
    - 아티팩트가 오래되었는지 확인 (아티팩트 타임스탬프를 코드베이스의 마지막 의미 있는 수정 시각과 비교)
    - **아티팩트가 최신이라면**: 로드 후 Requirements Analysis로 스킵
    - **아티팩트가 오래되었다면**: 다음 단계는 Reverse Engineering (아티팩트 갱신을 위해 재실행)
    - **사용자가 명시적으로 재실행을 요청하면**: 신선도와 무관하게 다음 단계는 Reverse Engineering
- **리버스 엔지니어링 아티팩트가 없다면**: 다음 단계는 Reverse Engineering

## Step 4: 초기 상태 파일 생성

`aidlc-docs/aidlc-state.md` 생성:

```markdown
# AI-DLC State Tracking

## Project Information
- **Project Type**: [Greenfield/Brownfield]
- **Start Date**: [ISO timestamp]
- **Current Stage**: INCEPTION - Workspace Detection

## Workspace State
- **Existing Code**: [Yes/No]
- **Reverse Engineering Needed**: [Yes/No]
- **Workspace Root**: [절대 경로]

## Code Location Rules
- **Application Code**: Workspace root (aidlc-docs/ 에 두지 마세요)
- **Documentation**: aidlc-docs/ 전용
- **Structure patterns**: code-generation.md의 Critical Rules 참고

## Stage Progress
[워크플로우가 진행되면서 채워집니다]
```

## Step 5: 완료 메시지 제시

**Brownfield 프로젝트:**
```markdown
# 🔍 Workspace Detection Complete

Workspace 분석 결과:
• **Project Type**: Brownfield project
• [AI가 생성한 워크스페이스 발견사항 요약을 불릿으로]
• **Next Step**: 기존 코드베이스 분석을 위해 **Reverse Engineering**으로 진행 중...
```

**Greenfield 프로젝트:**
```markdown
# 🔍 Workspace Detection Complete

Workspace 분석 결과:
• **Project Type**: Greenfield project
• **Next Step**: **Requirements Analysis**로 진행 중...
```

## Step 6: 자동으로 진행

- **사용자 승인 불필요** — 이 단계는 정보 제공용입니다.
- 자동으로 다음 단계로 진행:
  - **Brownfield**: Reverse Engineering (기존 아티팩트 없는 경우) 또는 Requirements Analysis (아티팩트 있는 경우)
  - **Greenfield**: Requirements Analysis
