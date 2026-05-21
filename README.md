# AI-DLC Workflows (v2)

> **사람은 판단을 코드화하고, AI는 그것을 결정론적으로 orchestrate하고 self-verify합니다.**

> [!NOTE]
> v2는 [AI-DLC Workflows 2.0 Specification](./assets/AI-DLC-Workflows-2.0-Specification.pdf) 백서에 명시된 자율 소프트웨어 개발 비전, 핵심 원칙, 그리고 아키텍처를 구현합니다. v2는 [aidlc-workflows](https://github.com/awslabs/aidlc-workflows)의 활성 개발 브랜치입니다. 레이아웃, skill 세트, 설치 모델은 여전히 진화 중이며 호환성이 깨지는 변경이 발생할 수 있습니다. 안정 버전이 필요하다면 `main`을 사용하세요.

> [!IMPORTANT]
> 생성형 AI는 실수할 수 있습니다. 선택한 AI 모델과 agentic 코딩 어시스턴트가 생성한 모든 출력과 비용을 검토하는 것이 좋습니다. [AWS Responsible AI Policy](https://aws.amazon.com/ai/responsible-ai/policy/)를 참고하세요.


AI-DLC에 대해 더 알아보려면 이 [블로그](https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/)와 거기서 참조하는 [Method Definition Paper](https://prod.d13rzhkk8cj2z0.amplifyapp.com/)를 읽어보세요.


## 목차

- [시작하기](#getting-started)
- [플랫폼 지원](#platform-support)
- [사용법](#usage)
- [문제 해결](#troubleshooting)
- [기여하기](#contributing)
- [라이선스](#license)

---

## 시작하기

v2는 아직 릴리스 zip을 배포하지 않습니다. 사용해 보려면 이 브랜치를 clone한 뒤 배포물을 직접 빌드해야 합니다.

1. 이 repo를 clone하고 v2 브랜치를 프로젝트 **외부** 폴더(예: `~/workspaces`)로 체크아웃합니다.
2. Kiro 배포물을 빌드합니다:

   ```bash
   make build-kiro
   ```

   이 명령은 `dist/kiro/.kiro/`를 생성하며, Kiro IDE와 Kiro CLI 모두에서 동작합니다.
3. 아래에서 사용 중인 플랫폼의 설정 지침을 따릅니다.

사전 요구사항:

- [Node.js](https://nodejs.org/) (빌드 스크립트와 결정론적 process checker가 사용)
- 아래에 나열된 지원 플랫폼 중 하나

---

## 플랫폼 지원

v2는 현재 다음을 지원합니다:

- [Kiro IDE](https://kiro.dev/)
- [Kiro CLI](https://kiro.dev/cli/)

두 플랫폼은 동일한 `.kiro/` 설치 디렉토리를 공유합니다. 더 많은 대상(Q Developer, Cursor, Cline, Claude Code, Copilot 등)이 계획되어 있습니다 — 지금 당장 필요하다면 v1을 사용하세요.

### Kiro

AI-DLC v2는 프로젝트 루트에 `.kiro/` 디렉토리 형태로 설치됩니다. 아래 명령들은 이 repo를 `~/workspaces/aidlc-workflows-v2`에 clone하고 `make build-kiro`를 실행했다고 가정합니다. 다른 곳에 clone했다면 경로를 적절히 바꿔주세요.

**macOS / Linux:**

```bash
cp -R ~/workspaces/aidlc-workflows-v2/dist/kiro/.kiro .
```

**Windows (PowerShell):**

```powershell
Copy-Item -Recurse -Force "$env:USERPROFILE\workspaces\aidlc-workflows-v2\dist\kiro\.kiro" .
```

**Windows (CMD):**

```cmd
xcopy %USERPROFILE%\workspaces\aidlc-workflows-v2\dist\kiro\.kiro .kiro\ /E /I /Y
```

프로젝트는 다음과 같은 구조가 되어야 합니다:

```text
<project-root>/
└── .kiro/
    ├── agents/
    ├── aidlc-common/
    ├── hooks/
    └── skills/
```

#### Kiro IDE에서 확인하기

steering / skills 패널을 열고 워크스페이스 아래에 AI-DLC skill 항목들(`aidlc-orchestrator`, `aidlc-intent-bootstrap`, `aidlc-requirements-analysis` 등)이 보이는지 확인합니다.

AI-DLC workflow를 실행할 때는 Kiro IDE를 **Vibe** 모드로 사용하세요. Kiro가 Spec 모드로 전환할 것을 권유해도 거절하고 Vibe 모드에 머무세요.

#### Kiro CLI에서 확인하기

`kiro-cli`를 실행한 뒤 `/context show`를 입력합니다. `.kiro/skills/`와 `.kiro/aidlc-common/`이 목록에 보이는지 확인합니다.

---

## 사용법

1. 채팅창에 **"Using AI-DLC, ..."** 라는 문구로 intent를 진술하며 소프트웨어 개발 작업을 시작합니다.
2. AI-DLC orchestrator가 자동으로 활성화되어 intent에 맞춘 workflow를 제안합니다.
3. orchestrator가 생성하는 구조화된 질문들에 답하고, 제안된 plan을 승인합니다.
4. 각 stage마다 artifact를 검토하고, 다음 stage가 실행되기 전에 승인합니다.
5. artifact는 프로젝트 내부가 아니라 옆에 있는 `org-ai-kb/aidlc-docs/intent-<nnn>-<slug>/`에 저장됩니다.

---

## 문제 해결

| 문제 | 해결 방법 |
| --- | --- |
| Kiro에서 skill이 로드되지 않음 | 프로젝트 루트에 `.kiro/`가 존재하고 그 안에 `agents/`, `aidlc-common/`, `hooks/`, `skills/` 하위 폴더가 있는지 확인하세요. 복사 후에는 Kiro 세션을 재시작하세요. |
| 파일 인코딩 문제 | 파일이 UTF-8로 인코딩되어 있는지 확인하세요. |
| 세션에 규칙이 적용되지 않음 | `.kiro/`를 업데이트한 뒤 새 채팅 세션을 시작하세요. |
| 빌드 실패 | Node.js가 설치되어 있고 `PATH`에 등록되어 있는지 확인한 뒤 `make clean && make build-kiro`를 실행하세요. |
| Windows 경로가 해석되지 않음 | markdown 파일 안에서는 슬래시 `/`를 사용하세요. 역슬래시는 경로 해석을 깨뜨릴 수 있습니다. |

---

## 기여하기

v2는 pre-release 상태입니다. 실험해 보고 싶다면:

- `src/`만 수정하세요 — `dist/`는 생성물입니다.
- `make build-kiro`로 다시 빌드한 뒤 `src/` 변경과 재생성된 `dist/`를 함께 커밋하세요.

전체 기여 가이드는 v2가 이 브랜치를 졸업하기 전에 추가될 예정입니다.

---

## 라이선스

이 라이브러리는 MIT-0 License로 배포됩니다. [LICENSE](LICENSE) 파일을 참고하세요.
