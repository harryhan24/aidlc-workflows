# Session Continuity Templates

## Welcome Back Prompt Template

사용자가 기존 AI-DLC 프로젝트 작업을 이어가기 위해 돌아오면 이 프롬프트를 제시합니다.

```markdown
**다시 오신 것을 환영합니다! 진행 중인 AI-DLC 프로젝트가 있는 것으로 보입니다.**

aidlc-state.md 기준 현재 상태는 다음과 같습니다.
- **Project**: [project-name]
- **Current Phase**: [INCEPTION/CONSTRUCTION/OPERATIONS]
- **Current Stage**: [Stage Name]
- **Last Completed**: [Last completed step]
- **Next Step**: [Next step to work on]

**오늘 어떤 작업을 진행하시겠습니까?**

A) 이전에 중단한 지점부터 계속 진행([Next step description])

B) 이전 stage 검토([Show available stages])

[Answer]:
```

## MANDATORY: Session Continuity Instructions

1. 기존 프로젝트를 감지하면 **항상 aidlc-state.md를 먼저 읽습니다**.
2. workflow file에서 **current status를 parse**해 프롬프트를 채웁니다.
3. **MANDATORY: Load Previous Stage Artifacts** - 어떤 stage를 재개하든, 먼저 이전 stage의 관련 산출물을 모두 자동으로 읽습니다.
   - **Reverse Engineering**: architecture.md, code-structure.md, api-documentation.md를 읽습니다.
   - **Requirements Analysis**: requirements.md, requirement-verification-questions.md를 읽습니다.
   - **User Stories**: stories.md, personas.md, story-generation-plan.md를 읽습니다.
   - **Application Design**: application-design 산출물(components.md, component-methods.md, services.md)을 읽습니다.
   - **Design (Units)**: unit-of-work.md, unit-of-work-dependency.md, unit-of-work-story-map.md를 읽습니다.
   - **Per-Unit Design**: functional-design.md, nfr-requirements.md, nfr-design.md, infrastructure-design.md를 읽습니다.
   - **Code Stages**: 모든 code files, plans, 이전 산출물 전체를 읽습니다.
4. **Smart Context Loading by Stage**:
   - **Early Stages(Workspace Detection, Reverse Engineering)**: workspace analysis를 로드합니다.
   - **Requirements/Stories**: reverse engineering + requirements 산출물을 로드합니다.
   - **Design Stages**: requirements + stories + architecture + design 산출물을 로드합니다.
   - **Code Stages**: 모든 산출물 + 기존 code files를 로드합니다.
5. architectural choice와 current phase에 맞게 options를 조정합니다.
6. generic description 대신 구체적인 next steps를 보여줍니다.
7. continuity prompt를 timestamp와 함께 audit.md에 기록합니다.
8. **Context Summary**: 산출물을 로드한 뒤, 사용자 인지를 위해 로드한 항목을 간단히 요약합니다.
9. **Asking questions**: clarification 또는 user feedback questions는 항상 .md 파일에 배치합니다. 객관식 질문을 chat session 안에 inline으로 두지 마세요.

## Error Handling

세션 재개 중 산출물이 없거나 손상되어 있으면 복구 절차는 [error-handling.md](error-handling.md)를 참조하세요.
