# 기여 가이드라인

AI-DLC에 기여하는 데 관심을 가져주셔서 감사합니다. 버그 리포트, 새 규칙 제안, 수정, 또는 문서 개선 등 무엇이든 커뮤니티의 피드백과 기여를 소중하게 생각합니다.

이슈나 pull request를 제출하기 전에 이 문서를 한 번 읽어주세요.

## Tenets

기여하기 전에 우리의 [tenets](README.md#tenets)을 먼저 숙지하세요.

## 규칙 기여하기

AI-DLC 규칙들은 `aidlc-rules/aws-aidlc-rule-details/` 아래에 있습니다. 기여 시 다음을 지켜주세요:

- **재현 가능해야 합니다**: 변경 사항은 테스트 케이스 또는 일련의 단계를 통해 일관되게 재현 가능해야 합니다.
- **단일 진실 공급원(single source of truth)**: 콘텐츠를 중복시키지 마세요. 여러 stage에 적용되는 지침이라면 `common/`에 두고 그곳을 참조하세요.
- **도구 중립적으로 유지하세요**: 핵심 방법론은 특정 IDE, agent, 모델을 전제하지 않아야 합니다. 도구별 파일은 소스로부터 생성됩니다.

### 디렉토리 구조 — 이름 변경/이동 금지

`aidlc-rules/` 아래의 `aws-aidlc-rules/`와 `aws-aidlc-rule-details/` 폴더 이름은 공개 계약(public contract)의 일부입니다. 워크숍, 테스트, 그리고 `core-workflow.md`의 경로 해석 로직이 모두 이 정확한 이름에 의존하고 있습니다. 평탄화하거나 이름을 바꾸거나 재구성하지 마세요.

```text
aidlc-rules/
├── aws-aidlc-rules/            # 핵심 workflow 진입점
│   └── core-workflow.md
└── aws-aidlc-rule-details/     # workflow가 참조하는 상세 규칙들
    ├── common/
    ├── inception/
    ├── construction/
    ├── extensions/
    └── operations/
```

### 규칙 구조

규칙들은 phase 단위로 정리되어 있습니다:

- `common/` - 모든 phase에 공유되는 지침
- `inception/` - 기획과 아키텍처 규칙
- `construction/` - 설계와 구현 규칙
- `operations/` - 배포와 모니터링 규칙
- `extensions/` - 선택적 cross-cutting 제약 규칙

### 변경 사항 테스트

규칙 변경 사항을 제출하기 전에 지원되는 플랫폼(Amazon Q Developer, Kiro 또는 다른 도구) 중 최소 하나에서 테스트하세요. PR에 어떤 것을 테스트했는지 명시해 주세요.

설치 지침을 추가하거나 업데이트하는 경우에는 Mac, Windows CMD, Windows PowerShell에서 모두 테스트했는지 확인하세요.

## 버그 신고/기능 요청

GitHub 이슈를 사용해 버그를 신고하거나 기능을 제안하세요. 등록하기 전에 중복을 피하기 위해 기존 이슈를 확인하세요.

다음 내용을 포함해 주세요:

- 영향 받는 규칙 또는 stage
- 예상 동작과 실제 동작
- 테스트에 사용한 플랫폼/모델

## Pull Request로 기여하기

### 이슈로 먼저 시작하세요

PR 작업을 시작하기 전에 이슈를 먼저 여는 것을 권장합니다. 이렇게 하면 어떤 것을 염두에 두고 있는지 우리와 커뮤니티가 이해하고, 접근 방법을 논의하고, 코드 작성에 시간을 투자하기 전에 범위를 맞출 수 있습니다. 오타나 lint 수정 같은 작은 변경이라면 PR로 바로 가도 괜찮습니다.

### AI가 생성한 기여

AI 코딩 agent가 만든 PR도 환영하며 동일한 절차를 따릅니다. 이슈로 시작하고, 범위를 맞추고, 품질 기준을 충족하세요.

### PR 제출

1. 최신 `main` 브랜치를 기준으로 작업합니다
2. 열려 있는 PR과 최근에 머지된 PR을 확인합니다
3. 저장소를 fork합니다
4. 변경 사항을 작성합니다 (집중된 범위로 유지)
5. [conventional commits](https://www.conventionalcommits.org/)를 따라 명확한 커밋 메시지를 사용합니다 (예: `feat:`, `fix:`, `docs:`)
6. PR을 제출하고 피드백에 응답합니다

### PR 종료

모든 PR을 리뷰하며 기여가 머지될 수 있도록 돕고자 합니다. 프로젝트 품질을 유지하기 위해 범위에서 벗어나거나 여기에 설명된 가이드라인을 따르지 않는 PR은 닫힐 수 있습니다. 그런 일이 생기더라도 언제든 이슈를 열고 다시 시도하시는 것을 환영합니다.

## 행동 강령

이 프로젝트는 [Amazon Open Source Code of Conduct](https://aws.github.io/code-of-conduct)를 채택했습니다.

자세한 내용은 [Code of Conduct FAQ](https://aws.github.io/code-of-conduct-faq)를 참고하거나 추가 질문/의견이 있으면 <opensource-codeofconduct@amazon.com>으로 문의하세요.

## 보안 이슈 알림

잠재적인 보안 이슈를 발견했다면 [vulnerability reporting page](http://aws.amazon.com/security/vulnerability-reporting/)를 통해 AWS/Amazon Security에 알려주세요. 공개 GitHub 이슈를 만들지 말아주세요.

## 라이선스

프로젝트의 라이선스는 [LICENSE](LICENSE) 파일을 참고하세요. 기여자에게는 기여물의 라이선스 확인을 요청하게 됩니다.
