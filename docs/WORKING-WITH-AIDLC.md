# AIDLC 활용 가이드 (Working with AIDLC)

이 가이드는 AI-DLC(AI-Driven Development Life Cycle)를 최대한 활용하는 방법을 안내합니다. 첫 프롬프트부터 동작하는 코드까지 — 각 스테이지에서 AI와 효과적으로 상호작용하는 방법을 다룹니다.

각 섹션의 기본 내용부터 시작하세요. 고급 팁은 실제 워크숍 경험에서 도출된 것으로, 팀이 기본을 익힌 뒤 가장 유용하다고 평가한 패턴들을 다룹니다.

---

## 목차

1. [General Rules](#1-general-rules)
2. [Inception Phase](#2-inception-phase)
3. [Construction Phase](#3-construction-phase)
4. [Never Vibe Code](#4-never-vibe-code)

---

## 1. General Rules

### 파일을 변경하지 않고 질문하기

초기에 길러야 할 가장 중요한 습관 중 하나: **모든 질문이 문서 업데이트를 트리거해서는 안 된다**.

가드 없이 AI에게 무언가를 물으면, AI는 그것을 변경 요청으로 해석하여 즉시 설계 문서를 업데이트할 수 있습니다. 이를 막으려면 탐색적 질문에 명확한 no-change 지시를 접두로 붙이세요.

**기본 패턴:**

```text
Do not update any documents. Help me understand why [this decision] was made.
```

```text
Do not update any documents. For [component name], is it reasonable to use [library or technology] here?
```

```text
Do not change anything. Assess the impact of [proposed change].
I want to understand the consequences before we decide.
```

이러한 패턴은 어떤 것도 확정하지 않은 채로 AI와 함께 큰 소리로 생각하고, 옵션을 평가하고, 결정에 도전할 수 있게 해줍니다. 답변에 만족하면 필요한 경우 의도적인 업데이트 지시로 후속 조치하세요.

> **Tip**: 모든 탐색적 메시지를 "Do not update any documents."로 시작하세요. 행동할 준비가 되면 그 제약을 언제든 떼낼 수 있습니다.

---

### Question → Doc → Approval 플로우

AIDLC는 채팅에서 인라인으로 명확화 질문을 던지지 않습니다. 질문을 마크다운 파일에 쓰고, 당신이 그 안에 답변을 채워 넣기를 기다립니다. 이는 모든 결정에 대한 영구적인 기록을 남기고 팀 전체가 기여하기 쉽게 만들어 줍니다.

**Step 1 — AIDLC가 질문 파일을 생성합니다**

AI는 `aidlc-docs/inception/requirements/requirement-verification-questions.md` 같은 파일을 만들고 멈춥니다. 당신이 답변할 때까지 진행하지 않습니다.

**Step 2 — 답변을 채워 넣습니다**

파일을 열고 각 `[Answer]:` 태그를 채웁니다. 질문은 다지선다 형식을 사용합니다.

```markdown
## Question: Deployment model
Where will this service be deployed?

A) AWS Lambda (serverless)

B) AWS ECS Fargate (containerized)

C) Existing on-premises infrastructure

X) Other (please describe after [Answer]: tag below)

[Answer]: B
```

답변할 때 잘 작동하는 몇 가지 방식:

- **글자 옆에 레이블을 붙이세요.** `C — financial summary and debt service coverage`가 단순히 `C`보다 명확합니다.
- **간단한 정당화를 포함하세요.** `A — design-first; generate the OpenAPI spec before writing code`는 의도를 확인하고 AI가 이후 단계로 가져갈 컨텍스트를 제공합니다.
- **둘 다를 의미할 때는 옵션을 조합하세요.** `B and C — rate limiting at both API Gateway level and application level (not D)`는 모호하지 않습니다.
- **옵션이 거의 맞지만 단서를 더해야 할 때는 단서를 다세요.** `B — migration is a separate project; however, include a one-time migration into the new data structures.`
- **X를 자유롭게 사용하세요.** 어떤 옵션도 맞지 않으면, 잘못된 답을 강요하기보다 X가 올바른 선택입니다.

**Step 3 — 답변이 준비되었다고 AI에게 알립니다**

채팅으로 돌아가서 말합니다: "We have answered your clarification questions. Please re-read the file and proceed."

Tip: AI에게 명시적으로 파일을 *다시 읽도록(re-read)* 요청하면 메모리에 있는 (당신의 최신 편집을 반영하지 않을 수 있는) 버전 대신 디스크에서 답변을 로드하도록 보장합니다.

**Step 4 — AIDLC가 검증하고 진행합니다**

AI는 당신의 답변을 읽고, 남아 있는 모호함을 표시하고, 다음 아티팩트를 생성하기 위해 진행합니다.

> **고급 팁**: AI의 질문 일부에 답하는 문서가 있다면, AI가 스스로 해결하도록 지시할 수 있습니다: "Analyze the rationale for each question. If a question has already been answered through the provided documentation, answer it yourself. Only ask me if it is still unclear." 이는 게이트 지점에서 불필요한 왕복을 줄여줍니다.

**승인 게이트**

각 스테이지가 끝날 때 AIDLC는 두 가지 옵션과 함께 완료 메시지를 제시합니다.

- **Request Changes** — 진행하기 전에 수정 요청
- **Approve and Continue** — 출력을 수락하고 진행

승인 전에 생성된 아티팩트를 읽으세요. 필요하면 팀과 논의하세요. 만족할 때만 승인하세요.

---

### 컨텍스트 관리

컨텍스트는 세션 동안 AI의 작업 메모리입니다. AIDLC는 일관된 다운스트림 출력을 생성하기 위해 아티팩트와 지시의 전체 체인을 컨텍스트에 가지고 있는 것에 의존합니다. 이를 잘 관리하는 것은 당신이 개발할 수 있는 가장 레버리지가 높은 습관 중 하나입니다.

**핵심 룰: 모든 자연스러운 결정 지점에서 컨텍스트를 클리어하라.**

AIDLC는 게이트를 중심으로 만들어졌습니다 — AI가 멈추고 당신에게 무언가(답변할 질문 파일, 승인할 문서, 검토할 plan)를 물어보는 순간들. 이러한 일시 정지는 단순한 승인 체크포인트가 아닙니다. 진행하기 전에 새 컨텍스트를 시작하기에 올바른 순간입니다.

게이트에서 컨텍스트를 클리어하는 것은 위험이 낮습니다. AI의 현재 작업이 이미 파일에 저장되어 있기 때문입니다. 다음 컨텍스트는 깨끗하게 시작하여 디스크에서 관련 아티팩트를 로드하고, 이전의 모든 단계에서 누적된 노이즈를 가지고 가지 않습니다.

여러 게이트에 걸쳐 컨텍스트가 누적되도록 두면, AI는 이전 지시와 아티팩트의 압축되거나 부분적으로 손실된 버전에서 작업하게 됩니다. 출력 품질이 미묘하고 진단하기 어려운 방식으로 저하됩니다.

**실전 가이드:**

- AI가 질문 파일에 답변하라고 요청하면 — 질문에 답한 뒤 **새 컨텍스트를 시작**하고 파일을 다시 읽고 계속하라고 AI에게 알리세요
- AI가 승인을 위해 문서를 제시하면 — 검토한 뒤 변경을 요청하거나 승인하고 진행하기 위해 **새 컨텍스트를 시작**하세요
- 도구가 워크플로우 중간에 "compact context" 프롬프트를 제공하면, **항상 거부하세요** — 압축(compaction)은 깨끗한 리셋과 같지 않으며 더 많은 것을 잃습니다

**컨텍스트 리셋 후 재개하는 방법:**

옵션 1 — 상태 파일 방식 (권장):

```text
Go to aidlc-docs/aidlc-state.md, find the first unchecked item,
then go to the corresponding plan file and resume from that point.
```

옵션 2 — 수동 핸드오프:

```text
I am resuming a previously stopped conversation. Here is the context:
[paste summary of last output or recent change]
Please continue with [next action or section X].
```

> **Tip**: 컨텍스트를 리셋할 때마다 현재 변경 사항을 저장소에 커밋하고 푸시하세요. 몇 초밖에 걸리지 않으며 항상 깨끗한 복구 지점을 확보할 수 있습니다.

```text
Please commit and push all current changes to the repository.
```

---

### 프롬프트 배칭(Batching)

모든 프롬프트를 따로 보내야 하는 것은 아닙니다. 워크숍 경험에서 나온 단순한 룰:

**같은 주제에 단단히 결합된 두 변경은 하나의 프롬프트에 포함하세요. 두 변경이 무관하다면 한 번에 하나씩 하세요.**

과도한 배칭(무관한 변경 결합)은 AI가 초점을 잃고 세부 사항을 놓치게 만듭니다. 부족한 배칭(밀접하게 관련된 것들에 대해 별도 프롬프트)은 불필요한 왕복을 늘립니다. 의심스러우면 분리하는 쪽으로 기우세요.

---

### 외부 참조 파일 로딩

스키마, 아키텍처 다이어그램, 데이터 사전, API 스펙 등 기존 문서를 AIDLC에 가리키면, AIDLC는 그 내용을 현재 스테이지에 통합합니다.

**기본 패턴:**

```text
Please read [file path or description]. Use it as the basis for [what you want].
```

```text
We have an existing audit table structure. Please add it to the inception documents
and reference it for this service. When we proceed, expect new requirements and
stories related to this service.
```

> **고급 팁**: 시작 시점뿐만 아니라 어떤 스테이지에서든 문서를 로드할 수 있습니다. Construction 중에 새 제약(업데이트된 보안 정책, 수정된 데이터 모델)이 등장하면, 로드하고 진행 전에 영향 평가를 AIDLC에 요청하세요.
>
> **고급 팁 — 익스텐션으로서의 엔터프라이즈 표준**: 조직에 모든 프로젝트에 적용되어야 하는 보안, 컴플라이언스, API 가이드라인이 있다면, `aidlc-rules/extensions/`에 마크다운 스티어링 파일로 추가하세요. AIDLC가 수동 주입 없이 모든 단계에 자동으로 로드합니다.

---

### 독립적인 비평(Critique) 받기

AIDLC는 자신의 이전 결정을 옹호합니다. 아티팩트에 대한 편향 없는 평가를 원할 때는 **새 컨텍스트**에서 비평을 요청하세요 — AI가 왜 그 결정을 내렸는지에 대한 기억이 없는 컨텍스트.

```text
Produce a critique document of [the requirements document / the component design].
Do this in a new context separate from everything else.
```

이는 아티팩트가 생성된 동일 세션에서 비평을 요청하는 것보다 더 유용하고 객관적인 피드백을 만들어냅니다.

---

### 깊이 레벨(Depth Levels)

AIDLC는 요청의 복잡도에 따라 각 스테이지를 얼마나 깊이 실행할지 조정합니다. 당신이 이에 영향을 줄 수 있습니다.

```text
Keep this at minimal depth — we just need the basic structure documented.
```

```text
This is a production-critical component. Please run at comprehensive depth.
```

---

## 2. Inception Phase

Inception 단계는 설계나 코드 작업이 시작되기 전에 당신과 AI가 *무엇을, 왜 만들지*를 정렬하는 단계입니다. 여기서 더 많은 컨텍스트를 가져올수록 Construction에서 마주칠 명확화 질문과 재작업이 줄어듭니다.

### 시작 전에 입력 자료를 준비하라

AIDLC를 시작하기 전에 할 수 있는 가장 효과적인 한 가지는 두 개의 문서를 준비하는 것입니다.

1. **Vision Document(비전 문서)** — 무엇을 왜 만들 것인가
2. **Technical Environment Document(기술 환경 문서)** — 어떤 도구와 제약이 적용되는가

이 문서들은 AIDLC가 던질 명확화 질문의 수를 극적으로 줄여주고, AI가 가정을 만들기보다 팀의 실제 컨텍스트에서 시작하도록 보장합니다.

**어디서 시작할까:**

- [writing-inputs/inputs-quickstart.md](writing-inputs/inputs-quickstart.md) — 그린필드와 브라운필드 모두에 대한 빠른 요약
- [writing-inputs/vision-document-guide.md](writing-inputs/vision-document-guide.md) — 템플릿을 포함한 전체 비전 가이드
- [writing-inputs/technical-environment-guide.md](writing-inputs/technical-environment-guide.md) — 템플릿을 포함한 전체 기술 환경 가이드

**브라운필드 프로젝트** (기존 코드베이스에 추가)는 약간 다른 입력 자료가 필요합니다. 비전 문서는 현재 상태 설명과 변경되지 말아야 할 것들의 명시적인 리스트가 필요합니다. 기술 환경 문서는 원하는 스택이 아닌 기존 스택을 설명해야 하며, 예제 코드는 실제 기존 파일에서 가져와야 합니다. 브라운필드 최소 입력과 작업 예제는 [writing-inputs/inputs-quickstart.md](writing-inputs/inputs-quickstart.md)를 참고하세요.

빠르게 시작하고 싶다면 **최소 입력**:

비전의 경우: 무엇을 누구를 위해 만들고 있는지 설명하는 한 단락, 스코프에 있는 MVP 기능 리스트, 명시적으로 스코프 밖인 기능 리스트, 그리고 — 당신이 이미 불확실하다고 알고 있는 — 미해결 질문(open questions). 미해결 질문은 사전 선언된 모호함으로서 Requirements Analysis에 직접 공급되어, 설계 중간에 깜짝 등장하기보다 일찍 해결됩니다.

기술 환경의 경우: 언어와 버전, 패키지 매니저, 웹 프레임워크, 클라우드 제공자와 배포 모델, 테스트 프레임워크, 금지된 라이브러리 테이블(각 항목에 대해 이유와 권장 대안), 보안 기본 사항, 그리고 전형적인 엔드포인트/함수/테스트 각각에 대한 최소 하나의 예제.

금지된 라이브러리 테이블은 단순 리스트보다 중요합니다 — 이유와 대안 컬럼은 *왜* 라이브러리가 금지되었는지를 AI-DLC에 알려주어 더 나은 치환 결정을 이끌어냅니다. 예제 코드 패턴은 기본 사항을 넘어 단일로 가장 레버리지가 높은 추가입니다. 코드 생성 중에 AI-DLC가 자체적으로 발명하지 않고 따라야 할 구체적인 패턴을 제공합니다.

> **Tip**: 사전에 채워 넣는 모든 빈틈은 Requirements Analysis 동안 하나 덜 받게 될 명확화 질문입니다.

---

### 새 프로젝트 시작하기(Kicking Off)

입력 문서가 준비되면:

```text
I want to start a new project. Please read [path to vision document] and
[path to technical environment document], then begin the AIDLC workflow.
```

AIDLC는 워크스페이스를 스캔하고, 그린필드인지 브라운필드인지 판단한 후, 당신의 문서를 주 소스로 사용하여 Requirements Analysis로 진행합니다 — 그 문서들이 다루지 않는 것에 대해서만 질문합니다.

브라운필드 프로젝트의 경우, AIDLC는 먼저 Reverse Engineering을 실행하여 기존 코드베이스를 분석하고 아키텍처, 컴포넌트, API 문서를 생성합니다. 이 아티팩트들을 신중하게 검토하세요 — 이후 모든 것의 기반이 됩니다.

---

### Requirements 질문에 답하기

글자 사용, 레이블 추가, 옵션 조합, X로 커스텀 답변하기에 대한 전체 가이드는 [Section 1](#the-question--doc--approval-flow)의 답변 팁을 참고하세요. Requirements Analysis에만 해당하는 몇 가지 추가 사항:

- **전체 비전과 MVP를 명시적으로 분리하세요.** AIDLC가 어떤 기능을 포함할지 물으면 명명하세요. 무언가가 스코프 밖이라면 그렇게 말하세요 — 모호하게 두지 마세요.
- **의도적인 "아니오" 결정을 분명히 명시하세요.** `D — no caching required at this time`는 의도를 전달합니다. 빈 답변은 AI가 추측성 선택을 하도록 초대합니다.
- **단계적 접근(phased approaches)을 인라인으로 설명하세요.** `X — simple role-based workflow now; replace with external workflow engine when available`는 AIDLC가 올바른 확장 지점을 가진 현재 솔루션을 설계하도록 해줍니다.

> **고급 팁 — 보안 익스텐션**: Requirements Analysis 동안 AIDLC는 보안 익스텐션 룰을 강제할지 묻습니다. 프로덕션급 애플리케이션이라면 Yes를 선택하세요. 프로토타입이라면 No도 괜찮습니다. 이 결정은 기록되고 Construction 전체에 강제되므로, 의도적으로 선택하세요.

---

### Inception 단계에서의 상호작용 패턴

**진행 중 기능 연기하기:**

```text
We are going to backlog the [feature name] capability for the current release.
Please remove it from the component design and flag the related user stories as backlogged.
```

백로깅(삭제가 아님)은 작업을 향후 이터레이션을 위해 보존하면서 현재 빌드에 영향을 주지 않도록 합니다.

**기존 데이터 구조 등록하기:**

```text
We have an existing [schema/structure name]. Please add it to the inception documents
and reference it for this service. When we proceed, expect new requirements and
stories related to this service.
```

**암묵적인 데이터 소스를 명시적으로 만들기:**

```text
For the [service name], add the understanding that [new data source] is also a
data source for this feature, in addition to [existing data source]. Then review
requirements and user stories to ensure this is captured.
```

**설계 변경 후 업스트림 영향 확인:**

설계 아티팩트의 의미 있는 변경 후, 이전 문서들이 여전히 일관성 있는지 AIDLC에게 확인하라고 요청하세요.

```text
Now review the previous steps — user stories and requirements — to ensure
this change does not require updates to any of those documents.
```

> **고급 팁 — 상시(standing) 역전파 룰**: 각 변경 후마다 묻기보다 단계 시작에서 상시 지시로 설정하세요: "Every time you update a document, check whether the change impacts the requirements document and user stories, and prompt me if it does." 이는 당신이 기억할 필요 없이 자동 안전망을 만들어 줍니다.

**컴포넌트 설계의 병렬 팀 리뷰:**

팀이 분리되어 여러 컴포넌트를 동시에 리뷰한다면:

```text
Restrict your edits to the files under your team's control. When all teams are done,
we will ask the AI to review all changes and confirm there are no conflicts.
Then we will ask it to review impacts to user stories and requirements.
```

모두 끝나면 충돌 확인을 트리거하세요.

```text
We had [N] independent groups editing component design files. Please review all files
and report any conflicts or inconsistencies. Do not edit the files — produce a report
for our review.
```

각 충돌을 번호로 명시적으로 해결하세요.

```text
For conflict #[number] ([conflict description]):
update [target file] to reflect [your decision].
```

```text
For conflict #[number] ([capability name]):
this capability is backlogged. Update the documentation to clearly mark it as
backlogged so code generation does not attempt to implement it.
```

**오래된 설계 파일 아카이빙:**

설계 중 탐색으로 더 이상 필요 없는 파일이 생긴 경우:

```text
Move the [file descriptions] to an archive folder — do not delete them.
Then confirm whether they are required for code generation.
```

> **고급 팁 — 컴포넌트 크기 제약**: 단일 스프린트에서 구현하기에 너무 큰 컴포넌트를 막고 싶다면, Application Design 동안 story-point 캡을 설정하세요: "At the component design phase, inject the following instruction: no single component should have more than [X] aggregate story points. If a component exceeds this limit, break it down into smaller sub-components."
>
> **고급 팁 — 단계 중간의 컨텍스트 리셋**: 세션이 중단되면 다음으로 상태를 재확립하세요.
>
> ```text
> Stop. New context. We just completed [description of recent work].
> Please review [upstream artifacts] to assess any impact of the recent change.
> [Paste the change description here.]
> ```

---

## 3. Construction Phase

Construction 단계는 설계가 코드가 되는 곳입니다. 각 unit of work는 일련의 (조건부) 설계 스테이지를 거친 후 Code Generation(항상)을 거칩니다. 모든 유닛이 완료된 후 Build and Test가 작업을 마무리합니다.

### 설계 리뷰 프로세스

각 unit of work에 대해 AIDLC는 코드를 생성하기 전에 다음 설계 스테이지의 일부 또는 전체를 실행할 수 있습니다.

- **Functional Design** — 비즈니스 로직, 도메인 모델, 데이터 스키마
- **NFR Requirements** — 성능, 보안, 확장성, 기술 스택 선택
- **NFR Design** — 설계에 NFR 패턴 적용
- **Infrastructure Design** — 설계를 실제 클라우드 서비스로 매핑

각 스테이지는 `aidlc-docs/construction/{unit-name}/`에 문서를 생성합니다. 각 게이트에서 당신의 일은 문서를 읽고 결정하는 것입니다: 변경 요청 또는 승인.

**승인하기 전에 읽으세요.** 설계 문서는 코드 생성의 source of truth입니다. 여기서 빠져나간 실수는 나중에 고치기 더 어렵습니다.

**설계에서 코드로 진행하기:**

Code Generation으로 전환할 준비가 되면, AI에게 사전에 필요한 구조적 컨텍스트를 제공하세요.

```text
We have completed component design review. We are ready for code creation.
Please use the following directory and source code structure:
[reference an existing service or folder structure].
Use this pattern for APIs. For the UI, follow the [Vue.js composables/components/store]
directory structure. Please ask any questions you have before proceeding.
```

생성 시작 전에 질문을 초대하면 파일 생성 중간이 아닌 plan 단계에서 모호함이 해결됩니다.

**타겟된 수정 요청:**

명확하게 — 요소, 무엇이 잘못되었는지, 어떻게 되어야 하는지를 명명하세요.

```text
The [endpoint description] should use [correct parameter], not [incorrect parameter].
Please update the [component name] accordingly.
```

**AI가 제시한 옵션 중에서 선택:**

```text
Please implement Option B — [option description] — for [feature name].
Update all component design documents accordingly.
```

옵션을 글자 *와* 설명으로 참조하고, 업데이트 범위를 질문이 발생한 문서뿐만 아니라 영향받는 모든 문서로 명시적으로 지정하세요.

**디자인 패턴 오버라이드:**

```text
We prefer to deviate from [standard pattern] and use [our preferred approach]
to allow [rationale]. Please update the component design documents accordingly.
```

근거가 중요합니다. AIDLC는 이를 이후 스테이지로 가지고 가서 일탈이 조용히 되돌아가는 것을 막아줍니다.

> **고급 팁 — 커밋 전 영향 평가**: 의미 있는 설계 변경에 대해서는 행동하기 전에 평가하세요.
>
> ```text
> Do not change anything. Assess the impact of [proposed change].
> [Describe the proposed change in detail.]
> ```
>
> **고급 팁 — 인라인 코드 문서화**: 모든 유닛에 일관되게 인라인 문서화를 적용하고 싶다면, 유닛별로 반복하기보다 Construction 단계 시작에서 상시 룰로 추가하세요: "Add inline code documentation as a standard rule for the construction phase."

---

### Code Generation 프로세스

Code Generation은 뚜렷한 두 부분으로 구성됩니다. 둘 다 명시적인 승인이 필요합니다.

**Part 1 — 계획 수립(Planning)**

AIDLC는 생성 또는 수정될 모든 파일에 대해 번호 매겨지고 체크박스로 추적되는 plan을 만듭니다. 승인하기 전에 이 plan을 검토하세요. 다음을 확인하세요.

- 모든 파일이 올바른 위치에 있는지 (애플리케이션 코드는 워크스페이스 루트, 절대 `aidlc-docs/` 안이 아님)
- 단계들이 설계 문서가 명시한 모든 것을 다루는지
- 브라운필드 프로젝트는 새 중복을 옆에 두지 않고 수정할 기존 파일을 나열하는지

> **고급 팁 — 내부 라이브러리**: plan을 승인하기 전에 Q&A 파일이나 구현 plan에 내부 라이브러리 요구사항을 주입하세요.
>
> ```text
> In addition to my answers, you must use the following libraries from our
> [starter project / building blocks]: [list each library explicitly].
> Explain why and when each should be used, not just what it is.
> ```
>
> 내부 라이브러리에 대한 큐레이팅된 마크다운 가이드가 AI에게 저장소를 가리키는 것보다 더 잘 작동합니다. 하나를 만들고 코드 생성 입력으로 참조하세요.
>
> **고급 팁 — Figma 디자인에서 UI 생성**: Figma 디자인의 스크린샷을 찍어 비전 가능 모델(예: ChatGPT)에 전달하여 스크린샷에서 프레임워크 코드를 생성한 다음, 그 출력을 UI 구현 입력으로 AIDLC에 제공하세요. 이는 원시 디자인 도구 export보다 구체적이고 도구가 읽을 수 있는 명세를 만들어냅니다.

**Part 2 — 생성(Generation)**

AIDLC는 각 단계를 순차적으로 실행하며 각 단계가 완료되면 체크오프합니다. 모든 단계가 끝나면 생성된 파일 경로와 함께 완료 메시지를 제시합니다.

승인하기 전에 생성된 코드를 검토하세요. 무언가가 맞지 않으면:

```text
Request Changes: [describe specifically what needs to change]
```

> **고급 팁 — 브라운필드 파일 수정**: 기존 코드베이스의 경우 AIDLC는 파일을 제자리에서 수정합니다. 원본 옆에 `ClassName_modified.java`나 `service_new.ts`가 보이면 즉시 표시하세요.
>
> ```text
> I see [ClassName_modified.java] alongside [ClassName.java]. Please merge the changes
> into the original file and delete the duplicate.
> ```

---

### Build and Test

모든 유닛이 완료되면 AIDLC가 모든 유닛에 대한 빌드 및 테스트 지침을 생성합니다. 알아두면 좋은 몇 가지 패턴.

**적시(just-in-time)에 테스트 도구 주입:**

프로젝트 시작에 테스트 프레임워크나 테스트 관리 시스템 지침을 추가하지 마세요. 코드 생성이 시작될 때쯤이면 그 세부 사항들이 사이의 많은 스테이지를 거치며 압축되거나 손실되었을 수 있습니다. 적시에 주입하세요.

```text
At the functional test generation step, inject the following instruction:
generate functional tests using the [test management system] format described
in this document: [attach specification]. Use this API endpoint to push the
generated test cases to the [test management system] repository: [endpoint details].
```

이 원칙은 모든 도구 특정 지침에 적용됩니다 — 프로젝트 시작이 아니라 필요한 단계에 주입하세요.

**유닛 테스트 커버리지 범위 지정:**

```text
When generating unit tests, exclude third-party external dependencies from
code coverage calculations. Require a minimum of 80% coverage on internal
code paths only.
```

---

### Code Generation 후: 변경 사항 역전파

코드 생성 중에 만들어진 변경 사항 — 작은 설계 결정, 코드 작성 중 발견된 조정 — 은 설계 문서로 거꾸로 흘러야 합니다. ad hoc이 아니라 코드 폴리시가 완료된 후 의도적인 sweep으로 수행하세요.

```text
When you have finished polishing the code, review each unit's final design files
and propagate any changes back up the chain to requirements and user stories.
Make a plan for how to do this step by step before executing.
```

실행 전에 plan을 요청하면 sweep이 선택적이지 않고 모든 유닛에 걸쳐 체계적이도록 보장합니다.

> **고급 팁 — 재사용 가능한 스펙 추출**: 완료된 프로젝트의 마지막에 당신이 정립한 패턴을 향후 프로젝트를 위한 재사용 가능한 명세 문서로 추출하세요.
>
> ```text
> Create a set of reusable specification documents from the patterns expressed
> in this project: one for API design, one for security, one for UI specifications,
> one for the technology stack, and one for directory structure. Use the completed
> units as the source. I will review and approve each document before it is used
> in future projects.
> ```

---

## 4. Never Vibe Code

Vibe coding은 빠른 수정이나 시도를 위해 생성된 코드 파일을 직접 편집하는 것을 의미합니다 — 설계 문서를 완전히 우회합니다. 순간에는 빠르게 느껴지지만 곧 문제를 만듭니다.

문제는 편집 자체가 아닙니다. AIDLC가 모든 후속 작업에 사용하는 source of truth인 설계 문서가 더 이상 코드가 실제로 하는 일을 반영하지 않는다는 점입니다. AIDLC가 관련 유닛에 대해 다음번 Code Generation을 실행하거나, 세션을 재개하거나, 동료가 작업을 인계받을 때 이 불일치는 혼란과 재작업을 야기합니다.

한 팀이 워크숍에서 직접적으로 표현했습니다.

> "You never fix code directly. If you discover an issue, go back to AIDLC and say: I have discovered issue X. Review the design and make a plan to fix it. If this affects the design, update it, then update the code."

**룰: 설계를 먼저 업데이트하고, 그다음에 코드를 생성한다.**

---

### 변경을 올바르게 만드는 방법

버그를 발견했든, 설계 결정에 대해 마음을 바꿨든, 새 요구사항을 받았든, 플로우는 동일합니다.

**Step 1 — 아무것도 건드리지 않고 이슈를 설명하기:**

```text
Do not update any documents yet. I have discovered issue [X].
Review the design and help me understand where this needs to be addressed.
```

**Step 2 — 설계 문서 수정:**

```text
Please update [specific design document] to reflect [the fix].
Then check whether any upstream documents — requirements, user stories —
also need to be updated.
```

**Step 3 — 영향받는 코드 재생성:**

```text
The design for [unit name] has been updated. Please re-run code generation
for the affected files only.
```

이 플로우는 파일을 직접 편집하는 것에 비해 몇 분이 더 걸립니다. 그러나 문서를 동기화 상태로 유지하고, 감사 로그를 완전하게 유지하며, 팀이 실제로 만들어진 것에 대해 정렬된 상태를 유지하게 합니다.

---

### "그냥 파일을 편집"하고 싶을 때

**"한 줄짜리 수정일 뿐이야."**

설계를 우회하는 한 줄 수정도 여전히 드리프트를 만듭니다. 관련 설계 문서에 수정을 메모하고 AIDLC가 적용하게 하세요.

```text
In [functional-design.md for unit X], update [method or rule] to [the fix].
Then regenerate [the affected file].
```

**"그냥 탐색 중이야 — 아직 확정된 건 없어."**

탐색은 정확히 "Do not update any documents"가 있는 이유입니다. 채팅에서 자유롭게 탐색하세요. 준비되었을 때만 커밋하세요.

**"지금 당장 팀을 unblock해야 해."**

때로는 빠르게 움직여야 합니다. 직접 편집을 하면, 감사 로그가 정확하게 유지되도록 정직하게 로깅하세요.

```text
We made a temporary direct edit to [file] to unblock the team.
The fix was [description]. Please update [design document] to reflect this
and verify no other documents are inconsistent.
```

---

### 드리프트를 막는 상시 룰

Construction 단계 시작에서 설정할 수 있는, 매번 묻는 것을 기억할 필요 없이 문제를 일찍 잡아주는 두 가지 상시 지시.

**모든 업데이트에 대한 역전파:**

```text
Every time you update a document, check whether the change impacts the
requirements document and user stories, and prompt me if it does.
```

**모든 코드 결정에 대한 design-first:**

```text
When you make a design decision during code generation, always make sure
the documentation reflects this change before proceeding.
```

Construction 시작에 한 번 설정하면 단계 전체에 적용됩니다.

---

### aidlc-docs 밖에 리포트 두기

실용적인 한 가지: AIDLC에 사람을 대상으로 한 리포트(아키텍처 다이어그램, 컴포넌트 요약, 스테이크홀더 프레젠테이션)를 생성하도록 요청한다면, `aidlc-docs/`에 저장되도록 두지 마세요. 그 파일들은 후속 스테이지에서 아티팩트로 로드되어 토큰 수를 부풀리고 무엇이 권위 있는 설계 입력인지에 대해 AI를 혼란스럽게 만들 수 있습니다.

별도의 `reports/` 폴더를 사용하고, 더 깨끗한 출력을 원하면 전용 리포트 명세 파일을 가진 새 컨텍스트에서 리포트를 생성하세요.

```text
Pause the process. Start a new context. Read [report specification markdown file]
and produce the report based on the current state of the AIDLC artifacts.
Save the output to a reports/ folder, not aidlc-docs/.
```

---

*입력 문서 작성에 대한 가이드는 [writing-inputs/inputs-quickstart.md](writing-inputs/inputs-quickstart.md)를 참고하세요.*
