# AI-DLC (AI-Driven Development Life Cycle)

> [!IMPORTANT]
> 생성형 AI는 실수를 할 수 있습니다. 선택한 AI 모델과 에이전틱 코딩 어시스턴트가 생성한 모든 출력물과 비용을 검토하는 것이 좋습니다. [AWS Responsible AI Policy](https://aws.amazon.com/ai/responsible-ai/policy/)를 참고하세요.

<!-- TODO: Replace this Amplify URL with a permanent/stable URL when available -->
AI-DLC는 여러분의 필요에 맞춰 적응하고, 품질 기준을 유지하며, 프로세스의 통제권을 여러분에게 두는 지능형 소프트웨어 개발 워크플로우입니다. AI-DLC 방법론(Methodology)에 대해 더 알아보려면 이 [블로그](https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/)와 거기서 참조하는 [Method Definition Paper](https://prod.d13rzhkk8cj2z0.amplifyapp.com/)를 읽어보세요.

## 목차

- [공통](#공통)
- [플랫폼별 설정](#플랫폼별-설정)
- [사용법](#사용법)
- [3단계 적응형 워크플로우](#3단계-적응형-워크플로우)
- [주요 기능](#주요-기능)
- [익스텐션](#익스텐션)
- [부속 도구](#부속-도구)
- [원칙(Tenets)](#원칙tenets)
- [사전 준비물](#사전-준비물)
- [트러블슈팅](#트러블슈팅)
- [버전 관리 권장사항](#버전-관리-권장사항)
- [추가 자료](#추가-자료)
- [생성되는 aidlc-docs/ 레퍼런스](#생성되는-aidlc-docs-레퍼런스)
- [실험적 기능: AI 보조 설정 (릴리스 다운로드)](#실험적-기능-ai-보조-설정-릴리스-다운로드)
- [기여하기](#기여하기)
- [라이선스](#라이선스)

---

## 공통

1. [Releases 페이지](../../releases/latest)에서 `ai-dlc-rules-v<release-number>.zip` 이라는 이름의 최신 릴리스 zip 파일을 프로젝트 디렉터리 **바깥**의 폴더(예: `~/Downloads`)로 다운로드합니다.
2. zip을 압축 해제합니다. 안에는 두 개의 하위 디렉터리를 가진 `aidlc-rules/` 폴더가 들어 있습니다:
   - `aws-aidlc-rules/` — AI-DLC 워크플로우의 핵심 룰(rule)
   - `aws-aidlc-rule-details/` — 핵심 룰이 조건부로 참조하는 상세 룰
3. 아래에서 사용하는 코딩 에이전트와 플랫폼에 맞는 설정 안내를 따릅니다.

---

## 플랫폼별 설정

- [Kiro](#kiro)
- [Amazon Q Developer IDE 플러그인](#amazon-q-developer-ide-플러그인확장)
- [Cursor IDE](#cursor-ide)
- [Cline](#cline)
- [Claude Code](#claude-code)
- [GitHub Copilot](#github-copilot)

---

### Kiro

AI-DLC는 프로젝트 워크스페이스 내에서 [Kiro Steering Files](https://kiro.dev/docs/cli/steering/)를 사용합니다.

아래 명령어들은 zip을 `Downloads` 폴더에 압축 해제하여 결과 경로가 `Downloads/aidlc-rules/`가 된 상황을 전제로 합니다. 다른 위치를 사용했다면 `Downloads`를 실제 폴더 경로로 바꾸세요.

> [!NOTE]
> **Windows 사용자:** File Explorer의 **Extract All...** 대화상자를 사용했다면 zip 이름으로 된 래퍼 폴더(예: `ai-dlc-rules-v0.1.8\aidlc-rules\...`)가 기본으로 생성됩니다. 해당 옵션을 해제/수정해서 내용물이 `Downloads\aidlc-rules\`에 바로 들어가게 하거나(아래 명령과 일치), 명령어의 각 `Downloads\` 경로 앞에 `ai-dlc-rules-v<version>\`을 붙이세요 — `<version>`은 다운로드한 릴리스로 치환합니다.

macOS/Linux:

```bash
mkdir -p .kiro/steering
cp -R ~/Downloads/aidlc-rules/aws-aidlc-rules .kiro/steering/
cp -R ~/Downloads/aidlc-rules/aws-aidlc-rule-details .kiro/
```

Windows (PowerShell):

```powershell
New-Item -ItemType Directory -Force -Path ".kiro\steering"
Copy-Item -Recurse "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rules" ".kiro\steering\"
Copy-Item -Recurse "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rule-details" ".kiro\"
```

Windows (CMD):

```cmd
mkdir .kiro\steering
xcopy %USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rules .kiro\steering\aws-aidlc-rules\ /E /I
xcopy %USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rule-details .kiro\aws-aidlc-rule-details\ /E /I
```

프로젝트 구조는 다음과 같아야 합니다:

```text
<project-root>/
    ├── .kiro/
    │     ├── steering/
    │     │      ├── aws-aidlc-rules/
    │     ├── aws-aidlc-rule-details/
```

룰이 로드되었는지 확인하려면:

#### Kiro IDE에서 확인

Steering 파일 패널을 열고 아래 스크린샷처럼 `Workspace` 아래에 `core-workflow` 항목이 보이는지 확인합니다.

<img src="./assets/images/kiro-ide-aidlc-rules-loaded.png?raw=true" alt="AI-DLC Rules in Kiro IDE" width="700" height="450">

AI-DLC 워크플로우를 실행할 때는 Kiro IDE를 Vibe 모드로 사용합니다. 이렇게 해야 AI-DLC 워크플로우가 Kiro에서의 개발 흐름을 이끌게 됩니다. Kiro가 가끔 spec 모드로 전환하라고 권할 수 있는데, 그런 프롬프트에는 `No`를 선택해서 Vibe 모드를 유지하세요.

<img src="./assets/images/kiro-sdd-nudge.png?raw=true" alt="Staying in Kiro Vibe mode" width="500" height="175">

#### Kiro CLI에서 확인

`kiro-cli`를 실행한 다음 `/context show`를 입력해 `.kiro/steering/aws-aidlc-rules` 항목이 있는지 확인합니다.

<img src="./assets/images/kiro-cli-aidlc-rules-loaded.png?raw=true" alt="AI-DLC Rules in Kiro CLI" width="700" height="660">

---

### Amazon Q Developer IDE 플러그인/확장

AI-DLC는 프로젝트 워크스페이스 내에서 [Amazon Q Rules](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/context-project-rules.html)를 사용합니다.

아래 명령어들은 zip을 `Downloads` 폴더에 압축 해제하여 결과 경로가 `Downloads/aidlc-rules/`가 된 상황을 전제로 합니다. 다른 위치를 사용했다면 `Downloads`를 실제 폴더 경로로 바꾸세요.

> [!NOTE]
> **Windows 사용자:** File Explorer의 **Extract All...** 대화상자를 사용했다면 zip 이름으로 된 래퍼 폴더(예: `ai-dlc-rules-v0.1.8\aidlc-rules\...`)가 기본으로 생성됩니다. 해당 옵션을 해제/수정해서 내용물이 `Downloads\aidlc-rules\`에 바로 들어가게 하거나(아래 명령과 일치), 명령어의 각 `Downloads\` 경로 앞에 `ai-dlc-rules-v<version>\`을 붙이세요 — `<version>`은 다운로드한 릴리스로 치환합니다.

macOS/Linux:

```bash
mkdir -p .amazonq/rules
cp -R ~/Downloads/aidlc-rules/aws-aidlc-rules .amazonq/rules/
cp -R ~/Downloads/aidlc-rules/aws-aidlc-rule-details .amazonq/
```

Windows (PowerShell):

```powershell
New-Item -ItemType Directory -Force -Path ".amazonq\rules"
Copy-Item -Recurse "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rules" ".amazonq\rules\"
Copy-Item -Recurse "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rule-details" ".amazonq\"
```

Windows (CMD):

```cmd
mkdir .amazonq\rules
xcopy %USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rules .amazonq\rules\aws-aidlc-rules\ /E /I
xcopy %USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rule-details .amazonq\aws-aidlc-rule-details\ /E /I
```

프로젝트 구조는 다음과 같아야 합니다:

```text
<project-root>/
    ├── .amazonq/
    │     ├── rules/
    │     │     ├── aws-aidlc-rules/
    │     ├── aws-aidlc-rule-details/
```

룰이 로드되었는지 확인하려면:

1. Amazon Q Chat 창의 오른쪽 아래 모서리에 있는 `Rules` 버튼을 클릭합니다.
2. `.amazonq/rules/aws-aidlc-rules` 항목이 보이는지 확인합니다.

<img src="./assets/images/q-ide-aidlc-rules-loaded.png?raw=true" alt="AI-DLC Rules in Q Developer IDE plugin" width="700" height="400">

---

### Cursor IDE

AI-DLC는 지능형 워크플로우를 구현하기 위해 [Cursor Rules](https://cursor.com/docs/context/rules)를 사용합니다.

아래 명령어들은 zip을 `Downloads` 폴더에 압축 해제하여 결과 경로가 `Downloads/aidlc-rules/`가 된 상황을 전제로 합니다. 다른 위치를 사용했다면 `Downloads`를 실제 폴더 경로로 바꾸세요.

> [!NOTE]
> **Windows 사용자:** File Explorer의 **Extract All...** 대화상자를 사용했다면 zip 이름으로 된 래퍼 폴더(예: `ai-dlc-rules-v0.1.8\aidlc-rules\...`)가 기본으로 생성됩니다. 해당 옵션을 해제/수정해서 내용물이 `Downloads\aidlc-rules\`에 바로 들어가게 하거나(아래 명령과 일치), 명령어의 각 `Downloads\` 경로 앞에 `ai-dlc-rules-v<version>\`을 붙이세요 — `<version>`은 다운로드한 릴리스로 치환합니다.

#### 옵션 1: 프로젝트 룰 (권장)

**Unix/Linux/macOS:**

```bash
mkdir -p .cursor/rules

cat > .cursor/rules/ai-dlc-workflow.mdc << 'EOF'
---
description: "소프트웨어 개발을 위한 AI-DLC (AI-Driven Development Life Cycle) 적응형 워크플로우"
alwaysApply: true
---

EOF
cat ~/Downloads/aidlc-rules/aws-aidlc-rules/core-workflow.md >> .cursor/rules/ai-dlc-workflow.mdc

mkdir -p .aidlc-rule-details
cp -R ~/Downloads/aidlc-rules/aws-aidlc-rule-details/* .aidlc-rule-details/
```

**Windows PowerShell:**

```powershell
New-Item -ItemType Directory -Force -Path ".cursor\rules"

$frontmatter = @"
---
description: "소프트웨어 개발을 위한 AI-DLC (AI-Driven Development Life Cycle) 적응형 워크플로우"
alwaysApply: true
---

"@
$frontmatter | Out-File -FilePath ".cursor\rules\ai-dlc-workflow.mdc" -Encoding utf8

Get-Content "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rules\core-workflow.md" | Add-Content ".cursor\rules\ai-dlc-workflow.mdc"

New-Item -ItemType Directory -Force -Path ".aidlc-rule-details"
Copy-Item "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rule-details\*" ".aidlc-rule-details\" -Recurse
```

**Windows CMD:**

```cmd
mkdir .cursor\rules

(
echo ---
echo description: "소프트웨어 개발을 위한 AI-DLC (AI-Driven Development Life Cycle) 적응형 워크플로우"
echo alwaysApply: true
echo ---
echo.
) > .cursor\rules\ai-dlc-workflow.mdc

type "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rules\core-workflow.md" >> .cursor\rules\ai-dlc-workflow.mdc

mkdir .aidlc-rule-details
xcopy "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rule-details" ".aidlc-rule-details\" /E /I
```

#### 옵션 2: AGENTS.md (간단한 대안)

**Unix/Linux/macOS:**

```bash
cp ~/Downloads/aidlc-rules/aws-aidlc-rules/core-workflow.md ./AGENTS.md
mkdir -p .aidlc-rule-details
cp -R ~/Downloads/aidlc-rules/aws-aidlc-rule-details/* .aidlc-rule-details/
```

**Windows PowerShell:**

```powershell
Copy-Item "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rules\core-workflow.md" ".\AGENTS.md"
New-Item -ItemType Directory -Force -Path ".aidlc-rule-details"
Copy-Item "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rule-details\*" ".aidlc-rule-details\" -Recurse
```

**Windows CMD:**

```cmd
copy "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rules\core-workflow.md" ".\AGENTS.md"
mkdir .aidlc-rule-details
xcopy "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rule-details" ".aidlc-rule-details\" /E /I
```

**설정 확인:**

1. **Cursor Settings → Rules, Commands** 메뉴를 엽니다.
2. **Project Rules** 아래에 `ai-dlc-workflow`가 나열되어 있어야 합니다.
3. `AGENTS.md`의 경우, 자동으로 감지되어 적용됩니다.

![AI-DLC Rules in Cursor](./assets/images/cursor-ide-aidlc-rules-loaded.png?raw=true "AI-DLC Rules in Cursor")

**디렉터리 구조 (옵션 1):**

```text
<my-project>/
├── .cursor/
│   └── rules/
│       └── ai-dlc-workflow.mdc
└── .aidlc-rule-details/
    ├── common/
    ├── inception/
    ├── construction/
    ├── extensions/
    └── operations/
```

---

### Cline

AI-DLC는 지능형 워크플로우를 구현하기 위해 Cline Rules를 사용합니다.

아래 명령어들은 zip을 `Downloads` 폴더에 압축 해제하여 결과 경로가 `Downloads/aidlc-rules/`가 된 상황을 전제로 합니다. 다른 위치를 사용했다면 `Downloads`를 실제 폴더 경로로 바꾸세요.

> [!NOTE]
> **Windows 사용자:** File Explorer의 **Extract All...** 대화상자를 사용했다면 zip 이름으로 된 래퍼 폴더(예: `ai-dlc-rules-v0.1.8\aidlc-rules\...`)가 기본으로 생성됩니다. 해당 옵션을 해제/수정해서 내용물이 `Downloads\aidlc-rules\`에 바로 들어가게 하거나(아래 명령과 일치), 명령어의 각 `Downloads\` 경로 앞에 `ai-dlc-rules-v<version>\`을 붙이세요 — `<version>`은 다운로드한 릴리스로 치환합니다.

#### 옵션 1: .clinerules 디렉터리 (권장)

**Unix/Linux/macOS:**

```bash
mkdir -p .clinerules
cp ~/Downloads/aidlc-rules/aws-aidlc-rules/core-workflow.md .clinerules/
mkdir -p .aidlc-rule-details
cp -R ~/Downloads/aidlc-rules/aws-aidlc-rule-details/* .aidlc-rule-details/
```

**Windows PowerShell:**

```powershell
New-Item -ItemType Directory -Force -Path ".clinerules"
Copy-Item "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rules\core-workflow.md" ".clinerules\"
New-Item -ItemType Directory -Force -Path ".aidlc-rule-details"
Copy-Item "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rule-details\*" ".aidlc-rule-details\" -Recurse
```

**Windows CMD:**

```cmd
mkdir .clinerules
copy "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rules\core-workflow.md" ".clinerules\"
mkdir .aidlc-rule-details
xcopy "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rule-details" ".aidlc-rule-details\" /E /I
```

#### 옵션 2: AGENTS.md (대안)

**Unix/Linux/macOS:**

```bash
cp ~/Downloads/aidlc-rules/aws-aidlc-rules/core-workflow.md ./AGENTS.md
mkdir -p .aidlc-rule-details
cp -R ~/Downloads/aidlc-rules/aws-aidlc-rule-details/* .aidlc-rule-details/
```

**Windows PowerShell:**

```powershell
Copy-Item "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rules\core-workflow.md" ".\AGENTS.md"
New-Item -ItemType Directory -Force -Path ".aidlc-rule-details"
Copy-Item "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rule-details\*" ".aidlc-rule-details\" -Recurse
```

**Windows CMD:**

```cmd
copy "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rules\core-workflow.md" ".\AGENTS.md"
mkdir .aidlc-rule-details
xcopy "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rule-details" ".aidlc-rule-details\" /E /I
```

**설정 확인:**

1. Cline의 채팅 인터페이스에서 채팅 입력란 아래에 있는 Rules 팝오버를 찾습니다.
2. `core-workflow.md`가 나열되어 있고 활성 상태인지 확인합니다.
3. 룰 파일은 필요에 따라 켜고 끌 수 있습니다.

![AI-DLC Rules in Cline](./assets/images/cline-ide-aidlc-rules-loaded.png?raw=true "AI-DLC Rules in Cline")

**디렉터리 구조 (옵션 1):**

```text
<my-project>/
├── .clinerules/
│   └── core-workflow.md
└── .aidlc-rule-details/
    ├── common/
    ├── inception/
    ├── construction/
    ├── extensions/
    └── operations/
```

---

### Claude Code

AI-DLC는 지능형 워크플로우를 구현하기 위해 Claude Code의 프로젝트 메모리 파일(`CLAUDE.md`)을 사용합니다.

아래 명령어들은 zip을 `Downloads` 폴더에 압축 해제하여 결과 경로가 `Downloads/aidlc-rules/`가 된 상황을 전제로 합니다. 다른 위치를 사용했다면 `Downloads`를 실제 폴더 경로로 바꾸세요.

> [!NOTE]
> **Windows 사용자:** File Explorer의 **Extract All...** 대화상자를 사용했다면 zip 이름으로 된 래퍼 폴더(예: `ai-dlc-rules-v0.1.8\aidlc-rules\...`)가 기본으로 생성됩니다. 해당 옵션을 해제/수정해서 내용물이 `Downloads\aidlc-rules\`에 바로 들어가게 하거나(아래 명령과 일치), 명령어의 각 `Downloads\` 경로 앞에 `ai-dlc-rules-v<version>\`을 붙이세요 — `<version>`은 다운로드한 릴리스로 치환합니다.

#### 옵션 1: 프로젝트 루트 (권장)

**Unix/Linux/macOS:**

```bash
cp ~/Downloads/aidlc-rules/aws-aidlc-rules/core-workflow.md ./CLAUDE.md
mkdir -p .aidlc-rule-details
cp -R ~/Downloads/aidlc-rules/aws-aidlc-rule-details/* .aidlc-rule-details/
```

**Windows PowerShell:**

```powershell
Copy-Item "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rules\core-workflow.md" ".\CLAUDE.md"
New-Item -ItemType Directory -Force -Path ".aidlc-rule-details"
Copy-Item "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rule-details\*" ".aidlc-rule-details\" -Recurse
```

**Windows CMD:**

```cmd
copy "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rules\core-workflow.md" ".\CLAUDE.md"
mkdir .aidlc-rule-details
xcopy "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rule-details" ".aidlc-rule-details\" /E /I
```

#### 옵션 2: .claude 디렉터리

**Unix/Linux/macOS:**

```bash
mkdir -p .claude
cp ~/Downloads/aidlc-rules/aws-aidlc-rules/core-workflow.md .claude/CLAUDE.md
mkdir -p .aidlc-rule-details
cp -R ~/Downloads/aidlc-rules/aws-aidlc-rule-details/* .aidlc-rule-details/
```

**Windows PowerShell:**

```powershell
New-Item -ItemType Directory -Force -Path ".claude"
Copy-Item "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rules\core-workflow.md" ".claude\CLAUDE.md"
New-Item -ItemType Directory -Force -Path ".aidlc-rule-details"
Copy-Item "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rule-details\*" ".aidlc-rule-details\" -Recurse
```

**Windows CMD:**

```cmd
mkdir .claude
copy "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rules\core-workflow.md" ".claude\CLAUDE.md"
mkdir .aidlc-rule-details
xcopy "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rule-details" ".aidlc-rule-details\" /E /I
```

**설정 확인:**

1. 프로젝트 디렉터리에서 Claude Code를 실행합니다 (CLI: `claude` 또는 VS Code 확장).
2. `/config` 명령어를 사용해 현재 설정을 확인합니다.
3. Claude에게 "이 프로젝트에서 현재 활성화된 인스트럭션이 무엇인가요?"라고 물어봅니다.

**디렉터리 구조 (옵션 1):**

```text
<my-project>/
├── CLAUDE.md
└── .aidlc-rule-details/
    ├── common/
    ├── inception/
    ├── construction/
    ├── extensions/
    └── operations/
```

---

### GitHub Copilot

AI-DLC는 지능형 워크플로우를 구현하기 위해 [GitHub Copilot custom instructions](https://code.visualstudio.com/docs/copilot/customization/custom-instructions)를 사용합니다. `.github/copilot-instructions.md` 파일은 자동으로 감지되어 워크스페이스 내의 모든 채팅 요청에 적용됩니다.

아래 명령어들은 zip을 `Downloads` 폴더에 압축 해제하여 결과 경로가 `Downloads/aidlc-rules/`가 된 상황을 전제로 합니다. 다른 위치를 사용했다면 `Downloads`를 실제 폴더 경로로 바꾸세요.

> [!NOTE]
> **Windows 사용자:** File Explorer의 **Extract All...** 대화상자를 사용했다면 zip 이름으로 된 래퍼 폴더(예: `ai-dlc-rules-v0.1.8\aidlc-rules\...`)가 기본으로 생성됩니다. 해당 옵션을 해제/수정해서 내용물이 `Downloads\aidlc-rules\`에 바로 들어가게 하거나(아래 명령과 일치), 명령어의 각 `Downloads\` 경로 앞에 `ai-dlc-rules-v<version>\`을 붙이세요 — `<version>`은 다운로드한 릴리스로 치환합니다.

**Unix/Linux/macOS:**

```bash
mkdir -p .github
cp ~/Downloads/aidlc-rules/aws-aidlc-rules/core-workflow.md .github/copilot-instructions.md
mkdir -p .aidlc-rule-details
cp -R ~/Downloads/aidlc-rules/aws-aidlc-rule-details/* .aidlc-rule-details/
```

**Windows PowerShell:**

```powershell
New-Item -ItemType Directory -Force -Path ".github"
Copy-Item "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rules\core-workflow.md" ".github\copilot-instructions.md"
New-Item -ItemType Directory -Force -Path ".aidlc-rule-details"
Copy-Item "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rule-details\*" ".aidlc-rule-details\" -Recurse
```

**Windows CMD:**

```cmd
mkdir .github
copy "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rules\core-workflow.md" ".github\copilot-instructions.md"
mkdir .aidlc-rule-details
xcopy "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rule-details" ".aidlc-rule-details\" /E /I
```

**설정 확인:**

1. VS Code에서 프로젝트 폴더를 엽니다.
2. Copilot Chat 패널을 엽니다 (Cmd/Ctrl+Shift+I).
3. **Configure Chat** (톱니바퀴 아이콘) > **Chat Instructions**를 선택해서 `copilot-instructions`가 나열되어 있는지 확인합니다.
4. 또는 채팅 입력란에 `/instructions`를 입력해서 활성 인스트럭션을 확인할 수 있습니다.

**디렉터리 구조:**

```text
<my-project>/
├── .github/
│   └── copilot-instructions.md
└── .aidlc-rule-details/
    ├── common/
    ├── inception/
    ├── construction/
    ├── extensions/
    └── operations/
```

---

### OpenAI Codex

AI-DLC는 지원하는 코딩 에이전트로 OpenAI Codex를 지원하며, 지능형 워크플로우를 전달하기 위해 [Codex AGENTS.md](https://developers.openai.com/codex/guides/agents-md) 규약을 사용합니다. Codex는 세션을 시작할 때 프로젝트 루트의 `AGENTS.md`를 자동으로 발견하고 로드합니다.

아래 명령어들은 zip을 `Downloads` 폴더에 압축 해제하여 결과 경로가 `Downloads/aidlc-rules/`가 된 상황을 전제로 합니다. 다른 위치를 사용했다면 `Downloads`를 실제 폴더 경로로 바꾸세요.

> [!NOTE]
> **Windows 사용자:** File Explorer의 **Extract All...** 대화상자를 사용했다면 zip 이름으로 된 래퍼 폴더(예: `ai-dlc-rules-v0.1.8\aidlc-rules\...`)가 기본으로 생성됩니다. 해당 옵션을 해제/수정해서 내용물이 `Downloads\aidlc-rules\`에 바로 들어가게 하거나(아래 명령과 일치), 명령어의 각 `Downloads\` 경로 앞에 `ai-dlc-rules-v<version>\`을 붙이세요 — `<version>`은 다운로드한 릴리스로 치환합니다.

**Unix/Linux/macOS:**

```bash
cp ~/Downloads/aidlc-rules/aws-aidlc-rules/core-workflow.md ./AGENTS.md
mkdir -p .aidlc-rule-details
cp -R ~/Downloads/aidlc-rules/aws-aidlc-rule-details/* .aidlc-rule-details/
```

**Windows PowerShell:**

```powershell
Copy-Item "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rules\core-workflow.md" ".\AGENTS.md"
New-Item -ItemType Directory -Force -Path ".aidlc-rule-details"
Copy-Item "$env:USERPROFILE\Downloads\aidlc-rules\aws-aidlc-rule-details\*" ".aidlc-rule-details\" -Recurse
```

**Windows CMD:**

```cmd
copy "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rules\core-workflow.md" ".\AGENTS.md"
mkdir .aidlc-rule-details
xcopy "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rule-details" ".aidlc-rule-details\" /E /I
```

**설정 확인:**

1. 프로젝트 디렉터리에서 Codex 세션을 시작합니다.
2. Codex에게 다음과 같이 물어봅니다. 기존 프로젝트라면 "Using AIDLC analyze the project?", 새 프로젝트라면 "Using Aidlc what workflow do you see".
3. Codex가 AI-DLC의 3단계 워크플로우(Inception → Construction → Operations)를 설명해야 합니다.

> [!NOTE]
> `AGENTS.md` 파일은 기본 설정에서 Codex의 인스트럭션 예산(instruction budget) 안에 들어가도록 설계되어 있습니다. 프로젝트 고유 내용을 많이 추가해서 Codex가 프로젝트 문서가 인스트럭션 한도를 초과했다고 보고한다면, Codex 설정에서 한도를 높일 수 있습니다(예: `config.toml`의 `project_doc_max_bytes` 조정):
>
> ```toml
> project_doc_max_bytes = 65536  # 예시 값; 프로젝트에 적절한 한도를 선택하세요
> ```

**디렉터리 구조:**

```text
<my-project>/
├── AGENTS.md
└── .aidlc-rule-details/
    ├── common/
    ├── inception/
    ├── construction/
    ├── extensions/
    └── operations/
```

---

### 그 외 에이전트

AI-DLC는 프로젝트 단위의 룰이나 steering 파일을 지원하는 모든 코딩 에이전트와 함께 동작합니다. 일반적인 접근 방식:

1. `aws-aidlc-rules/`를 사용하는 에이전트가 프로젝트 룰을 읽어오는 위치에 배치합니다(에이전트 공식 문서 참고).
2. `aws-aidlc-rule-details/`는 룰이 참조할 수 있도록 형제 레벨(sibling level)에 배치합니다.

에이전트에 룰 파일 규약이 없다면, 두 폴더 모두를 프로젝트 루트에 두고 `aws-aidlc-rules/`를 룰 디렉터리로 지정하도록 에이전트에 알려주면 됩니다.

---

## 사용법

1. 채팅에서 **"Using AI-DLC, ..."**라는 구문으로 의도를 표현하면서 소프트웨어 개발 프로젝트를 시작합니다.
2. AI-DLC 워크플로우가 자동으로 활성화되어 이후 과정을 안내합니다.
3. AI-DLC가 던지는 구조화된 질문에 답합니다.
4. AI가 생성하는 모든 계획을 꼼꼼히 검토하세요. 사용자가 직접 감독하고 검증해야 합니다.
5. 실행 계획을 검토해 어떤 스테이지가 실행될지 확인합니다.
6. 각 스테이지의 산출물을 신중히 검토하고 승인해서 통제권을 유지하세요.
7. 모든 산출물은 `aidlc-docs/` 디렉터리에 생성됩니다.

---

## 3단계 적응형 워크플로우

AI-DLC는 프로젝트의 복잡도에 적응하는 구조화된 3단계 접근 방식을 따릅니다:

### 🔵 INCEPTION PHASE (인셉션 단계)

**무엇(WHAT)** 을 만들지, **왜(WHY)** 만드는지를 결정합니다.

- 요구사항 분석 및 검증
- 사용자 스토리(user story) 작성 (해당되는 경우)
- 애플리케이션 설계와 병렬 개발을 위한 작업 단위(units of work) 생성
- 리스크 평가 및 복잡도 분석

### 🟢 CONSTRUCTION PHASE (컨스트럭션 단계)

**어떻게(HOW)** 만들지를 결정합니다.

- 상세 컴포넌트 설계
- 코드 생성 및 구현
- 빌드 설정 및 테스트 전략
- 품질 보증 및 검증

### 🟡 OPERATIONS PHASE (오퍼레이션 단계)

배포와 모니터링 (예정)

- 배포 자동화 및 인프라
- 모니터링과 옵저버빌리티(observability) 셋업
- 프로덕션 준비 상태(production readiness) 검증

---

## 주요 기능

| 기능                          | 설명                                                                                                                   |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| **적응형 지능 (Adaptive Intelligence)** | 구체적인 요청에 가치를 더하는 스테이지만 실행                                                                          |
| **컨텍스트 인식 (Context-Aware)**      | 기존 코드베이스와 복잡도 요구사항을 분석                                                                              |
| **리스크 기반 (Risk-Based)**           | 복잡한 변경은 종합적으로 다루고, 단순한 변경은 효율적으로 처리                                                        |
| **질문 기반 (Question-Driven)**        | 채팅이 아닌 파일에 작성된 구조화된 객관식 질문 사용                                                                   |
| **항상 통제권 유지 (Always in Control)** | 실행 계획을 검토하고 각 단계를 승인                                                                                   |
| **확장 가능 (Extensible)**             | 보안, 컴플라이언스, 조직 고유 룰 등 커스텀 룰을 핵심 워크플로우 위에 레이어로 얹을 수 있음                            |

---

## 익스텐션

AI-DLC는 핵심 워크플로우 위에 추가 룰을 레이어로 얹을 수 있게 해주는 익스텐션(extension) 시스템을 지원합니다. 익스텐션은 `aws-aidlc-rule-details/extensions/` 아래에 카테고리(예: `security/`, `testing/`)별로 묶인 마크다운 파일들로 구성됩니다.

### 익스텐션 동작 방식

각 익스텐션은 같은 디렉터리에 놓이는 두 개의 파일로 구성됩니다:

- **rules 파일** (예: `security-baseline.md`): 익스텐션의 룰을 담음.
- **opt-in 파일** (예: `security-baseline.opt-in.md`): 요구사항 분석(Requirements Analysis) 중 사용자에게 제시되는 구조화된 객관식 질문을 담음.

워크플로우 시작 시, AI-DLC는 `extensions/` 디렉터리를 스캔해서 `*.opt-in.md` 파일만 로드합니다. 요구사항 분석 단계에서 각 opt-in 프롬프트를 사용자에게 제시합니다. 사용자가 옵트인하면, 명명 규약에 따라(`.opt-in.md`를 제거하고 `.md`를 붙임) 해당하는 rules 파일이 로드됩니다. 사용자가 옵트아웃하면 rules 파일은 절대 로드되지 않습니다. `*.opt-in.md`가 없는 익스텐션은 항상 적용됩니다.

활성화된 익스텐션 룰은 차단형(blocking) 제약입니다 — 각 스테이지에서 모델은 다음 단계로 진행하기 전에 준수 여부를 검증합니다.

### 기본 제공 익스텐션

`extensions/` 디렉터리에는 다음이 기본 포함되어 있습니다 (새 익스텐션은 시간이 지나면서 추가될 수 있음):

```text
aws-aidlc-rule-details/
└── extensions/
    ├── security/                      # 익스텐션 카테고리
    │   └── baseline/
    │       ├── security-baseline.md          # 기본 보안 룰
    │       └── security-baseline.opt-in.md   # opt-in 프롬프트
    └── testing/                       # 익스텐션 카테고리
        └── property-based/
            ├── property-based-testing.md          # 속성 기반 테스트 룰
            └── property-based-testing.opt-in.md   # opt-in 프롬프트
```

> [!IMPORTANT]
> 보안 익스텐션 룰은 AI-DLC 워크플로우 안에서 효과적인 보안 룰을 구축하기 위한 방향성 레퍼런스로 제공됩니다. 각 조직은 프로덕션 워크플로우에 배포하기 전에 자체적으로 보안 룰을 구축, 커스터마이즈, 철저히 테스트해야 합니다.

### 직접 익스텐션 추가하기

기존 카테고리를 확장할 수도 있고, 완전히 새로운 카테고리를 만들 수도 있습니다.

1. `extensions/` 아래에 디렉터리를 만듭니다(예: `security/compliance/` 또는 `performance/baseline/`).
2. **rules 파일**을 추가합니다(예: `compliance.md`). `security-baseline.md`와 동일한 구조를 따릅니다:
   - 각 룰을 `## Rule <PREFIX-NN>: <Title>` 형식의 헤딩으로 정의합니다. prefix는 짧은 카테고리 식별자이고, NN은 순차 번호입니다(예: `COMPLIANCE-01`, `COMPLIANCE-02`). 이 ID들은 감사 로그와 컴플라이언스 요약에 참조되므로, 로드된 모든 익스텐션 전반에서 고유해야 합니다.
   - 요구사항을 설명하는 **Rule** 섹션을 포함합니다.
   - 모델이 평가해야 할 구체적 검사 항목을 담은 **Verification** 섹션을 포함합니다.
3. 명명 규약 `<name>.opt-in.md`에 맞춰 **opt-in 파일**을 추가합니다(예: `compliance.opt-in.md`). 기대 포맷은 `security-baseline.opt-in.md`를 참고하세요. 이 파일을 두지 않으면 익스텐션은 사용자 옵트아웃 없이 항상 적용됩니다.
4. 룰은 기본적으로 차단형입니다 — 검증 기준을 만족하지 못하면 해당 발견사항이 해결될 때까지 스테이지가 진행되지 않습니다.

---

## 부속 도구

`scripts/` 디렉터리에는 AI-DLC 워크플로우를 강화해주는 부속 도구들이 들어 있습니다:

### AIDLC Evaluator

**위치:** [`scripts/aidlc-evaluator/`](scripts/aidlc-evaluator/)

AI-DLC 워크플로우 변경을 검증하기 위한 자동화된 테스팅·리포팅 프레임워크입니다. Evaluator는 다음을 제공합니다:

- **Golden Test Cases** — 검증을 위한 큐레이션된 베이스라인 테스트 케이스
- **Execution Framework** — 평가 파이프라인을 통해 테스트 케이스를 실행하는 오케스트레이션
- **Semantic Evaluation** — 출력의 정확성과 완전성에 대한 AI 기반 평가
- **Code Evaluation** — 정적 분석 (린팅, 보안 스캐닝, 중복 탐지)
- **NFR Evaluation** — 비기능 요구사항(non-functional requirements) 테스트 (토큰 사용량, 실행 시간, 모델 간 일관성)
- **CI/CD Integration** — PR 검증을 위한 자동화 파이프라인

**빠른 시작:**

```bash
cd scripts/aidlc-evaluator
uv sync
uv run python run.py test
```

**문서:** [scripts/aidlc-evaluator/README.md](scripts/aidlc-evaluator/README.md) 참고

---

### AIDLC Design Reviewer

**위치:** [`scripts/aidlc-designreview/`](scripts/aidlc-designreview/)

⚠️ **실험적 기능** — AWS Bedrock의 Claude 모델을 사용해 AIDLC 설계 산출물을 분석하는 AI 기반 설계 리뷰 도구.

**기능:**

- **Multi-Agent Review** — 세 개의 전문 AI 에이전트 (Critique, Alternatives, Gap Analysis)
- **품질 점수 (Quality Scoring)** — 가중 심각도 분석과 실행 가능한 권고
- **두 가지 배포 모드:**
  - **CLI Tool** — CI/CD 파이프라인을 위한 온디맨드 리뷰
  - **Claude Code Hook** — 개발 중 실시간 리뷰 (실험적)

**설치 (CLI Tool):**

```bash
cd scripts/aidlc-designreview
uv sync --extra test
source .venv/bin/activate  # Linux/Mac
design-reviewer --aidlc-docs /path/to/aidlc-docs
```

**설치 (Claude Code Hook):**

```bash
# From workspace root
./scripts/aidlc-designreview/tool-install/install-linux.sh      # Linux
./scripts/aidlc-designreview/tool-install/install-mac.sh        # macOS
.\scripts\aidlc-designreview\tool-install\install-windows.ps1   # Windows PowerShell
```

설치 스크립트가 워크스페이스 루트를 자동으로 감지해서 `.claude/`에 훅을 설치합니다.

**문서:**

- [scripts/aidlc-designreview/README.md](scripts/aidlc-designreview/README.md) — 메인 문서
- [scripts/aidlc-designreview/INSTALLATION.md](scripts/aidlc-designreview/INSTALLATION.md) — 훅 설치 가이드

---

## 원칙(Tenets)

의사결정을 이끄는 우리의 핵심 원칙입니다.

- **중복 없음 (No duplication)**. 진실의 원천(source of truth)은 한 곳에만 둡니다. 새로운 도구나 포맷을 지원하기 위해 특정 파일이 필요하다면, 별도 복사본을 유지하는 대신 원본에서 생성합니다.

- **방법론 우선 (Methodology first)**. AI-DLC는 본질적으로 도구가 아니라 방법론입니다. 사용자가 시작하기 위해 무언가 설치할 필요가 없어야 합니다. 그렇긴 해도, 사용자가 방법론을 도입하거나 확장하는 데 도움이 된다면 편의 도구(스크립트, CLI)는 향후 열어 두고 있습니다.

- **재현 가능 (Reproducible)**. 룰은 서로 다른 모델이 비슷한 결과를 내놓을 만큼 명확해야 합니다. 모델마다 동작이 다른 것은 알지만, 방법론은 명시적인 안내를 통해 그 편차를 최소화해야 합니다.

- **불가지론적 (Agnostic)**. 방법론은 어떤 IDE, 에이전트, 모델과도 동작해야 합니다. 특정 도구나 벤더에 묶이지 않습니다.

- **사람이 루프 안에 (Human in the loop)**. 중요한 의사결정에는 사용자의 명시적 확인이 필요합니다. 에이전트는 제안하고, 사람은 승인합니다.

---

## 사전 준비물

AI 보조 코딩을 위해 지원되는 플랫폼/도구 중 하나가 설치되어 있어야 합니다:

| 플랫폼                          | 설치 링크                                                                                                                                                       |
| ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Kiro                          | [Install](https://kiro.dev/)                                                                                                                                    |
| Kiro CLI                      | [Install](https://kiro.dev/cli/)                                                                                                                                |
| Amazon Q Developer IDE Plugin | [Install](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/q-in-IDE.html)                                                                               |
| Cursor IDE                    | [Install](https://cursor.com/)                                                                                                                                  |
| Cline VS Code Extension       | [Install](https://marketplace.visualstudio.com/items?itemName=saoudrizwan.claude-dev)                                                                           |
| Claude Code CLI               | [Install](https://github.com/anthropics/claude-code)                                                                                                            |
| GitHub Copilot                | [Install](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot) + [Chat](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot-chat) |

---

## 트러블슈팅

### 일반적인 문제

| 문제                         | 해결 방법                                                              |
| ---------------------------- | ---------------------------------------------------------------------- |
| 룰이 로드되지 않음            | 사용하는 플랫폼에 맞는 경로에 파일이 존재하는지 확인                   |
| 파일 인코딩 문제              | 파일이 UTF-8로 인코딩되어 있는지 확인                                  |
| 세션에 룰이 적용되지 않음     | 파일 변경 후 새 채팅 세션을 시작                                       |
| 룰 디테일이 로드되지 않음     | `.aidlc-rule-details/`와 그 하위 디렉터리들이 존재하는지 확인          |

### 플랫폼별 문제

#### Amazon Q Developer / Kiro

- `/context show`로 룰이 로드되었는지 확인합니다.
- `.amazonq/rules/` 또는 `.kiro/steering/` 디렉터리 구조를 점검합니다.

#### Cursor

- "Apply Intelligently"를 쓰려면, 프론트매터에 description이 정의되어 있어야 합니다.
- **Cursor Settings → Rules**에서 룰이 활성화되어 있는지 확인합니다.
- 룰이 너무 큰 경우(>500 라인) 여러 개의 집중된 룰로 분할합니다.

#### Cline

- 채팅 입력란 아래에 있는 Rules 팝오버를 확인합니다.
- 팝오버 UI에서 필요에 따라 룰 파일을 켜고 끕니다.

#### Claude Code

- `/config` 명령어로 현재 설정을 확인합니다.
- "이 프로젝트에서 현재 활성화된 인스트럭션이 무엇인가요?"라고 물어봅니다.

#### GitHub Copilot

- **Configure Chat** (톱니바퀴 아이콘) > **Chat Instructions**를 선택해서 인스트럭션이 로드되었는지 확인합니다.
- 채팅 입력란에 `/instructions`를 입력해서 활성 인스트럭션 파일을 확인합니다.
- 워크스페이스 루트에 `.github/copilot-instructions.md`가 존재하는지 확인합니다.

### Windows에서의 파일 경로 문제

- 마크다운 파일 내 경로에는 슬래시 `/`를 사용합니다.
- 백슬래시를 쓴 Windows 경로는 제대로 동작하지 않을 수 있습니다.

---

## 버전 관리 권장사항

**저장소에 커밋하기:**

```gitignore
# 아래 파일들은 버전 관리되어야 합니다
CLAUDE.md
AGENTS.md
.amazonq/rules/
.amazonq/aws-aidlc-rule-details/
.kiro/steering/
.kiro/aws-aidlc-rule-details/
.cursor/rules/
.clinerules/
.github/copilot-instructions.md
.aidlc-rule-details/
```

**선택사항 - 필요 시 `.gitignore`에 추가:**

```gitignore
# 로컬 전용 설정
.claude/settings.local.json
```

---

## 생성되는 aidlc-docs/ 레퍼런스

AI-DLC 워크플로우가 생성하는 모든 문서 산출물의 전체 레퍼런스는 [docs/GENERATED_DOCS_REFERENCE.md](docs/GENERATED_DOCS_REFERENCE.md)를 참고하세요.

---

## 실험적 기능: AI 보조 설정 (릴리스 다운로드)

| 리소스                                              | 링크                                                                                                                          |
| --------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| AI-DLC Method Definition Paper                      | [Paper](https://prod.d13rzhkk8cj2z0.amplifyapp.com/)                                                                          |
| AI-DLC Methodology Blog                             | [AWS Blog](https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/)                                             |
| AI-DLC Open-source Launch Blog                      | [AWS Blog](https://aws.amazon.com/blogs/devops/open-sourcing-adaptive-workflows-for-ai-driven-development-life-cycle-ai-dlc/) |
| AI-DLC Example Walkthrough Blog                     | [AWS Blog](https://aws.amazon.com/blogs/devops/building-with-ai-dlc-using-amazon-q-developer/)                                |
| Amazon Q Developer Documentation                    | [Docs](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/q-in-IDE.html)                                                |
| Kiro CLI Documentation                              | [Docs](https://kiro.dev/docs/cli/steering/)                                                                                   |
| Cursor Rules Documentation                          | [Docs](https://cursor.com/docs/context/rules)                                                                                 |
| Claude Code Documentation                           | [GitHub](https://github.com/anthropics/claude-code)                                                                           |
| GitHub Copilot Documentation                        | [Docs](https://docs.github.com/en/copilot)                                                                                    |
| AI-DLC 활용하기 (상호작용 패턴과 팁)                  | [docs/WORKING-WITH-AIDLC.md](docs/WORKING-WITH-AIDLC.md)                                                                      |
| 기여 가이드라인                                      | [CONTRIBUTING.md](CONTRIBUTING.md)                                                                                            |
| 행동 강령                                            | [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md)                                                                                      |

---

## 추가 자료

<!-- TODO: Replace this Amplify URL with a permanent/stable URL when available -->
| 리소스                                              | 링크                                                                                                                          |
| --------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| AI-DLC Method Definition Paper                      | [Paper](https://prod.d13rzhkk8cj2z0.amplifyapp.com/)                                                                          |
| AI-DLC Methodology Blog                             | [AWS Blog](https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/)                                             |
| AI-DLC Open-source Launch Blog                      | [AWS Blog](https://aws.amazon.com/blogs/devops/open-sourcing-adaptive-workflows-for-ai-driven-development-life-cycle-ai-dlc/) |
| AI-DLC Example Walkthrough Blog                     | [AWS Blog](https://aws.amazon.com/blogs/devops/building-with-ai-dlc-using-amazon-q-developer/)                                |
| Amazon Q Developer Documentation                    | [Docs](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/q-in-IDE.html)                                                |
| Kiro CLI Documentation                              | [Docs](https://kiro.dev/docs/cli/steering/)                                                                                   |
| Cursor Rules Documentation                          | [Docs](https://cursor.com/docs/context/rules)                                                                                 |
| Claude Code Documentation                           | [GitHub](https://github.com/anthropics/claude-code)                                                                           |
| GitHub Copilot Documentation                        | [Docs](https://docs.github.com/en/copilot)                                                                                    |
| AI-DLC 활용하기 (상호작용 패턴과 팁)                  | [docs/WORKING-WITH-AIDLC.md](docs/WORKING-WITH-AIDLC.md)                                                                      |
| 기여 가이드라인                                      | [CONTRIBUTING.md](CONTRIBUTING.md)                                                                                            |
| 행동 강령                                            | [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md)                                                                                      |

---

## 기여하기

자세한 내용은 [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications)을 참고하세요.

## 라이선스

이 라이브러리는 MIT-0 라이선스 하에 배포됩니다. [LICENSE](LICENSE) 파일을 참고하세요.
