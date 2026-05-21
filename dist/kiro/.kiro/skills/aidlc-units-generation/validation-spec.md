# Units Generation — Validation Spec

## 입력

- Artifacts: `units-of-work.md`, `units-of-work-dependency.md`, `units-of-work-story-map.md`
- Answered question file: `units-generation-questions.md`
- Upstream: `requirements.md`, `stories.md`, `components.md`, `component-methods.md`, `component-dependencies.md`, `services.md`, `cross-cutting.md`
- Upstream (if present): `screen-structure.md`

## 규칙

1. 세 artifact(`units-of-work.md`, `units-of-work-dependency.md`, `units-of-work-story-map.md`) 모두가 존재해야 하며 비어 있지 않아야 합니다.
2. `components.md`의 모든 컴포넌트는 `units-of-work.md`에서 정확히 하나의 unit에 할당되어야 합니다. 어떤 컴포넌트도 미할당되거나 여러 unit에 할당되어서는 안 됩니다.
3. `stories.md`의 모든 story는 `units-of-work-story-map.md`에 정확히 하나의 primary unit과 함께 등장해야 합니다. 어떤 story도 미할당되거나 여러 primary unit을 가져서는 안 됩니다.
4. `units-of-work.md`의 모든 unit은 최소 하나의 컴포넌트와 (primary owner로서) 최소 하나의 story를 할당받아야 합니다.
5. `units-of-work-dependency.md`의 모든 의존성은 `units-of-work.md`에 정의된 unit을 참조해야 합니다. 정의되지 않은 unit에 대한 참조는 허용되지 않습니다.
6. unit 간 순환 의존성은 명시적인 정당화와 함께 나열되어야 합니다. 정당화되지 않은 순환 의존성은 실패입니다.
7. 분해(decomposition)는 `components.md`, `services.md`, `stories.md`로 추적 가능한 범위를 넘어서는 unit을 도입해서는 안 됩니다. 갭은 발견사항(finding)으로 보고하며, 조용히 추가되어서는 안 됩니다.
8. 각 unit은 질문 답변에서 논의된 배포 모델과 일관된 명확한 type 지정(service 또는 module)을 가져야 합니다.
9. `units-of-work-story-map.md`의 story 할당은 `units-of-work.md`의 Stories 필드와 일관되어야 합니다. `units-of-work.md`에서 어떤 unit 아래 나열된 모든 story는 `units-of-work-story-map.md`에서 해당 unit을 primary 또는 contributing으로 가져야 하며, 그 역도 마찬가지입니다.
10. contributing unit이 있는 모든 story는 여러 unit에 걸치는 이유를 설명하는 근거를 가져야 합니다. 근거 없는 multi-unit 할당은 실패입니다.
11. cross-cutting 컴포넌트(`cross-cutting.md` 출처)는 unit에 할당되어야 합니다. 전용 cross-cutting/platform unit이 존재한다면 upstream unit 의존성이 없어야 합니다(다른 unit이 이를 의존하며, 그 반대는 아닙니다).
