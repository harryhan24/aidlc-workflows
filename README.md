# AI-DLC (AI-Driven Development Life Cycle)

> [!IMPORTANT]
> 생성형 AI는 실수할 수 있습니다. 선택한 AI 모델과 에이전트형 코딩 어시스턴트가 생성한 모든 출력과 비용을 검토하는 것을 권장합니다. 자세한 내용은 [AWS Responsible AI Policy](https://aws.amazon.com/ai/responsible-ai/policy/)를 참조하세요.

<!-- TODO: 안정적인 영구 URL이 준비되면 이 Amplify URL을 교체하세요 -->
AI-DLC는 사용자의 필요에 맞게 적응하고, 품질 기준을 유지하며, 전체 프로세스에 대한 제어권을 사용자에게 유지시켜 주는 지능형 소프트웨어 개발 워크플로입니다. AI-DLC 방법론에 대해 더 알아보려면 이 [블로그](https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/)와 블로그에서 언급한 [Method Definition Paper](https://prod.d13rzhkk8cj2z0.amplifyapp.com/)를 읽어보세요.

## 목차

- [공통](#공통)
- [플랫폼별 설정](#플랫폼별-설정)
- [사용법](#사용법)
- [3단계 적응형 워크플로](#3단계-적응형-워크플로)
- [주요 기능](#주요-기능)
- [확장 기능](#확장-기능)
- [지원 도구](#지원-도구)
- [원칙](#원칙)
- [사전 요구 사항](#사전-요구-사항)
- [문제 해결](#문제-해결)
- [버전 관리 권장 사항](#버전-관리-권장-사항)
- [추가 리소스](#추가-리소스)
- [생성되는 aidlc-docs/ 참조](#생성되는-aidlc-docs-참조)
- [실험: AI 지원 설정(릴리스 다운로드)](#실험-ai-지원-설정릴리스-다운로드)
- [기여](#기여)
- [라이선스](#라이선스)

---

## 공통

1. [Releases page](../../releases/latest)에서 최신 릴리스 zip 파일(`ai-dlc-rules-v<release-number>.zip`)을 프로젝트 디렉터리 **외부** 폴더(예: `~/Downloads`)로 다운로드합니다.
2. zip 파일을 압축 해제합니다. 압축 파일에는 두 개의 하위 디렉터리를 가진 `aidlc-rules/` 폴더가 들어 있습니다.
   - `aws-aidlc-rules/` — 핵심 AI-DLC 워크플로 규칙
   - `aws-aidlc-rule-details/` — 핵심 규칙에서 조건부로 참조하는 상세 규칙
3. 아래에서 사용하는 코딩 에이전트와 플랫폼에 맞는 설정 안내를 따릅니다.

---

## 플랫폼별 설정

- [Kiro](#kiro)
- [Amazon Q Developer IDE Plugin](#amazon-q-developer-ide-pluginextension)
- [Cursor IDE](#cursor-ide)
- [Cline](#cline)
- [Claude Code](#claude-code)
- [GitHub Copilot](#github-copilot)

---

### Kiro

AI-DLC는 프로젝트 워크스페이스 안에서 [Kiro Steering Files](https://kiro.dev/docs/cli/steering/)를 사용합니다.

아래 명령은 zip 파일을 `Downloads` 폴더에 압축 해제해 결과 경로가 `Downloads/aidlc-rules/`라고 가정합니다. 다른 위치를 사용했다면 `Downloads`를 실제 폴더 경로로 바꾸세요.

> [!NOTE]
> **Windows 사용자:** File Explorer의 **Extract All...** 대화상자를 사용했다면 기본적으로 zip 파일명과 같은 래퍼 폴더(예: `ai-dlc-rules-v0.1.8\aidlc-rules\...`)가 생성됩니다. 아래 명령과 맞게 내용이 `Downloads\aidlc-rules\`에 바로 풀리도록 대상을 해제/수정하거나, 각 명령의 `Downloads\` 경로 앞에 `ai-dlc-rules-v<version>\`을 붙이세요. 여기서 `<version>`은 다운로드한 릴리스 버전으로 바꿉니다.

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

프로젝트 구조는 다음과 같아야 합니다.

```text
<project-root>/
    ├── .kiro/
    │     ├── steering/
    │     │      ├── aws-aidlc-rules/
    │     ├── aws-aidlc-rule-details/
```

규칙이 로드되었는지 확인하려면 다음을 수행합니다.

#### Kiro IDE에서 확인

steering files 패널을 열고, 아래 스크린샷처럼 `Workspace` 아래에 `core-workflow` 항목이 보이는지 확인합니다.

<img src="./assets/images/kiro-ide-aidlc-rules-loaded.png?raw=true" alt="Kiro IDE의 AI-DLC 규칙" width="700" height="450">

Kiro IDE에서는 Vibe mode에서 AI-DLC 워크플로를 실행합니다. 이렇게 하면 AI-DLC 워크플로가 Kiro의 개발 흐름을 안내합니다. 때때로 Kiro가 spec mode로 전환하라고 제안할 수 있습니다. Vibe mode를 유지하려면 해당 프롬프트에서 `No`를 선택하세요.

<img src="./assets/images/kiro-sdd-nudge.png?raw=true" alt="Kiro Vibe mode 유지" width="500" height="175">

#### Kiro CLI에서 확인

`kiro-cli`를 실행한 다음 `/context show`를 입력하고, `.kiro/steering/aws-aidlc-rules` 항목이 있는지 확인합니다.

<img src="./assets/images/kiro-cli-aidlc-rules-loaded.png?raw=true" alt="Kiro CLI의 AI-DLC 규칙" width="700" height="660">

---

### Amazon Q Developer IDE Plugin/Extension

AI-DLC는 프로젝트 워크스페이스 안에서 [Amazon Q Rules](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/context-project-rules.html)를 사용합니다.

아래 명령은 zip 파일을 `Downloads` 폴더에 압축 해제해 결과 경로가 `Downloads/aidlc-rules/`라고 가정합니다. 다른 위치를 사용했다면 `Downloads`를 실제 폴더 경로로 바꾸세요.

> [!NOTE]
> **Windows 사용자:** File Explorer의 **Extract All...** 대화상자를 사용했다면 기본적으로 zip 파일명과 같은 래퍼 폴더(예: `ai-dlc-rules-v0.1.8\aidlc-rules\...`)가 생성됩니다. 아래 명령과 맞게 내용이 `Downloads\aidlc-rules\`에 바로 풀리도록 대상을 해제/수정하거나, 각 명령의 `Downloads\` 경로 앞에 `ai-dlc-rules-v<version>\`을 붙이세요. 여기서 `<version>`은 다운로드한 릴리스 버전으로 바꿉니다.

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

프로젝트 구조는 다음과 같아야 합니다.

```text
<project-root>/
    ├── .amazonq/
    │     ├── rules/
    │     │     ├── aws-aidlc-rules/
    │     ├── aws-aidlc-rule-details/
```

규칙이 로드되었는지 확인하려면 다음을 수행합니다.

1. Amazon Q Chat 창의 오른쪽 아래에 있는 `Rules` 버튼을 클릭합니다.
2. `.amazonq/rules/aws-aidlc-rules` 항목이 보이는지 확인합니다.

<img src="./assets/images/q-ide-aidlc-rules-loaded.png?raw=true" alt="Q Developer IDE plugin의 AI-DLC 규칙" width="700" height="400">

---

### Cursor IDE

AI-DLC는 [Cursor Rules](https://cursor.com/docs/context/rules)를 사용해 지능형 워크플로를 구현합니다.

아래 명령은 zip 파일을 `Downloads` 폴더에 압축 해제해 결과 경로가 `Downloads/aidlc-rules/`라고 가정합니다. 다른 위치를 사용했다면 `Downloads`를 실제 폴더 경로로 바꾸세요.

> [!NOTE]
> **Windows 사용자:** File Explorer의 **Extract All...** 대화상자를 사용했다면 기본적으로 zip 파일명과 같은 래퍼 폴더(예: `ai-dlc-rules-v0.1.8\aidlc-rules\...`)가 생성됩니다. 아래 명령과 맞게 내용이 `Downloads\aidlc-rules\`에 바로 풀리도록 대상을 해제/수정하거나, 각 명령의 `Downloads\` 경로 앞에 `ai-dlc-rules-v<version>\`을 붙이세요. 여기서 `<version>`은 다운로드한 릴리스 버전으로 바꿉니다.

#### 옵션 1: Project Rules(권장)

**Unix/Linux/macOS:**

```bash
mkdir -p .cursor/rules

cat > .cursor/rules/ai-dlc-workflow.mdc << 'EOF'
---
description: "소프트웨어 개발을 위한 AI-DLC (AI-Driven Development Life Cycle) 적응형 워크플로"
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
description: "소프트웨어 개발을 위한 AI-DLC (AI-Driven Development Life Cycle) 적응형 워크플로"
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
echo description: "소프트웨어 개발을 위한 AI-DLC (AI-Driven Development Life Cycle) 적응형 워크플로"
echo alwaysApply: true
echo ---
echo.
) > .cursor\rules\ai-dlc-workflow.mdc

type "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rules\core-workflow.md" >> .cursor\rules\ai-dlc-workflow.mdc

mkdir .aidlc-rule-details
xcopy "%USERPROFILE%\Downloads\aidlc-rules\aws-aidlc-rule-details" ".aidlc-rule-details\" /E /I
```

#### 옵션 2: AGENTS.md(간단한 대안)

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

1. **Cursor Settings → Rules, Commands**를 엽니다.
2. **Project Rules** 아래에 `ai-dlc-workflow`가 표시되어야 합니다.
3. `AGENTS.md`를 사용하는 경우 자동으로 감지되어 적용됩니다.

![Cursor의 AI-DLC 규칙](./assets/images/cursor-ide-aidlc-rules-loaded.png?raw=true "Cursor의 AI-DLC 규칙")

**디렉터리 구조(옵션 1):**

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

AI-DLC는 Cline Rules를 사용해 지능형 워크플로를 구현합니다.

아래 명령은 zip 파일을 `Downloads` 폴더에 압축 해제해 결과 경로가 `Downloads/aidlc-rules/`라고 가정합니다. 다른 위치를 사용했다면 `Downloads`를 실제 폴더 경로로 바꾸세요.

> [!NOTE]
> **Windows 사용자:** File Explorer의 **Extract All...** 대화상자를 사용했다면 기본적으로 zip 파일명과 같은 래퍼 폴더(예: `ai-dlc-rules-v0.1.8\aidlc-rules\...`)가 생성됩니다. 아래 명령과 맞게 내용이 `Downloads\aidlc-rules\`에 바로 풀리도록 대상을 해제/수정하거나, 각 명령의 `Downloads\` 경로 앞에 `ai-dlc-rules-v<version>\`을 붙이세요. 여기서 `<version>`은 다운로드한 릴리스 버전으로 바꿉니다.

#### 옵션 1: .clinerules 디렉터리(권장)

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

#### 옵션 2: AGENTS.md(대안)

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

1. Cline 채팅 인터페이스에서 채팅 입력창 아래의 Rules 팝오버를 찾습니다.
2. `core-workflow.md`가 목록에 있고 활성화되어 있는지 확인합니다.
3. 필요하면 규칙 파일을 켜거나 끌 수 있습니다.

![Cline의 AI-DLC 규칙](./assets/images/cline-ide-aidlc-rules-loaded.png?raw=true "Cline의 AI-DLC 규칙")

**디렉터리 구조(옵션 1):**

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

AI-DLC는 Claude Code의 프로젝트 메모리 파일(`CLAUDE.md`)을 사용해 지능형 워크플로를 구현합니다.

아래 명령은 zip 파일을 `Downloads` 폴더에 압축 해제해 결과 경로가 `Downloads/aidlc-rules/`라고 가정합니다. 다른 위치를 사용했다면 `Downloads`를 실제 폴더 경로로 바꾸세요.

> [!NOTE]
> **Windows 사용자:** File Explorer의 **Extract All...** 대화상자를 사용했다면 기본적으로 zip 파일명과 같은 래퍼 폴더(예: `ai-dlc-rules-v0.1.8\aidlc-rules\...`)가 생성됩니다. 아래 명령과 맞게 내용이 `Downloads\aidlc-rules\`에 바로 풀리도록 대상을 해제/수정하거나, 각 명령의 `Downloads\` 경로 앞에 `ai-dlc-rules-v<version>\`을 붙이세요. 여기서 `<version>`은 다운로드한 릴리스 버전으로 바꿉니다.

#### 옵션 1: 프로젝트 루트(권장)

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

1. 프로젝트 디렉터리에서 Claude Code를 시작합니다(CLI: `claude` 또는 VS Code extension).
2. `/config` 명령으로 현재 구성을 확인합니다.
3. Claude에게 "What instructions are currently active in this project?"라고 질문합니다.

**디렉터리 구조(옵션 1):**

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

AI-DLC는 [GitHub Copilot custom instructions](https://code.visualstudio.com/docs/copilot/customization/custom-instructions)를 사용해 지능형 워크플로를 구현합니다. `.github/copilot-instructions.md` 파일은 자동으로 감지되어 워크스페이스의 모든 채팅 요청에 적용됩니다.

아래 명령은 zip 파일을 `Downloads` 폴더에 압축 해제해 결과 경로가 `Downloads/aidlc-rules/`라고 가정합니다. 다른 위치를 사용했다면 `Downloads`를 실제 폴더 경로로 바꾸세요.

> [!NOTE]
> **Windows 사용자:** File Explorer의 **Extract All...** 대화상자를 사용했다면 기본적으로 zip 파일명과 같은 래퍼 폴더(예: `ai-dlc-rules-v0.1.8\aidlc-rules\...`)가 생성됩니다. 아래 명령과 맞게 내용이 `Downloads\aidlc-rules\`에 바로 풀리도록 대상을 해제/수정하거나, 각 명령의 `Downloads\` 경로 앞에 `ai-dlc-rules-v<version>\`을 붙이세요. 여기서 `<version>`은 다운로드한 릴리스 버전으로 바꿉니다.

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
2. Copilot Chat 패널(Cmd/Ctrl+Shift+I)을 엽니다.
3. **Configure Chat**(톱니바퀴 아이콘) > **Chat Instructions**를 선택하고 `copilot-instructions`가 목록에 있는지 확인합니다.
4. 또는 채팅 입력창에 `/instructions`를 입력해 활성 지침을 확인합니다.

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

AI-DLC는 OpenAI Codex를 지원 코딩 에이전트로 지원하며, [Codex AGENTS.md](https://developers.openai.com/codex/guides/agents-md) 규약을 사용해 지능형 워크플로를 제공합니다. Codex는 세션을 시작할 때 프로젝트 루트의 `AGENTS.md`를 자동으로 찾아 로드합니다.

아래 명령은 zip 파일을 `Downloads` 폴더에 압축 해제해 결과 경로가 `Downloads/aidlc-rules/`라고 가정합니다. 다른 위치를 사용했다면 `Downloads`를 실제 폴더 경로로 바꾸세요.

> [!NOTE]
> **Windows 사용자:** File Explorer의 **Extract All...** 대화상자를 사용했다면 기본적으로 zip 파일명과 같은 래퍼 폴더(예: `ai-dlc-rules-v0.1.8\aidlc-rules\...`)가 생성됩니다. 아래 명령과 맞게 내용이 `Downloads\aidlc-rules\`에 바로 풀리도록 대상을 해제/수정하거나, 각 명령의 `Downloads\` 경로 앞에 `ai-dlc-rules-v<version>\`을 붙이세요. 여기서 `<version>`은 다운로드한 릴리스 버전으로 바꿉니다.

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
2. Codex에게 기존 프로젝트라면 "Using AIDLC analyze the project?", 새 프로젝트라면 "Using Aidlc what workflow do you see"라고 질문합니다.
3. Codex가 AI-DLC의 3단계 워크플로(Inception → Construction → Operations)를 설명해야 합니다.

> [!NOTE]
> `AGENTS.md` 파일은 기본 설정에서 Codex의 instruction budget 안에 들어가도록 설계되었습니다. 프로젝트별 내용을 많이 추가했고 Codex가 프로젝트 문서가 instruction limit을 초과한다고 보고하면 Codex 구성에서 제한을 늘릴 수 있습니다. 예를 들어 `config.toml` 파일의 `project_doc_max_bytes`를 조정할 수 있습니다.
>
> ```toml
> project_doc_max_bytes = 65536  # 예시 값입니다. 프로젝트에 맞는 제한을 선택하세요
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

### 기타 에이전트

AI-DLC는 프로젝트 수준의 rules 또는 steering files를 지원하는 모든 코딩 에이전트에서 동작합니다. 일반적인 접근 방식은 다음과 같습니다.

1. 에이전트가 프로젝트 규칙을 읽는 위치에 `aws-aidlc-rules/`를 배치합니다(사용 중인 에이전트 문서를 참조하세요).
2. 규칙이 참조할 수 있도록 같은 수준의 형제 위치에 `aws-aidlc-rule-details/`를 배치합니다.

에이전트에 rules files 규약이 없다면 두 폴더를 프로젝트 루트에 두고, 에이전트가 `aws-aidlc-rules/`를 규칙 디렉터리로 사용하도록 지정하세요.

---

## 사용법

1. 채팅에서 **"Using AI-DLC, ..."**라는 문구로 의도를 밝히며 소프트웨어 개발 프로젝트를 시작합니다.
2. AI-DLC 워크플로가 자동으로 활성화되어 이후 과정을 안내합니다.
3. AI-DLC가 묻는 구조화된 질문에 답합니다.
4. AI가 생성하는 모든 계획을 신중하게 검토합니다. 사용자가 직접 감독하고 검증해야 합니다.
5. 실행 계획을 검토해 어떤 단계가 실행될지 확인합니다.
6. 각 단계의 산출물을 신중하게 검토하고 승인해 제어권을 유지합니다.
7. 모든 산출물은 `aidlc-docs/` 디렉터리에 생성됩니다.

---

## 3단계 적응형 워크플로

AI-DLC는 프로젝트의 복잡도에 맞게 적응하는 구조화된 3단계 접근 방식을 따릅니다.

### 🔵 INCEPTION PHASE

**무엇을** 만들고 **왜** 만드는지 결정합니다.

- 요구 사항 분석 및 검증
- 사용자 스토리 작성(해당되는 경우)
- Application Design 및 병렬 개발을 위한 units of work 생성
- 위험 평가 및 복잡도 평가

### 🟢 CONSTRUCTION PHASE

**어떻게** 만들지 결정합니다.

- 상세 컴포넌트 설계
- 코드 생성 및 구현
- 빌드 구성 및 테스트 전략
- 품질 보증 및 검증

### 🟡 OPERATIONS PHASE

배포 및 모니터링(향후 지원)

- 배포 자동화 및 인프라
- 모니터링 및 관측성 설정
- 프로덕션 준비 상태 검증

---

## 주요 기능

| 기능                      | 설명                                                                                   |
| ------------------------- | -------------------------------------------------------------------------------------- |
| **Adaptive Intelligence** | 특정 요청에 가치가 있는 단계만 실행합니다                                              |
| **Context-Aware**         | 기존 코드베이스와 복잡도 요구 사항을 분석합니다                                        |
| **Risk-Based**            | 복잡한 변경은 포괄적으로 다루고, 단순한 변경은 효율적으로 처리합니다                   |
| **Question-Driven**       | 채팅이 아니라 파일에 구조화된 객관식 질문을 작성합니다                                 |
| **Always in Control**     | 실행 계획을 검토하고 각 단계를 승인합니다                                              |
| **Extensible**            | 보안, 컴플라이언스, 조직별 규칙 같은 사용자 지정 규칙을 핵심 워크플로 위에 얹습니다    |

---

## 확장 기능

AI-DLC는 핵심 워크플로 위에 추가 규칙을 계층화할 수 있는 확장 시스템을 지원합니다. 확장 기능은 `aws-aidlc-rule-details/extensions/` 아래에 카테고리별로 구성된 markdown 파일입니다(예: `security/`, `testing/`).

### 확장 기능 작동 방식

각 확장 기능은 같은 디렉터리에 배치되는 두 파일로 구성됩니다.

- 확장 규칙을 담은 **rules file**(예: `security-baseline.md`)
- Requirements Analysis 중 사용자에게 제시되는 구조화된 객관식 질문을 담은 **opt-in file**(예: `security-baseline.opt-in.md`)

워크플로 시작 시 AI-DLC는 `extensions/` 디렉터리를 스캔하고 `*.opt-in.md` 파일만 로드합니다. Requirements Analysis 중 각 opt-in 프롬프트를 사용자에게 제시합니다. 사용자가 opt in하면 해당 rules file을 로드합니다(명명 규칙: `.opt-in.md`를 제거하고 `.md`를 붙임). 사용자가 opt out하면 rules file은 로드하지 않습니다. 대응되는 `*.opt-in.md` 파일이 없는 확장 기능은 항상 적용됩니다.

활성화된 확장 규칙은 blocking constraints입니다. 각 단계에서 모델은 다음 단계로 진행하기 전에 준수 여부를 검증합니다.

### 기본 제공 확장 기능

`extensions/` 디렉터리에는 다음 항목이 포함되어 있습니다. 시간이 지나면서 새 확장 기능이 추가될 수 있습니다.

```text
aws-aidlc-rule-details/
└── extensions/
    ├── security/                      # 확장 카테고리
    │   └── baseline/
    │       ├── security-baseline.md          # 기준 보안 규칙
    │       └── security-baseline.opt-in.md   # Opt-in 프롬프트
    └── testing/                       # 확장 카테고리
        └── property-based/
            ├── property-based-testing.md          # Property-based testing 규칙
            └── property-based-testing.opt-in.md   # Opt-in 프롬프트
```

> [!IMPORTANT]
> 보안 확장 규칙은 AI-DLC 워크플로 안에서 효과적인 보안 규칙을 만들기 위한 방향성 있는 참고 자료로 제공됩니다. 각 조직은 프로덕션 워크플로에 배포하기 전에 자체 보안 규칙을 만들고, 사용자 지정하고, 충분히 테스트해야 합니다.

### 직접 확장 기능 추가하기

기존 카테고리를 확장하거나 완전히 새로운 카테고리를 만들 수 있습니다.

1. `extensions/` 아래에 디렉터리를 만듭니다(예: `security/compliance/` 또는 `performance/baseline/`).
2. **rules file**을 추가합니다(예: `compliance.md`). `security-baseline.md`와 같은 구조를 따르세요.
   - 각 규칙을 `## Rule <PREFIX-NN>: <Title>` 형식의 heading으로 정의합니다. prefix는 짧은 카테고리 식별자이고 NN은 순차 번호입니다(예: `COMPLIANCE-01`, `COMPLIANCE-02`). 이 ID는 감사 로그와 준수 요약에서 참조되므로, 로드되는 모든 확장 기능 전체에서 고유해야 합니다.
   - 요구 사항을 설명하는 **Rule** 섹션을 포함합니다.
   - 모델이 평가해야 하는 구체적인 확인 항목을 담은 **Verification** 섹션을 포함합니다.
3. `<name>.opt-in.md` 명명 규칙을 사용해 대응되는 **opt-in file**을 추가합니다(예: `compliance.opt-in.md`). 예상 형식은 `security-baseline.opt-in.md`를 참조하세요. 이 파일을 생략하면 사용자 opt-out 없이 항상 확장 기능이 적용됩니다.
4. 규칙은 기본적으로 blocking입니다. 검증 기준을 충족하지 못하면 해당 finding이 해결될 때까지 단계를 진행할 수 없습니다.

---

## 지원 도구

`scripts/` 디렉터리에는 AI-DLC 워크플로를 강화하는 지원 도구가 들어 있습니다.

### AIDLC Evaluator

**위치:** [`scripts/aidlc-evaluator/`](scripts/aidlc-evaluator/)

AI-DLC 워크플로 변경 사항을 검증하기 위한 자동화 테스트 및 보고 프레임워크입니다. evaluator는 다음을 제공합니다.

- **Golden Test Cases** — 검증을 위한 선별된 기준 테스트 케이스
- **Execution Framework** — 평가 파이프라인을 통해 테스트 케이스를 실행하는 오케스트레이션
- **Semantic Evaluation** — 출력의 정확성과 완성도를 평가하는 AI 기반 평가
- **Code Evaluation** — 정적 분석(linting, security scanning, duplication detection)
- **NFR Evaluation** — 비기능 요구 사항 테스트(token usage, execution time, cross-model consistency)
- **CI/CD Integration** — PR 검증을 위한 자동화 파이프라인

**빠른 시작:**

```bash
cd scripts/aidlc-evaluator
uv sync
uv run python run.py test
```

**문서:** [scripts/aidlc-evaluator/README.md](scripts/aidlc-evaluator/README.md)를 참조하세요.

---

### AIDLC Design Reviewer

**위치:** [`scripts/aidlc-designreview/`](scripts/aidlc-designreview/)

⚠️ **실험 기능** — AWS Bedrock을 통해 Claude 모델을 사용하여 AIDLC 설계 산출물을 분석하는 AI 기반 설계 리뷰 도구입니다.

**기능:**

- **Multi-Agent Review** — 세 가지 전문 AI 에이전트(Critique, Alternatives, Gap Analysis)
- **Quality Scoring** — 실행 가능한 권장 사항을 포함한 가중치 기반 심각도 분석
- **두 가지 배포 모드:**
  - **CLI Tool** — CI/CD 파이프라인용 온디맨드 리뷰
  - **Claude Code Hook** — 개발 중 실시간 리뷰(실험 기능)

**설치(CLI Tool):**

```bash
cd scripts/aidlc-designreview
uv sync --extra test
source .venv/bin/activate  # Linux/Mac
design-reviewer --aidlc-docs /path/to/aidlc-docs
```

**설치(Claude Code Hook):**

```bash
# 워크스페이스 루트에서 실행
./scripts/aidlc-designreview/tool-install/install-linux.sh      # Linux
./scripts/aidlc-designreview/tool-install/install-mac.sh        # macOS
.\scripts\aidlc-designreview\tool-install\install-windows.ps1   # Windows PowerShell
```

설치 프로그램은 워크스페이스 루트를 자동으로 감지하고 hook을 `.claude/`에 설치합니다.

**문서:**

- [scripts/aidlc-designreview/README.md](scripts/aidlc-designreview/README.md) — 기본 문서
- [scripts/aidlc-designreview/INSTALLATION.md](scripts/aidlc-designreview/INSTALLATION.md) — Hook 설치 가이드

---

## 원칙

다음은 의사 결정을 안내하는 핵심 원칙입니다.

- **No duplication**. 단일 소스가 진실의 원천입니다. 특정 파일이 필요한 새 도구나 형식을 지원해야 한다면 별도 사본을 유지하지 않고 원본에서 생성합니다.

- **Methodology first**. AI-DLC는 근본적으로 도구가 아니라 방법론입니다. 사용자가 시작하기 위해 무언가를 설치할 필요가 없어야 합니다. 다만 사용자가 방법론을 도입하거나 확장하는 데 도움이 된다면 향후 편의 도구(scripts, CLIs)를 도입할 수 있습니다.

- **Reproducible**. 규칙은 서로 다른 모델이 비슷한 결과를 낼 만큼 명확해야 합니다. 모델마다 다르게 동작한다는 점은 알고 있지만, 방법론은 명시적인 지침을 통해 편차를 최소화해야 합니다.

- **Agnostic**. 이 방법론은 모든 IDE, agent, model과 함께 동작합니다. 특정 도구나 vendor에 묶이지 않습니다.

- **Human in the loop**. 중요한 결정에는 명시적인 사용자 확인이 필요합니다. 에이전트가 제안하고, 사람이 승인합니다.

---

## 사전 요구 사항

Assisted AI Coding을 위해 지원 플랫폼/도구 중 하나가 설치되어 있어야 합니다.

| 플랫폼                        | 설치 링크                                                                                                                                                       |
| ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Kiro                          | [Install](https://kiro.dev/)                                                                                                                                    |
| Kiro CLI                      | [Install](https://kiro.dev/cli/)                                                                                                                                |
| Amazon Q Developer IDE Plugin | [Install](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/q-in-IDE.html)                                                                               |
| Cursor IDE                    | [Install](https://cursor.com/)                                                                                                                                  |
| Cline VS Code Extension       | [Install](https://marketplace.visualstudio.com/items?itemName=saoudrizwan.claude-dev)                                                                           |
| Claude Code CLI               | [Install](https://github.com/anthropics/claude-code)                                                                                                            |
| GitHub Copilot                | [Install](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot) + [Chat](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot-chat) |

---

## 문제 해결

### 일반 문제

| 문제                           | 해결 방법                                                       |
| ------------------------------ | --------------------------------------------------------------- |
| 규칙이 로드되지 않음           | 해당 플랫폼의 올바른 위치에 파일이 있는지 확인하세요            |
| 파일 인코딩 문제               | 파일이 UTF-8로 인코딩되어 있는지 확인하세요                     |
| 세션에 규칙이 적용되지 않음    | 파일 변경 후 새 채팅 세션을 시작하세요                          |
| 규칙 상세 파일이 로드되지 않음 | `.aidlc-rule-details/`가 하위 디렉터리와 함께 있는지 확인하세요 |

### 플랫폼별 문제

#### Amazon Q Developer / Kiro

- `/context show`를 사용해 규칙이 로드되었는지 확인합니다.
- `.amazonq/rules/` 또는 `.kiro/steering/` 디렉터리 구조를 확인합니다.

#### Cursor

- "Apply Intelligently"를 사용하려면 frontmatter에 description이 정의되어 있는지 확인합니다.
- **Cursor Settings → Rules**에서 규칙이 활성화되어 있는지 확인합니다.
- 규칙이 너무 크면(500줄 초과) 여러 개의 집중된 규칙으로 나눕니다.

#### Cline

- 채팅 입력창 아래의 Rules 팝오버를 확인합니다.
- 팝오버 UI를 사용해 필요에 따라 규칙 파일을 켜거나 끕니다.

#### Claude Code

- `/config` 명령으로 현재 구성을 확인합니다.
- "What instructions are currently active in this project?"라고 질문합니다.

#### GitHub Copilot

- **Configure Chat**(톱니바퀴 아이콘) > **Chat Instructions**를 선택해 지침이 로드되었는지 확인합니다.
- 채팅 입력창에 `/instructions`를 입력해 활성 지침 파일을 확인합니다.
- 워크스페이스 루트에 `.github/copilot-instructions.md`가 있는지 확인합니다.

### Windows의 파일 경로 문제

- markdown 파일 안의 파일 경로에는 forward slash `/`를 사용합니다.
- backslash를 사용하는 Windows 경로는 올바르게 동작하지 않을 수 있습니다.

---

## 버전 관리 권장 사항

**저장소에 커밋할 항목:**

```gitignore
# 버전 관리해야 하는 항목
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

**선택 사항 - 필요한 경우 `.gitignore`에 추가:**

```gitignore
# 로컬 전용 설정
.claude/settings.local.json
```

---

## 생성되는 aidlc-docs/ 참조

AI-DLC 워크플로가 생성하는 모든 문서 산출물의 전체 참조는 [docs/GENERATED_DOCS_REFERENCE.md](docs/GENERATED_DOCS_REFERENCE.md)를 참조하세요.

---

## 실험: AI 지원 설정(릴리스 다운로드)

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
| Working with AI-DLC (interaction patterns and tips) | [docs/WORKING-WITH-AIDLC.md](docs/WORKING-WITH-AIDLC.md)                                                                      |
| Contributing Guidelines                             | [CONTRIBUTING.md](CONTRIBUTING.md)                                                                                            |
| Code of Conduct                                     | [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md)                                                                                      |

---

## 추가 리소스

<!-- TODO: 안정적인 영구 URL이 준비되면 이 Amplify URL을 교체하세요 -->
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
| Working with AI-DLC (interaction patterns and tips) | [docs/WORKING-WITH-AIDLC.md](docs/WORKING-WITH-AIDLC.md)                                                                      |
| Contributing Guidelines                             | [CONTRIBUTING.md](CONTRIBUTING.md)                                                                                            |
| Code of Conduct                                     | [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md)                                                                                      |

---

## 기여

자세한 내용은 [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications)을 참조하세요.

## 라이선스

이 라이브러리는 MIT-0 License에 따라 라이선스가 부여됩니다. [LICENSE](LICENSE) 파일을 참조하세요.
