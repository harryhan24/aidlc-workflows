# 세션 연속성 템플릿 (Session Continuity Templates)

## Welcome Back 프롬프트 템플릿
사용자가 기존 AI-DLC 프로젝트를 이어서 작업하기 위해 돌아왔을 때, 다음 프롬프트를 제시합니다:

```markdown
**다시 오신 걸 환영합니다! 진행 중인 기존 AI-DLC 프로젝트가 있는 것이 확인됩니다.**

aidlc-state.md를 기준으로 현재 상태는 다음과 같습니다:
- **Project**: [project-name]
- **Current Phase**: [INCEPTION/CONSTRUCTION/OPERATIONS]
- **Current Stage**: [Stage Name]
- **Last Completed**: [마지막으로 완료한 단계]
- **Next Step**: [다음에 진행할 단계]

**오늘은 어떤 작업을 하시겠어요?**

A) 중단했던 지점부터 계속 ([Next step description])

B) 이전 스테이지 검토 ([사용 가능한 스테이지 표시])

[Answer]: 
```

## MANDATORY: 세션 연속성 안내
1. **기존 프로젝트를 감지하면 항상 aidlc-state.md를 먼저 읽으세요.**
2. **워크플로우 파일에서 현재 상태를 파싱**해 프롬프트를 채웁니다.
3. **MANDATORY: 이전 스테이지 아티팩트 로드** — 어떤 스테이지든 재개하기 전에, 이전 스테이지의 관련 아티팩트를 자동으로 읽으세요:
   - **Reverse Engineering**: architecture.md, code-structure.md, api-documentation.md를 읽음
   - **Requirements Analysis**: requirements.md, requirement-verification-questions.md를 읽음
   - **User Stories**: stories.md, personas.md, story-generation-plan.md를 읽음
   - **Application Design**: application-design 아티팩트(components.md, component-methods.md, services.md)를 읽음
   - **Design (Units)**: unit-of-work.md, unit-of-work-dependency.md, unit-of-work-story-map.md를 읽음
   - **Per-Unit Design**: functional-design.md, nfr-requirements.md, nfr-design.md, infrastructure-design.md를 읽음
   - **Code Stages**: 모든 코드 파일과 계획, **그리고** 이전의 모든 아티팩트를 읽음
4. **스테이지별 스마트 컨텍스트 로딩**:
   - **초기 스테이지 (Workspace Detection, Reverse Engineering)**: 워크스페이스 분석을 로드
   - **Requirements/Stories**: 리버스 엔지니어링 + 요구사항 아티팩트 로드
   - **Design 스테이지**: 요구사항 + 스토리 + 아키텍처 + 설계 아티팩트 로드
   - **Code 스테이지**: 모든 아티팩트 + 기존 코드 파일 로드
5. 아키텍처 선택과 현재 단계에 따라 **옵션을 적응**시키세요.
6. 두루뭉술한 설명 대신 **구체적인 다음 단계를 표시**하세요.
7. 연속성 프롬프트를 타임스탬프와 함께 **audit.md에 로깅**하세요.
8. **Context Summary**: 아티팩트 로드 후, 무엇이 로드되었는지 사용자에게 간단히 요약해 제공하세요.
9. **질문하기**: 명확화/피드백 질문은 항상 .md 파일에 작성해서 묻습니다. 객관식 질문을 채팅 세션에 인라인으로 두지 마세요.

## 에러 처리
세션을 재개할 때 아티팩트가 누락되거나 손상된 경우, 복구 절차에 대한 가이드는 [error-handling.md](error-handling.md)를 참고하세요.
