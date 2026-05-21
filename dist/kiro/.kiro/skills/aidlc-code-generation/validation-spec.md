# Code Generation — Validation Spec

## 입력

- Artifacts: `code-generation-plan.md`, `CODE_SUMMARY.md`, workspace 내의 생성된 소스 코드 및 테스트
- Answered question file: `code-generation-questions.md`
- Upstream: `components.md`, `component-methods.md`, `component-dependencies.md`, `services.md`, `cross-cutting.md`, `data-models.md` (if present), `api-contracts.md` (if present), `stories.md`, `units-of-work.md`, `units-of-work-story-map.md`
- Upstream (if present): `business-logic-model.md`, `domain-entities.md`, `business-rules.md`, `nfr-requirements.md`, `tech-stack-decisions.md`

## 규칙

1. `code-generation-plan.md`가 사용자에 의해 승인되기 전까지 어떠한 코드도 생성될 수 없습니다.
2. 코드 생성은 layer별로 진행되어야 합니다. Layer N+1은 Layer N이 컴파일되고 해당 테스트가 통과하기 전까지 시작될 수 없습니다.
3. 각 layer는 최대 12개 파일을 포함할 수 있습니다. 5–8개를 권장합니다.
4. 단위 테스트는 그것이 테스트하는 코드와 동일한 layer 내에서 생성되어야 합니다 — 이후 layer나 stage로 미루어서는 안 됩니다.
5. 컴파일 실패 시: 최대 3회까지 self-correct 합니다. 로직/테스트 실패 시: 중단하고 사용자에게 제시합니다.
6. 애플리케이션 코드는 workspace 루트에 위치합니다. 문서 artifact는 해당 stage의 aidlc-docs 폴더에 위치합니다. 절대 섞어서는 안 됩니다.
7. brownfield의 경우: 코드를 생성하기 전에 기존 파일에서 코딩 컨벤션을 추출해야 합니다. 생성된 코드는 추출된 컨벤션을 따라야 합니다.
8. brownfield의 경우: 어떠한 기존 파일도 diff 요약을 제시하고 사용자의 승인을 받지 않은 채로 수정되어서는 안 됩니다.
9. 생성된 모든 파일은 `components.md`의 최소 하나의 컴포넌트와 `stories.md`의 최소 하나의 story로 추적 가능해야 합니다.
10. 재호출 시: 첫 번째 미확인 layer부터 재개합니다. ✅로 표시된 layer는 디스크에서 파일이 누락된 경우가 아니라면 재생성하지 않습니다.
11. 각 layer 체크포인트는 다음을 요구합니다: 모든 파일이 디스크에 존재, 빌드 통과, layer별 테스트 통과.
12. 생성된 코드는 `cross-cutting.md`의 패턴(오류 처리, 로깅, 검증)을 구현해야 합니다 — 새로운 패턴을 임의로 만들어서는 안 됩니다.
