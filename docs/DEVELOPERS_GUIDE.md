# 개발자 가이드 (Developer's Guide)

## CodeBuild를 로컬에서 실행하기

[CodeBuild local agent](https://docs.aws.amazon.com/codebuild/latest/userguide/use-codebuild-agent.html)를 사용하면 AWS CodeBuild 빌드를 로컬에서 실행할 수 있습니다. 원격으로 푸시하지 않고 buildspec 변경 사항을 테스트할 때 유용합니다.

### 사전 요구사항

- Docker가 설치되어 실행 중일 것
- `codebuild_build.sh` 스크립트:

### 기본 사용법

1. Setup

- 로컬 CodeBuild 스크립트를 다운로드하고 실행 가능하게 만듭니다.
- `GH_TOKEN` 환경 변수의 GitHub Personal Access Token (PAT)을 `./.env` 파일로 전송합니다.

```bash
if [ ! -f codebuild_build.sh ]; then
  curl -O https://raw.githubusercontent.com/aws/aws-codebuild-docker-images/master/local_builds/codebuild_build.sh && chmod +x codebuild_build.sh;
fi;
echo "GH_TOKEN=${GH_TOKEN:-ghp_notset}" > "./.env";
```

1. Iterate

- _선택적으로 `.github/workflows/codebuild.yml` GitHub 워크플로우의 `buildspec-override` 값을 편집_
- 워크플로우 내용에 따라 `./buildspec.yml`을 로컬 파일로 업데이트
- 머신 아키텍처에 맞는 이미지로 AWS CodeBuild 빌드를 로컬에서 실행

```bash
cat .github/workflows/codebuild.yml \
    | uvx yq -r '.jobs.build.steps[] | select(.id == "codebuild") | .with["buildspec-override"]' \
    > buildspec.yml
./codebuild_build.sh \
  -i "public.ecr.aws/codebuild/amazonlinux-$([ "$(arch)" = "arm64" -o "$(arch)" = "aarch64" ] && echo "aarch64" || echo "x86_64")-standard:$([ "$(arch)" = "arm64" -o "$(arch)" = "aarch64" ] && echo "3.0" || echo "5.0")" \
  -a "./.codebuild/artifacts/" \
  -l "public.ecr.aws/codebuild/local-builds:$([ "$(arch)" = "arm64" -o "$(arch)" = "aarch64" ] && echo "aarch64" || echo "latest")" \
  -c \
  -e "./.env"
```

### 전체 스크립트 옵션

| 플래그         | 필수       | 설명                                                                                                                                                                                                  |
| -------------- | ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-i IMAGE`     | Yes        | 고객 빌드 컨테이너 이미지 (예: `aws/codebuild/standard:5.0`)                                                                                                                                          |
| `-a DIR`       | Yes        | 아티팩트 출력 디렉토리                                                                                                                                                                                |
| `-b FILE`      | No         | Buildspec 오버라이드 파일. 기본값은 소스 디렉토리의 `buildspec.yml`                                                                                                                                   |
| `-s DIR`       | No         | 소스 디렉토리. 첫 번째 `-s`가 primary source이며, 추가 `-s` 플래그는 secondary source에 대해 `<sourceIdentifier>:<sourceLocation>` 형식을 사용. 기본값은 현재 작업 디렉토리                            |
| `-l IMAGE`     | No         | 기본 로컬 에이전트 이미지 오버라이드                                                                                                                                                                  |
| `-r DIR`       | No         | 리포트 출력 디렉토리                                                                                                                                                                                  |
| `-c`           | No         | 로컬 호스트의 AWS 설정 및 자격 증명 사용 (`~/.aws` 및 `AWS_*` 환경 변수)                                                                                                                              |
| `-p PROFILE`   | No         | 사용할 AWS CLI 프로파일 (`-c` 필요)                                                                                                                                                                   |
| `-e FILE`      | No         | 환경 변수가 담긴 파일 (`VAR=VAL` 형식, 한 줄에 하나)                                                                                                                                                  |
| `-m`           | No         | 소스 디렉토리를 빌드 컨테이너에 직접 마운트                                                                                                                                                           |
| `-d`           | No         | 빌드 컨테이너를 Docker privileged 모드로 실행                                                                                                                                                         |

## Security Scanners

[`security-scanners.yml`](../.github/workflows/security-scanners.yml) 워크플로우는 `main`으로의 모든 푸시, `main`을 타겟으로 하는 모든 PR, 매일 정해진 스케줄에 여섯 개의 스캐너를 실행합니다. 각 스캐너는 SARIF 리포트를 GitHub Code Scanning(**Security** 탭에서 확인)과 다운로드 가능한 아티팩트로 업로드합니다.

ClamAV를 제외한 모든 스캐너는 **deferred-failure 패턴**을 사용합니다. 스캔은 항상 끝까지 실행되고 결과를 업로드한 뒤에 잡이 실패합니다. 이는 빌드가 깨지더라도 발견 사항이 기록되도록 보장합니다.

### Bandit — Python SAST

**무엇을 탐지하는가:** Python 코드의 일반적인 보안 이슈(예: `subprocess`, `eval` 사용, 하드코딩된 패스워드, 약한 암호화).

**무엇이 실패를 트리거하는가:** 심각도 레벨에 관계없이 **high confidence**를 가진 모든 발견. 정확한 필터는 [`.github/workflows/security-scanners.yml`](../.github/workflows/security-scanners.yml)의 Bandit 설정을 참고하세요.

**범위:** 저장소의 모든 추적되는 Python 파일에 대해 실행됩니다. 정확한 include/exclude 패턴은 [`.github/workflows/security-scanners.yml`](../.github/workflows/security-scanners.yml)을 참고하세요.

**발견 사항 검토 방법:**

1. GitHub Security 탭의 **Code Scanning** 알림을 확인하거나 `bandit.sarif` 아티팩트를 다운로드
2. 각 발견 사항에는 Bandit 룰 ID(예: `B603`)와 위험 설명이 포함됨

**시정 조치 방법:**

- **코드 수정** — 선호되는 접근. Bandit 문서는 각 룰에 대한 안전한 대안을 나열
- **인라인 억제(Suppress inline)** — 영향 받는 라인에 `# nosec BXXX`(정당화와 함께)를 추가:

  ```python
  subprocess.run(cmd, check=True)  # nosec B603 — cmd is built from validated config, not user input
  ```

- **경로 제외** — `.bandit`의 `exclude` 리스트에 추가

### Semgrep — 다중 언어 SAST

**무엇을 탐지하는가:** 전체 Semgrep Registry(`--config=r/all`)를 사용해 모든 언어에서 보안 안티패턴, 위험한 API 사용, 코드 품질 이슈를 탐지.

**무엇이 실패를 트리거하는가:** 모든 발견. PR에서는 PR 베이스 커밋 대비 **새** 발견만 실패를 트리거합니다 — 기존 발견은 `--baseline-commit`을 통해 무시됩니다.

**발견 사항 검토 방법:**

1. **Code Scanning** 알림을 확인하거나 `semgrep.sarif` 아티팩트를 다운로드
2. 각 발견 사항에는 룰 ID(예: `python.lang.security.dangerous-subprocess-use-audit`)와 룰 문서 링크가 포함됨

**시정 조치 방법:**

- **코드 수정** — Semgrep Registry 문서의 룰 권장 수정을 따름
- **인라인 억제** — 영향 받는 라인에 `# nosemgrep: <rule-id>` 추가:

  ```python
  time.sleep(5)  # nosemgrep: arbitrary-sleep — polling for server startup
  ```

  YAML 파일의 경우:

  ```yaml
  run: exit ${{ steps.scan.outputs.exit_code }}  # nosemgrep: yaml.github-actions.security.curl-eval.curl-eval
  ```

- **경로 제외** — 경로를 `.semgrepignore`에 추가 (참고: `changed-semgrepignore` 감사 룰이 새 항목을 앱-시큐리티 리뷰 대상으로 표시)

### Grype — 의존성 취약점 스캐닝 (SCA)

**무엇을 탐지하는가:** lock 파일, manifest, 컨테이너 이미지를 스캔하여 프로젝트 의존성의 알려진 CVE.

**무엇이 실패를 트리거하는가:** **high 또는 critical**로 평가된 모든 취약점(`.grype.yaml`의 `fail-on-severity: high`). low 및 medium 취약점은 보고되지만 빌드를 실패시키지 않습니다.

**발견 사항 검토 방법:**

1. **Code Scanning** 알림을 확인하거나 `grype.sarif` 아티팩트를 다운로드
2. 각 발견 사항에는 CVE ID, 영향 받는 패키지, 설치된 버전, 수정된 버전(있는 경우)이 포함됨

**시정 조치 방법:**

- **의존성 업그레이드** — 선호되는 접근. 패치된 버전이 존재하는지 확인하고 관련 `pyproject.toml` 또는 lock 파일을 업데이트
- **설정에서 억제** — 이유와 함께 `.grype.yaml`의 `ignore` 리스트에 항목 추가:

  ```yaml
  ignore:
    - vulnerability: CVE-2024-12345
      reason: "only affects server-side XML parsing which we don't use"
  ```

  특정 패키지로 범위 지정 가능:

  ```yaml
  ignore:
    - vulnerability: CVE-2024-12345
      package:
        name: "some-package"
        version: "1.2.3"
      reason: "pinned version; affected code path is unreachable"
  ```

> **참고:** Grype는 SCA 스캐너입니다 — 소스 라인이 아니라 의존성을 분석합니다. 억제용 인라인 코드 코멘트는 없으며, 수용되는 모든 위험은 `.grype.yaml`에 들어갑니다.

### Gitleaks — 시크릿 탐지

**무엇을 탐지하는가:** git 히스토리 어딘가에 커밋된 시크릿(API 키, 토큰, 패스워드, 개인 키).

**무엇이 실패를 트리거하는가:** 베이스라인 파일(`.gitleaks-baseline.json`)에 존재하지 않는 모든 시크릿.

**발견 사항 검토 방법:**

1. `gitleaks.sarif` 아티팩트 다운로드
2. 각 발견 사항은 시크릿 타입(예: `generic-api-key`, `jwt`), 파일, 커밋을 식별

**시정 조치 방법:**

- **즉시 시크릿 회전** — 탐지된 모든 시크릿은 손상된 것으로 간주
- **히스토리에서 제거** — `git filter-repo` 또는 BFG Repo-Cleaner를 사용해 모든 커밋에서 시크릿 제거
- **베이스라인에 추가** — 알려진 false positive에만(예: 합성 자격 증명을 가진 테스트 픽스처). 베이스라인 재생성:

  ```bash
  gitleaks git --config=.gitleaks.toml --report-path=.gitleaks-baseline.json --report-format=json .
  ```

  커밋 전에 업데이트된 베이스라인을 신중히 검토
- **경로 허용리스트** — 시크릿 같은 패턴을 의도적으로 포함하는 파일(예: 테스트 자격 증명 스크러버)에 대해 `.gitleaks.toml`의 `[allowlist] paths`에 정규식 추가

### Checkov — Infrastructure as Code 스캐닝

**무엇을 탐지하는가:** GitHub Actions 워크플로우와 Dockerfile의 설정 오류(예: 고정되지 않은 액션, 누락된 보안 설정, 지나치게 광범위한 권한).

**범위:** `github_actions`와 `dockerfile` 프레임워크만 스캔(`.checkov.yaml`에서 설정).

**무엇이 실패를 트리거하는가:** `skip-check`에 나열된 체크를 제외한 모든 체크 실패.

**발견 사항 검토 방법:**

1. **Code Scanning** 알림을 확인하거나 `checkov.sarif` 아티팩트를 다운로드
2. 각 발견 사항에는 체크 ID(예: `CKV_GHA_7`, `CKV_DOCKER_2`)와 설정 오류 설명이 포함됨

**시정 조치 방법:**

- **설정 수정** — 특정 체크 ID에 대해 Checkov 문서를 따름
- **인라인 억제** — 영향 받는 라인 위 또는 라인에 코멘트 추가:

  Dockerfile의 경우:

  ```dockerfile
  # checkov:skip=CKV_DOCKER_2:healthcheck not needed for build-only image
  FROM python:3.12-slim
  ```

  GitHub Actions 워크플로우의 경우:

  ```yaml
  # checkov:skip=CKV_GHA_7:buildspec-override requires user parameters
  - uses: aws-actions/aws-codebuild-run-build@v1
  ```

  한 라인에 여러 skip:

  ```yaml
  # checkov:skip=CKV_DOCKER_2,CKV_DOCKER_3:reason for both
  ```

- **저장소 전체에서 skip** — 이유 설명 코멘트와 함께 `.checkov.yaml`의 `skip-check` 리스트에 체크 ID 추가

### ClamAV — 멀웨어 스캐닝

**무엇을 탐지하는가:** ClamAV의 시그니처 데이터베이스를 사용해 저장소 파일의 멀웨어, 바이러스, 트로이목마를 탐지.

**무엇이 실패를 트리거하는가:** 모든 멀웨어 탐지(이진 pass/fail).

**발견 사항 검토 방법:**

1. `clamdscan.txt` 아티팩트 다운로드 — 감염된 파일 경로가 포함된 전체 스캔 로그가 들어있음

> **참고:** ClamAV는 SARIF 출력을 생성하지 않으며 GitHub Code Scanning과 통합되지 않습니다. 결과는 텍스트 로그 아티팩트로만 제공됩니다.

**시정 조치 방법:**

- **감염된 파일 제거** 및 어떻게 유입되었는지 조사
- **탐지 검증** — false positive는 드물지만 가능합니다. 알려진 FP 데이터베이스에서 ClamAV 시그니처 이름 확인

### 실패 임계값 요약

| Scanner  | 실패 조건                          | 심각도 필터              | 설정 파일                                    |
| -------- | ---------------------------------- | ------------------------ | -------------------------------------------- |
| Bandit   | high confidence를 가진 모든 발견   | 모든 심각도              | `.bandit`                                    |
| Semgrep  | 모든 발견 (PR: 새 발견만)          | 모든 심각도              | `.semgrepignore`                             |
| Grype    | High 또는 critical CVE             | Low/medium은 실패 아님   | `.grype.yaml`                                |
| Gitleaks | 베이스라인에 없는 모든 시크릿      | 전부                     | `.gitleaks.toml`, `.gitleaks-baseline.json`  |
| Checkov  | 모든 체크 실패                     | 전부 (skip 제외)         | `.checkov.yaml`                              |
| ClamAV   | 모든 멀웨어 탐지                   | 이진 pass/fail           | 없음                                         |

### 억제(Suppression) 방법 요약

| Scanner  | 인라인 코멘트               | 설정 레벨                       | 베이스라인/차분             |
| -------- | --------------------------- | ------------------------------- | --------------------------- |
| Bandit   | `# nosec BXXX`              | `.bandit` `exclude`             | —                           |
| Semgrep  | `# nosemgrep: rule-id`      | `.semgrepignore`                | PR에서 `--baseline-commit`  |
| Grype    | _(해당 없음 — SCA)_         | `.grype.yaml` `ignore`          | —                           |
| Gitleaks | —                           | `.gitleaks.toml` `allowlist`    | `.gitleaks-baseline.json`   |
| Checkov  | `# checkov:skip=ID:reason`  | `.checkov.yaml` `skip-check`    | —                           |
| ClamAV   | —                           | —                               | —                           |

## GitHub Actions를 로컬에서 실행하기

_참고: 이는 [`act`](https://github.com/nektos/act) 도구를 사용하며 "us-east-1"의 유효한 AWS CodeBuild 프로젝트 `codebuild-project`에 접근할 수 있다고 가정합니다._

```shell
act --platform ubuntu-latest=-self-hosted \
    --job build \
    --workflows .github/workflows/codebuild.yml \
    --env-file .env \
    --var CODEBUILD_PROJECT_NAME=codebuild-project \
    --var AWS_REGION=us-east-1 \
    --var ROLE_DURATION_SECONDS=7200 \
    --artifact-server-path=$PWD/.codebuild/artifacts \
    --cache-server-path=$PWD/.codebuild/artifacts \
    --env ACT_CODEBUILD_DIR=$PWD/.codebuild/downloads \
    --bind
```
