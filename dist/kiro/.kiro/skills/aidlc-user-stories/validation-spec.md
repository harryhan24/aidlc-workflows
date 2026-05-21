# User Stories — Validation Spec

## 입력

- Artifacts: `personas.md`, `stories.md`
- Answered question file: `user-stories-questions.md`
- Upstream: `requirements.md`

## 규칙

1. `personas.md`와 `stories.md`가 모두 존재해야 합니다. 사람 persona가 없다면 `personas.md`가 이를 명시적으로 기재해야 합니다.
2. 모든 story는 INVEST 기준을 따라야 하며, 검증 가능한 pass/fail acceptance criteria를 가져야 합니다.
3. 모든 story는 고유한 `S-<n>` ID와, 해당 story가 다루는 FR/NFR 식별자를 나열한 `Requirements:` 라인을 가져야 합니다.
4. `requirements.md`의 모든 FR과 NFR은 최소 하나의 story로 다루어져야 합니다. 커버되지 않는 요구사항은 사유와 함께 명시적으로 문서화되어야 합니다.
5. story는 `requirements.md`가 함의하는 모든 시스템 layer를 커버해야 합니다 — 사용자 대면 동작만이 아니어야 합니다.
6. persona는 도메인과 요구사항에 근거해야 합니다 — 일반적인 archetype이 아니어야 합니다.
7. 어떤 두 story도 동일한 동작을 기술해서는 안 됩니다. 중복은 통합되어야 합니다.
