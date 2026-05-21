# 풀 리퀘스트 규약(Pull Request Conventions)

이 리포지토리에서 풀 리퀘스트를 생성할 때는 PR 본문에 항상 다음 사항을 포함해야 합니다.

1. `.github/pull_request_template.md`의 구조를 사용하십시오
2. PR 설명은 반드시 다음 contributor statement(기여자 동의 문구)로 끝나야 합니다 (CI에서 필수로 요구됩니다):

```text
By submitting this pull request, I confirm that you can use, modify, copy, and redistribute this contribution, under the terms of the [project license](https://github.com/awslabs/aidlc-workflows/blob/main/LICENSE).
```

이 문구가 없으면 `Require Contributor Statement` CI 체크가 실패합니다.
