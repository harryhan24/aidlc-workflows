# 기여 가이드라인

AI-DLC에 관심을 가져 주셔서 감사합니다. 버그 리포트, 새로운 규칙 추가, 수정, 문서 개선 등 어떤 형태의 기여든 커뮤니티의 피드백을 소중하게 생각합니다.

이슈나 풀 리퀘스트를 제출하기 전에 이 문서를 먼저 읽어 주시기 바랍니다.

## 핵심 원칙(Tenets)

기여하기 전에 [tenets](README.md#tenets)를 먼저 숙지해 주십시오.

## 규칙(Rule) 기여하기

AI-DLC 규칙은 `aidlc-rules/aws-aidlc-rule-details/`에 위치합니다. 기여 시 다음 원칙을 지켜 주십시오.

- **재현 가능성을 유지하십시오(Be reproducible)**: 변경 사항은 테스트 케이스 또는 단계별 절차를 통해 일관되게 재현 가능해야 합니다.
- **단일 진실 공급원(Single source of truth)**: 콘텐츠를 중복하지 마십시오. 여러 단계에 적용되는 가이드라면 `common/`에 두고 참조하도록 하십시오.
- **도구에 종속되지 않도록 하십시오(Keep it agnostic)**: 핵심 방법론은 특정 IDE, 에이전트, 모델을 전제하지 않아야 합니다. 도구 전용 파일은 원본으로부터 생성됩니다.

### 디렉터리 구조 — 이름 변경/이동 금지

`aidlc-rules/` 하위의 `aws-aidlc-rules/`와 `aws-aidlc-rule-details/`라는 폴더명은 공개 계약(public contract)의 일부입니다. 워크숍, 테스트, `core-workflow.md`의 경로 해석 로직 모두가 정확히 이 이름에 의존합니다. 평탄화하거나 이름을 바꾸거나 재구성하지 마십시오.

```text
aidlc-rules/
├── aws-aidlc-rules/            # Core workflow entry point
│   └── core-workflow.md
└── aws-aidlc-rule-details/     # Detailed rules referenced by the workflow
    ├── common/
    ├── inception/
    ├── construction/
    ├── extensions/
    └── operations/
```

### 규칙 구조

규칙은 단계(phase)별로 구성되어 있습니다.

- `common/` - 모든 단계에서 공유되는 가이던스
- `inception/` - 계획 및 아키텍처 규칙
- `construction/` - 설계 및 구현 규칙
- `operations/` - 배포 및 모니터링 규칙
- `extensions/` - 선택적 cross-cutting 제약 규칙

### 변경 사항 테스트

PR을 제출하기 전에 최소 하나 이상의 지원 플랫폼(Amazon Q Developer, Kiro 등)에서 규칙 변경 사항을 테스트하십시오. 테스트한 내용을 PR에 명시하십시오.

설치 안내를 추가하거나 업데이트하는 경우, Mac, Windows CMD, Windows Powershell 환경에서 모두 테스트했는지 확인하십시오.

## 버그 리포트/기능 요청

GitHub 이슈를 통해 버그를 신고하거나 기능을 제안하십시오. 등록 전에 기존 이슈를 확인하여 중복을 피해 주십시오.

다음 정보를 포함해 주십시오.

- 영향받는 규칙 또는 단계
- 예상 동작과 실제 동작의 차이
- 테스트에 사용한 플랫폼/모델

## 풀 리퀘스트로 기여하기

### 이슈에서 시작하기

PR 작업 전에 이슈를 먼저 여는 것을 권장합니다. 이를 통해 메인테이너와 커뮤니티가 의도를 파악하고, 접근 방식을 논의하며, 코드 작성에 시간을 투입하기 전에 범위를 정렬할 수 있습니다. 오타나 린트 수정 같은 작은 수정이라면 곧바로 PR로 진행해도 무방합니다.

### AI가 생성한 기여

AI 코딩 에이전트가 만든 PR도 환영하며, 동일한 절차를 따릅니다. 이슈에서 시작하여 범위를 정렬하고 품질 기준을 충족하면 됩니다.

### PR 제출

1. 최신 `main` 브랜치를 기준으로 작업하십시오
2. 기존에 열려 있거나 최근 머지된 PR을 확인하십시오
3. 리포지토리를 fork하십시오
4. 변경 사항을 작성하되 범위를 좁게 유지하십시오
5. [conventional commits](https://www.conventionalcommits.org/)를 따르는 명확한 커밋 메시지를 사용하십시오 (예: `feat:`, `fix:`, `docs:`)
6. PR을 제출하고 피드백에 대응하십시오

### PR 종결

모든 PR을 리뷰하며, 기여가 머지될 수 있도록 돕습니다. 다만 프로젝트 품질 유지를 위해 범위를 벗어나거나 본 가이드라인을 따르지 않는 PR은 종결될 수 있습니다. 그런 경우에도 언제든 이슈를 다시 열고 재시도하시기 바랍니다.

## 행동 강령(Code of Conduct)

이 프로젝트는 [Amazon Open Source Code of Conduct](https://aws.github.io/code-of-conduct)를 따릅니다.

자세한 내용은 [Code of Conduct FAQ](https://aws.github.io/code-of-conduct-faq)를 참고하시거나, 추가 질문 및 코멘트는 <opensource-codeofconduct@amazon.com>으로 문의해 주십시오.

## 보안 이슈 알림

잠재적인 보안 이슈를 발견한 경우 [vulnerability reporting page](http://aws.amazon.com/security/vulnerability-reporting/)를 통해 AWS/Amazon Security에 알려 주십시오. 공개 GitHub 이슈는 생성하지 마십시오.

## 라이선스

본 프로젝트의 라이선스는 [LICENSE](LICENSE) 파일을 참고하십시오. 기여에 대한 라이선스 동의를 확인 요청드릴 수 있습니다.
