---
name: aidlc-wireframes
description: |
  AI-DLC wireframes skill. 승인된 user story와 persona로부터 시각적 wireframe(SVG 또는 HTML)과 구조화된 markdown artifact(screen-data-map, screen-structure, wireframe-guidance)를 생성합니다. 출력물은 사람의 검토와 application-design 및 code-generation의 downstream 기계 소비 양쪽 모두에 사용됩니다.

  intent에 UI 대상 story가 포함된 workflow에만 포함됩니다. story와 persona가 이미 존재하고 이 단계만 필요할 때 `/skill aidlc-wireframes`로 명시적으로 호출하십시오. 신규 개발 intent의 경우 대신 `aidlc-orchestrator`를 활성화하십시오.
metadata:
  phase: inception
  stage: wireframes
  per-unit: "false"
  human-clarification: "true"
  plan-creation: "true"
  plan-verification: "true"
  artefact-verification: "true"
---

# Wireframes

승인된 user story와 persona를 기반으로 시스템의 UI 표면 — screen, layout, navigation, interaction flow — 을 설계합니다. 사람의 검토용 시각적 wireframe과 downstream skill들의 기계 소비용 구조화된 markdown 두 가지를 모두 생성합니다.

이 skill은 intent에 UI 대상 story가 포함된 경우에만 실행됩니다. 순수 backend/infrastructure intent의 경우 전적으로 건너뜁니다.

## Prerequisites

- `stories.md`와 `personas.md`가 승인되어 있어야 합니다
- `requirements.md`가 사용 가능해야 합니다
- brownfield의 경우: UI 패턴 추출을 위해 RE artifact(`code-structure.md`, `technology-stack.md`) 또는 기존 소스 코드가 사용 가능해야 합니다

## Input

- `stories.md`, `personas.md`
- `requirements.md`
- brownfield의 경우: `technology-stack.md`, `code-structure.md`, 기존 UI 소스 파일

## Question Guidance

clarification 질문은 다음에 집중하십시오:

- **Output format** — SVG wireframe인가, HTML prototype인가? (정적 layout 검토에는 SVG, 인터랙티브 flow 검토에는 HTML)
- **Design system / stylesheet** — 기존 CSS framework, component library, 또는 design token 세트가 있는가? 있다면 wireframe은 그 어휘를 참조해야 합니다(예: "그림자 있는 둥근 박스" 대신 "Card component 사용").
- **Brand guidance** — 색상, 타이포그래피, 로고, skin/theme. 사용할 수 없다면 brand guideline을 org-ai-kb(예: `org-ai-kb/design-system/brand-guidelines.md`)에 추가하도록 사람을 안내하십시오. wireframe은 중립적 스타일로 진행될 수 있지만 brand 결정이 보류된 지점은 표기해야 합니다.
- **Language and locale** — UI label과 copy의 주 언어. UI가 다국어인가? RTL 지원이 필요한가?
- **Responsive requirements** — mobile-first, desktop-only, 또는 adaptive? 주요 breakpoint는?
- **Accessibility target** — WCAG 수준(AA, AAA)? 특정 보조 기술 고려사항?
- **Navigation model** — tab, sidebar, breadcrumb, wizard flow, 또는 하이브리드? 화면들이 어떻게 연결되는가?
- **Interaction density** — 데이터가 많은 dashboard, 단순 폼 flow, 콘텐츠 사이트, 또는 혼합? 이것이 layout 패턴을 좌우합니다.
- **State representations** — wireframe이 loading state, empty state, error state까지 다뤄야 하는가, 아니면 happy path만 다루는가?
- **기존 UI 패턴**(brownfield) — 일치시켜야 할 기존 화면이 있는가? 새 화면이 기존 것과 일관된 느낌이어야 하는가? 접근 가능한 경우 기존 코드에서 패턴을 추출하십시오.
- **누락된 story coverage** — 어떤 화면이 `stories.md`의 어떤 story도 다루지 않는 데이터나 동작을 필요로 한다면, clarification 질문으로 제기하십시오: "Screen X는 [capability]를 필요로 합니다. 이를 다루는 story가 없습니다. user-stories로 돌아가서 coverage를 추가할까요, 아니면 범위 밖인가요?"

backend 아키텍처, API design, component 경계에 대해서는 묻지 마십시오 — 그것들은 application-design에 속합니다. wireframe은 사용자가 보고 행하는 것을 정의하고, backend는 그것을 제공합니다.

## Output

### 기계 판독 가능 artifact (markdown)

#### screen-data-map.md

각 screen/view별로:
- **Screen name**
- **Purpose** — 사용자가 여기에서 무엇을 달성하는가
- **Data displayed** — 사용자에게 보이는 필드/값, 논리적 타입 포함
- **Data submitted** — 사용자가 입력하거나 선택하는 필드/값
- **Actions** — 각 상호작용(버튼 클릭, 폼 제출, 탐색)에서 일어나는 일
- **Source stories** — 이 화면이 제공하는 `S-<n>` ID들
- **Source components** — 이 데이터를 제공할 component/service(story로부터 추론하되 단정하지 않음 — 최종 결정은 application-design이 합니다)

#### screen-structure.md

시스템 전반의 screen 아키텍처:
- **Screen inventory** — 계층(부모/자식 관계) 포함 전체 screen 목록
- **Navigation map** — screen들이 어떻게 연결되는지(link, tab, 뒤로가기, deep link)
- **Component tree per screen** — layout 계층(header → nav → main → sidebar → footer), 명명된 UI 영역과 그 목적
- **Shared components** — 여러 screen에 걸쳐 나타나는 UI 요소(nav bar, footer, notification 영역, modal)
- **Screen groups** — 논리적 그룹화(예: "onboarding flow", "settings pages", "dashboard views")

#### wireframe-guidance.md

code-generation이 wireframe을 재현할 수 있도록 하는 단계별 지침:
- **화면별:** 요소 배치(위에서 아래로, 왼쪽에서 오른쪽으로), 크기 비율, 간격 관계, 정렬 규칙
- **Interaction 동작:** hover, click, focus, submit 시 일어나는 일 — 상태 전이로 기술
- **Responsive adaptation:** 각 breakpoint에서 layout이 어떻게 바뀌는가
- **조건부 렌더링:** 사용자 role, 데이터 상태, 또는 feature flag에 따라 무엇이 표시/숨김되는가
- **Animation/transition 노트:** 모션 디자인(page transition, loading indicator, micro-interaction)

### 시각 artifact (사람 검토용)

#### wireframes/ (디렉터리)

사람이 선택한 형식으로 screen당 한 파일:
- `wireframes/<screen-name>.svg` — SVG를 선택한 경우
- `wireframes/<screen-name>.html` — HTML을 선택한 경우

각 wireframe은 다음을 표시합니다:
- 영역 라벨이 표시된 layout 구조
- placeholder 콘텐츠(현실적이지만 최종 카피는 아님)
- 명확히 표시된 인터랙티브 요소(버튼, link, 입력, dropdown)
- 정적 layout에서 보이지 않는 동작에 대한 주석

## Downstream Consumption

- **application-design**은 UI가 어떤 데이터를 필요로 하는지 이해하기 위해 `screen-data-map.md`를 읽습니다. 이는 BFF/aggregation 질문을 드러냅니다 — 화면이 여러 backend component에서 데이터를 끌어온다면, application-design은 BFF를 도입할지 또는 frontend가 service를 직접 호출할지를 사람에게 묻습니다.
- **code-generation**은 frontend 레이어 생성의 주요 입력으로 세 markdown 파일(`screen-data-map.md`, `screen-structure.md`, `wireframe-guidance.md`)을 모두 읽습니다. 시각 wireframe 파일은 layout 참조로 사용됩니다.
- 사람은 UX 승인을 위해 시각적 wireframe을 검토합니다.

## Validation

이 skill 출력에 대한 validation 규칙은 skill 루트의 `validation-spec.md`에 있습니다. 적용 방식은 `aidlc-common/protocols/aidlc-validator-protocol.md`를 참조하십시오.
