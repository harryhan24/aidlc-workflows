# AGENTS.md

## 프로젝트 개요

AI-DLC (AI-Driven Development Life Cycle)는 AI 코딩 에이전트를 구조화된 소프트웨어 개발 워크플로우로 안내하기 위한 방법론입니다. 이 저장소에는 핵심 워크플로우 룰, 단계별 상세 룰, 그리고 evaluator 프레임워크가 포함되어 있습니다.

배포되는 산출물은 `aidlc-rules/` 디렉터리이며, GitHub Releases를 통해 zip으로 압축되어 게시됩니다.

## 저장소 구조

```text
aidlc-rules/
├── aws-aidlc-rules/              # 핵심 워크플로우 진입점 (이름 변경 금지)
│   └── core-workflow.md
└── aws-aidlc-rule-details/       # 워크플로우가 참조하는 상세 룰 (이름 변경 금지)
    ├── common/                   # 모든 단계에서 공유되는 가이드
    ├── inception/                # 기획 및 아키텍처 룰
    ├── construction/             # 설계 및 구현 룰
    ├── extensions/               # 선택적 횡단 제약(cross-cutting constraint) 룰
    └── operations/               # 배포 및 모니터링 룰
scripts/aidlc-evaluator/          # Python 평가 프레임워크 (uv로 관리)
docs/
├── ADMINISTRATIVE_GUIDE.md       # CI/CD, 워크플로우, 시크릿, 릴리스 프로세스
├── DEVELOPERS_GUIDE.md           # 로컬 빌드 (CodeBuild, act), 보안 스캐너
├── WORKING-WITH-AIDLC.md         # AI-DLC 방법론 사용자 가이드
├── GENERATED_DOCS_REFERENCE.md   # aidlc-docs/ 디렉터리 전체 레퍼런스
└── writing-inputs/               # vision/tech-env 문서 가이드와 예시
.github/
├── workflows/                    # CI/CD 파이프라인 (워크플로우 8개)
├── dependabot.yml                # Dependabot 의존성 업데이트 설정
├── CODEOWNERS                    # PR 리뷰를 위한 코드 소유권 룰
├── ISSUE_TEMPLATE/               # 이슈 템플릿
├── pull_request_template.md      # 기여자 선언이 포함된 PR 템플릿
└── labeler.yml                   # 자동 라벨 룰 (경로 → 라벨 매핑)
.claude/                          # Claude Code 프로젝트 설정
```

## 주요 문서

- [CONTRIBUTING.md](CONTRIBUTING.md) — 기여 프로세스 및 컨벤션
- [docs/ADMINISTRATIVE_GUIDE.md](docs/ADMINISTRATIVE_GUIDE.md) — CI/CD 아키텍처, 보호된 환경, 시크릿, 권한, 릴리스 프로세스
- [docs/DEVELOPERS_GUIDE.md](docs/DEVELOPERS_GUIDE.md) — CodeBuild 로컬 실행, 보안 스캐너 상세 및 조치 안내
- [docs/WORKING-WITH-AIDLC.md](docs/WORKING-WITH-AIDLC.md) — AI-DLC 방법론 사용자 가이드(컨텍스트 관리, 프롬프트 패턴, 단계별 워크스루)
- [docs/GENERATED_DOCS_REFERENCE.md](docs/GENERATED_DOCS_REFERENCE.md) — 워크플로우 중 생성되는 `aidlc-docs/` 디렉터리 구조 전체 레퍼런스
- [docs/writing-inputs/](docs/writing-inputs/) — vision 및 technical environment 문서 가이드와 예시

**작업 유형별 읽어야 할 문서:**

- CI/CD, 워크플로우, 릴리스 → `ADMINISTRATIVE_GUIDE.md`, `DEVELOPERS_GUIDE.md`
- aidlc-rules 내용 → `WORKING-WITH-AIDLC.md`, `GENERATED_DOCS_REFERENCE.md`
- Vision 또는 technical environment 문서 → `docs/writing-inputs/`

## 셋업 명령

```bash
# 모든 마크다운 파일을 린트
npx markdownlint-cli2 "**/*.md"

# 마크다운 린트 이슈를 자동 수정
npx markdownlint-cli2 --fix "**/*.md"

# Evaluator 테스트 실행 (scripts/aidlc-evaluator/ 에서)
cd scripts/aidlc-evaluator && uv run pytest
```

## 코드 스타일

- 모든 콘텐츠는 마크다운 — `.markdownlint-cli2.yaml` 설정을 따릅니다.
- MD013(라인 길이)은 비활성화 — 긴 URL, 표, 코드 예시는 허용됩니다.
- MD033(inline HTML)은 비활성화 — 스크린샷용 `<img>` 태그를 사용합니다.
- MD024(중복 헤딩)는 비활성화 — 플랫폼 가이드 전반에서 섹션명이 반복됩니다.
- MD036(emphasis as heading)은 비활성화 — 리스트의 서브 라벨로 볼드 텍스트를 사용합니다.
- MD060(table alignment)은 강제 — 표의 파이프(`|`)는 세로로 정렬되어야 합니다.
- MD040(fenced code language)은 강제 — 코드 펜스에는 항상 언어를 명시합니다.
- 커밋 메시지는 [conventional commits](https://www.conventionalcommits.org/)를 따릅니다 (예: `feat:`, `fix:`, `docs:`, `chore:`).

## 테스팅 가이드

- 룰 변경은 제출 전에 지원되는 플랫폼(Amazon Q Developer, Kiro, Cursor, Cline, Claude Code, GitHub Copilot) 중 최소 하나에서 테스트합니다.
- 설치 안내를 추가하거나 수정한 경우 macOS, Windows CMD, Windows PowerShell에서 테스트합니다.
- 커밋 전에 `npx markdownlint-cli2 "**/*.md"`를 실행해 린트 이슈를 잡습니다.
- 설정되어 있다면 pre-commit 훅이 markdownlint를 자동 실행합니다.

## PR 가이드

- PR 제목은 conventional commits 형식을 따라야 합니다 (예: `fix: description`).
- PR 본문 끝에 항상 다음 기여자 선언(contributor statement)을 포함합니다:

  > By submitting this pull request, I confirm that you can use, modify, copy,
  > and redistribute this contribution, under the terms of the
  > [project license](https://github.com/awslabs/aidlc-workflows/blob/main/LICENSE).

- CI에서 강제하는 항목: conventional commit 제목, 기여자 선언, markdownlint, do-not-merge 라벨 체크.
- `.github/pull_request_template.md`의 구조를 사용합니다.

## 보안 스캐너

`main`으로의 모든 푸시, 모든 PR, 그리고 매일 6개의 스캐너가 실행됩니다. HIGH/CRITICAL 수준의 모든 발견사항은 머지 전에 조치되거나 문서화된 리스크 수용(risk acceptance)이 있어야 합니다.

| 스캐너   | 탐지 대상              | 실패 기준                   | 설정                                        |
| -------- | ---------------------- | --------------------------- | ------------------------------------------- |
| Bandit   | Python SAST 이슈       | 높은 confidence의 발견      | `.bandit`                                   |
| Semgrep  | 다중 언어 SAST         | 모든 발견 (PR: 신규만)      | `.semgrepignore`                            |
| Grype    | 의존성 CVE             | high/critical CVE           | `.grype.yaml`                               |
| Gitleaks | git 히스토리 내 시크릿 | 베이스라인이 아닌 모든 시크릿 | `.gitleaks.toml`, `.gitleaks-baseline.json` |
| Checkov  | IaC 오설정             | 모든 체크 실패              | `.checkov.yaml`                             |
| ClamAV   | 멀웨어                 | 모든 탐지                   | None                                        |

인라인 억제(suppression) 패턴:

- Bandit: `# nosec BXXX — justification`
- Semgrep: `# nosemgrep: rule-id — justification`
- Checkov: `# checkov:skip=CKV_ID:justification`

전체 조치 및 억제 상세는 [docs/DEVELOPERS_GUIDE.md](docs/DEVELOPERS_GUIDE.md#security-scanners)를 참고하세요.

## 중요한 제약사항

- 폴더명 `aws-aidlc-rules/`와 `aws-aidlc-rule-details/`는 공개 계약(public contract)의 일부입니다 — 이름 변경, 이동, 재구성 금지.
- 룰 전반에 내용을 중복하지 마세요 — 공유 가이드는 `common/`에 두고 참조합니다.
- 핵심 방법론은 IDE/에이전트/모델에 종속되지 않도록 유지합니다.
- 보안 이슈는 공개 GitHub 이슈가 아닌 [AWS vulnerability reporting](http://aws.amazon.com/security/vulnerability-reporting/)을 통해 신고해야 합니다.
- `CHANGELOG.md`는 git-cliff가 자동 생성합니다 — 수동 편집 금지.

## 에이전트 실행용 스니펫 (Copilot 추가분)

에이전트를 위한 짧은 가이드: 저장소의 uv 래퍼와 npx 기반 도구를 우선 사용하세요. 명령어를 실행하기 전에 docs/DEVELOPERS_GUIDE.md와 docs/ADMINISTRATIVE_GUIDE.md를 읽어보세요.

테스트 (uv):

```bash
uv run pytest
uv run pytest --cov --cov-report=term-missing
```

마크다운 린트 (npx):

```bash
npx markdownlint-cli2 "**/*.md"
npx markdownlint-cli2 --fix "**/*.md"
```

Docker로 실행하는 보안 스캔 (로컬, 크로스 플랫폼에 권장):

```bash
# Grype
docker run --rm -v "$PWD:/workspace" anchore/grype:latest grype dir:/workspace -o sarif=grype.sarif
# Gitleaks
docker run --rm -v "$PWD:/repo" zricethezav/gitleaks:latest detect --source /repo --report-format sarif --report-path gitleaks.sarif
# Semgrep
docker run --rm -v "$PWD:/src" returntocorp/semgrep semgrep --config=r/all --sarif /src > semgrep.sarif
# Checkov
docker run --rm -v "$PWD:/src" bridgecrew/checkov --directory /src --output-file-path checkov.sarif --output sarif
# Bandit
docker run --rm -v "$PWD:/src" python:3.12-slim bash -c "pip install -q bandit && bandit -r /src -f sarif -o /src/bandit.sarif"
# ClamAV
docker run --rm -v "$PWD:/data" mkodockx/docker-clamav clamscan -r /data --log=/data/clamdscan.txt
```

참고 사항:

- 위 명령은 SARIF/텍스트 산출물을 프로젝트 루트에 기록해서 CI/에이전트가 소비할 수 있도록 합니다.
- CI에서는 이미 스캐너가 실행됩니다 — Docker를 사용할 수 있는 환경에서 로컬 검증용으로 사용하세요.
- Docker를 사용할 수 없다면 docs/DEVELOPERS_GUIDE.md에 문서화된 플랫폼별 설치를 사용하세요.
