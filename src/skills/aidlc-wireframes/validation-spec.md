# Wireframes — Validation Spec

## 입력

- Artifacts (markdown): `screen-data-map.md`, `screen-structure.md`, `wireframe-guidance.md`
- Artifacts (visual): SVG 또는 HTML 파일을 포함하는 `wireframes/` 디렉터리
- Answered question file: `wireframes-questions.md`
- Upstream: `stories.md`, `personas.md`, `requirements.md`

## 규칙

1. 세 개의 markdown artifact(`screen-data-map.md`, `screen-structure.md`, `wireframe-guidance.md`) 모두가 존재해야 하며 비어 있지 않아야 합니다.
2. `wireframes/` 디렉터리가 존재해야 하며, `screen-structure.md`에 나열된 각 화면당 최소 하나의 visual wireframe 파일(SVG 또는 HTML)을 포함해야 합니다.
3. `screen-data-map.md`의 모든 화면은 "Source stories" 필드를 통해 `stories.md`의 최소 하나의 story로 추적 가능해야 합니다. story로 추적할 수 없는 화면은 사유와 함께 플래그 처리해야 합니다.
4. `stories.md`의 모든 UI-facing story는 `screen-data-map.md`의 최소 하나의 화면으로 다루어져야 합니다. 다루지 않은 UI story는 사유와 함께 플래그 처리해야 합니다.
5. `screen-data-map.md`에 나열된 모든 화면은 `screen-structure.md`(inventory 및 navigation map)에 등장해야 합니다. orphan 화면은 허용되지 않습니다.
6. `screen-structure.md`의 navigation map은 일관되어야 합니다 — 모든 navigation 링크는 inventory에 존재하는 화면을 참조해야 합니다. dead link는 허용되지 않습니다.
7. `wireframe-guidance.md`는 `screen-structure.md`의 모든 화면에 대해 항목을 가져야 합니다. 각 항목은 element 배치, 인터랙션 동작, responsive 적응(또는 responsive가 적용되지 않음을 명시)을 포함해야 합니다.
8. wireframe은 `stories.md` 또는 `requirements.md`로 추적 가능한 범위를 넘어서는 기능을 도입해서는 안 됩니다. 어떤 story로도 커버되지 않는 데이터나 동작이 화면에 표시된다면 갭으로 플래그 처리해야 하며, 조용히 추가되어서는 안 됩니다.
9. brownfield의 경우: wireframe은 upstream RE artifact 또는 기존 코드에서 식별된 기존 UI 패턴과 일관되어야 합니다. 기존 패턴에서 벗어난 부분은 근거와 함께 명시적으로 기록해야 합니다.
10. visual wireframe 파일은 well-formed 해야 합니다(유효한 SVG 또는 유효한 HTML). 빈 파일이나 placeholder만 있는 파일은 허용되지 않습니다.
11. `screen-data-map.md`의 "Data displayed"와 "Data submitted" 필드는 upstream `requirements.md`와 일관된 logical type을 사용해야 합니다 — upstream에 근거 없이 임의로 만들어낸 데이터 필드는 허용되지 않습니다.
12. `screen-structure.md`에서 식별된 공유 컴포넌트는 이를 참조하는 화면 전반에 걸쳐 일관되게 등장해야 합니다 — 동일한 공유 컴포넌트에 대한 모순된 정의는 허용되지 않습니다.
