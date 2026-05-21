# Changelog

이 프로젝트의 모든 주요 변경 사항은 이 파일에 기록됩니다.

## [0.1.8] - 2026-04-20

### Bug Fixes

- #172 머지 과정에서 누락된 PR head 브랜치 감지 로직 복원 (#173)
- tag-on-merge 워크플로우의 태그 생성 프로세스 수정 (#174)
- CodeBuild 액션 버전 업데이트 및 트리거 추가 (#175)
- fork에서는 CodeBuild 건너뛰기 (#178)
- 사용자의 대화 언어로 extension opt-in 프롬프트 표시 (#177)
- README 사소한 업데이트 (#192)

### CI/CD

- markdownlint 인프라 추가 (#159)

### Features

- 트렌드 리포트 요약을 PR 코멘트로 게시 (#172)
- 보안 스캐너 워크플로우 추가 (#161)
- 에이전트 주도 셋업 — 수동 단계 제거 (#109)

### Miscellaneous

- /scripts/aidlc-evaluator의 cryptography 버전 업 (#179)
- /scripts/aidlc-evaluator의 pytest 버전 업 (#184)
- /scripts/aidlc-evaluator의 pillow 버전 업 (#183)
- 워크플로우의 CodeQL 액션 버전 수정 (#191)
- /scripts/aidlc-evaluator의 python-multipart 버전 업 (#186)

## [0.1.7] - 2026-04-02

### Bug Fixes

- 필수 환경 변수인 GitHub 토큰 추가 (#137)
- 보안 extension 디스클레이머 추가 (#134)
- 릴리즈 워크플로우의 에러 처리 및 PR 생성 로직 리팩터링 (#140)
- 릴리즈 워크플로우 관련 PR #140 리뷰 피드백 반영 (#141)
- CodeBuild 워크플로우 아티팩트에서 retention-days 제한 제거 (#149)
- 읽기 전용 GITHUB_TOKEN을 사용하는 fork PR에서는 PR 코멘트 단계 건너뛰기 (#154)
- label-reminder 코멘트 삭제용 GitHub API 경로 정정 (#157)
- report-bundle CodeBuild secondary artifact 제거 및 --local-run-dir 지원 추가 (#162)
- rules-ref에 merge ref 대신 PR head 브랜치 사용 (#168)
- CodeBuild 트리거를 위해 릴리즈 PR에서 aidlc-rules/VERSION 작성 (#169)

### Documentation

- CodeBuild 로컬 실행을 위한 개발자 가이드 추가 (#94)
- working-with-aidlc 인터랙션 가이드 및 writing-inputs 문서 추가 (#121)
- 종합적인 문서 리뷰 및 보완 (#113)

### Features

- 코드 오너 추가 (#112)
- draft 릴리즈에 빌드 아티팩트를 포함한 changelog-first 릴리즈 플로우 (#125)
- AIDLC Evaluation & Reporting Framework 추가 (#115)
- pull request 린트 조건 업데이트 (#131)
- 크로스 릴리즈 트렌드 리포팅 패키지 추가 (#136)
- 현재 evaluator CLI에 맞춰 CodeBuild 워크플로우 정렬 및 트렌드 리포트 파이프라인 추가 (#147)
- 'codebuild' 라벨 + aidlc-rules 경로 조건으로 CodeBuild 게이트 적용 (#150)
- aidlc-rules/를 건드리는 PR에 codebuild 라벨 자동 부여 (#158)

### Miscellaneous

- /scripts/aidlc-evaluator의 pyjwt 버전 업 (#129)
- /scripts/aidlc-evaluator의 pillow 버전 업 (#130)
- /scripts/aidlc-evaluator의 requests 버전 업 (#146)
- /scripts/aidlc-evaluator의 cryptography 버전 업 (#148)
- /scripts/aidlc-evaluator의 pygments 버전 업 (#151)
- /scripts/aidlc-evaluator의 aiohttp 버전 업 (#163)

## [0.1.6] - 2026-03-05

### Bug Fixes

- CodeBuild 캐시 및 다운로드 수정 (#93)
- error-handling.md의 복사/붙여넣기 오류 정정 (#96)

### Features

- CodeBuild 워크플로우 추가 (#92)

### Miscellaneous

- GitHub 이슈 템플릿 추가 (#97)

## [0.1.4] - 2026-02-24

### Bug Fixes

- GitHub Copilot 지시사항과 Kiro CLI rule-details 경로 해석 수정 (#82, #84) (#87)

## [0.1.3] - 2026-02-11

### Bug Fixes

- 감사(audit) 타임스탬프에 실제 시스템 시간 사용 필수화 (#56)

### Documentation

- ZIP 다운로드 위치 명확화 및 노트 통합 (#70)

## [0.1.2] - 2026-02-08

### Bug Fixes

- core-workflow.md의 오타 수정
- 규칙명 변경 및 Critical Rules 섹션 하단으로 이동

### Documentation

- 사용자가 GitHub Releases를 이용하도록 README 업데이트 (#61)
- Windows CMD 셋업 안내 및 ZIP 노트 추가 (#68)

### Features

- 테스트 자동화 친화적 코드 생성 규칙 추가
- Construction 단계에 프론트엔드 디자인 커버리지 추가

## [0.1.1] - 2026-01-22

### Features

- Claude, OpenCode 등 IDE와 함께 동작하는 AIDLC 스킬 추가
- addin
- leo 파일 추가

### Miscellaneous

- 잘못된 파일 제거
- 잘못된 파일 제거

## [0.1.0] - 2026-01-22

### Features

- Kiro CLI 지원 및 멀티 플랫폼 아키텍처 추가

