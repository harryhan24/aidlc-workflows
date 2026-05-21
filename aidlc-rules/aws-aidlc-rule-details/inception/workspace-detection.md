# Workspace Detection

**Purpose**: workspace state를 판단하고 기존 AI-DLC project가 있는지 확인합니다.

## Step 1: Check for Existing AI-DLC Project

`aidlc-docs/aidlc-state.md`가 존재하는지 확인합니다.

- **If exists**: 마지막 phase부터 resume합니다(이전 phases의 context 로드).
- **If not exists**: new project assessment를 계속합니다.

## Step 2: Scan Workspace for Existing Code

**workspace에 existing code가 있는지 판단합니다.**

- source code files를 스캔합니다(.java, .py, .js, .ts, .jsx, .tsx, .kt, .kts, .scala, .groovy, .go, .rs, .rb, .php, .c, .h, .cpp, .hpp, .cc, .cs, .fs 등).
- build files를 확인합니다(pom.xml, package.json, build.gradle 등).
- project structure indicators를 찾습니다.
- workspace root directory를 식별합니다(aidlc-docs/가 아님).

**Record findings:**

```markdown
## Workspace State
- **Existing Code**: [Yes/No]
- **Programming Languages**: [List if found]
- **Build System**: [Maven/Gradle/npm/etc. if found]
- **Project Structure**: [Monolith/Microservices/Library/Empty]
- **Workspace Root**: [Absolute path]
```

## Step 3: Determine Next Phase

**IF workspace is empty(no existing code)**:

- flag 설정: `brownfield = false`
- Next phase: Requirements Analysis

**IF workspace has existing code**:

- flag 설정: `brownfield = true`
- `aidlc-docs/inception/reverse-engineering/`에 기존 reverse engineering artifacts가 있는지 확인합니다.
- **IF reverse engineering artifacts exist**:
  - artifacts가 stale한지 확인합니다(artifact timestamps와 codebase의 last significant modification 비교).
  - **IF artifacts are current**: artifacts를 로드하고 Requirements Analysis로 건너뜁니다.
  - **IF artifacts are stale**: 다음 phase는 Reverse Engineering입니다(artifacts refresh를 위해 다시 실행).
  - **IF user explicitly requests rerun**: staleness와 관계없이 다음 phase는 Reverse Engineering입니다.
- **IF no reverse engineering artifacts**: 다음 phase는 Reverse Engineering입니다.

## Step 4: Create Initial State File

`aidlc-docs/aidlc-state.md`를 생성합니다.

```markdown
# AI-DLC State Tracking

## Project Information
- **Project Type**: [Greenfield/Brownfield]
- **Start Date**: [ISO timestamp]
- **Current Stage**: INCEPTION - Workspace Detection

## Workspace State
- **Existing Code**: [Yes/No]
- **Reverse Engineering Needed**: [Yes/No]
- **Workspace Root**: [Absolute path]

## Code Location Rules
- **Application Code**: Workspace root (NEVER in aidlc-docs/)
- **Documentation**: aidlc-docs/ only
- **Structure patterns**: See code-generation.md Critical Rules

## Stage Progress
[Will be populated as workflow progresses]
```

## Step 5: Present Completion Message

**For Brownfield Projects:**

```markdown
# 🔍 Workspace Detection Complete

Workspace analysis findings:
• **Project Type**: Brownfield project
• [AI-generated summary of workspace findings in bullet points]
• **Next Step**: 기존 codebase를 분석하기 위해 **Reverse Engineering**으로 진행합니다...
```

**For Greenfield Projects:**

```markdown
# 🔍 Workspace Detection Complete

Workspace analysis findings:
• **Project Type**: Greenfield project
• **Next Step**: **Requirements Analysis**로 진행합니다...
```

## Step 6: Automatically Proceed

- **user approval 불필요** - informational only입니다.
- 다음 phase로 자동 진행합니다.
  - **Brownfield**: Reverse Engineering(기존 artifacts가 없는 경우) 또는 Requirements Analysis(artifacts가 있는 경우)
  - **Greenfield**: Requirements Analysis
