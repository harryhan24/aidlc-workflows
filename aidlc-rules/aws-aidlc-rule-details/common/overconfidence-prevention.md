# Overconfidence Prevention Guide

## Problem Statement

AI-DLC는 복잡한 project intent statements에 대해서도 충분한 clarifying questions를 묻지 않는 overconfidence를 보였습니다. 그 결과 적절한 requirements를 수집하지 않고 assumptions를 만들게 되었습니다.

## Root Cause Analysis

overconfidence issue는 여러 stage에서 질문을 건너뛰도록 유도하는 지시 때문에 발생했습니다.

1. **Functional Design**: "Skip entire categories if not applicable"
2. **User Stories**: "Use categories as inspiration, NOT as mandatory checklist"
3. **Requirements Analysis**: minimal questioning을 유도하는 유사 pattern
4. **NFR Requirements**: thorough analysis를 방해하는 "Only if" 조건

이 지시들은 comprehensive requirements gathering을 장려하기보다 AI가 질문을 피하도록 만들었습니다.

## Solution Implemented

### Updated Question Generation Philosophy

**OLD APPROACH**: "꼭 필요한 경우에만 질문한다"
**NEW APPROACH**: "확신이 없으면 질문한다. overconfidence는 좋지 않은 결과로 이어진다"

### Key Changes Made

#### 1. Requirements Analysis Stage

- "only if needed"에서 "exceptionally clear한 경우가 아니면 항상 questions 생성"으로 변경했습니다.
- 포괄적인 evaluation areas(functional, non-functional, business context, technical context)를 추가했습니다.
- proactive questioning approach를 강조했습니다.

#### 2. User Stories Stage

- "skip entire categories" 지시를 제거했습니다.
- 평가할 comprehensive question categories를 추가했습니다.
- answer analysis requirements를 강화했습니다.
- follow-up question mandates를 강화했습니다.

#### 3. Functional Design Stage

- "only if" 조건을 comprehensive evaluation으로 교체했습니다.
- 더 많은 question categories(data flow, integration points, error handling)를 추가했습니다.
- ambiguity detection and resolution requirements를 강화했습니다.

#### 4. NFR Requirements Stage

- question categories를 기본 NFR 밖으로 확장했습니다.
- reliability, maintainability, usability considerations를 추가했습니다.
- technical ambiguities에 대한 answer analysis를 강화했습니다.

### New Guiding Principles

1. **Default to Asking**: ambiguity가 조금이라도 있으면 clarifying questions를 묻습니다.
2. **Comprehensive Coverage**: 관련 category를 모두 평가하고 영역을 건너뛰지 않습니다.
3. **Thorough Analysis**: 모든 user responses를 신중하게 분석해 ambiguities를 찾습니다.
4. **Mandatory Follow-up**: 명확하지 않은 response에는 반드시 follow-up questions를 만듭니다.
5. **No Proceeding with Ambiguity**: 모든 ambiguity가 해결될 때까지 앞으로 진행하지 않습니다.

## Implementation Guidelines

### For Question Generation

- 모든 question category를 평가하고, 어떤 category도 건너뛰지 않습니다.
- clarification이 quality를 높일 수 있는 곳에서는 질문합니다.
- 각 stage에 comprehensive question categories를 포함합니다.
- 질문 포함 여부는 exclusion보다 inclusion을 기본값으로 합니다.

### For Answer Analysis

- vague responses를 찾습니다: "depends", "maybe", "not sure", "mix of", "somewhere between"
- undefined terms와 external concepts 참조를 감지합니다.
- contradictory 또는 incomplete answers를 식별합니다.
- 어떤 ambiguity든 있으면 follow-up questions를 만듭니다.

### For Follow-up Questions

- ambiguity가 감지되면 별도 clarification files를 만듭니다.
- 각 ambiguity를 해결할 specific questions를 묻습니다.
- 명확하지 않은 모든 response가 clarification될 때까지 진행하지 않습니다.
- 철저하게 확인합니다. under-clarify보다 over-clarify가 낫습니다.

## Quality Assurance

### Red Flags to Watch For

- complex project에서 질문 없이 stage가 완료됨
- vague 또는 ambiguous user responses가 있는데도 진행함
- justification 없이 entire question categories를 건너뜀
- clarification을 묻지 않고 assumption을 만듦

### Success Indicators

- project complexity에 맞는 적절한 수의 clarifying questions
- 필요 시 follow-up을 포함한 user responses의 철저한 analysis
- implementation 전에 명확하고 모호하지 않은 requirements 확보
- upfront clarification 개선으로 later stages에서 change 필요성 감소

## Maintenance

다음 경우 이 guide를 참조해야 합니다.

- AI-DLC에 새 stage 추가
- 기존 stage instructions 업데이트
- overconfidence issue 관점에서 AI-DLC performance 리뷰
- team members에게 AI-DLC question generation principles 교육

## Key Takeaway

**잘못된 assumption을 만드는 것보다 질문을 너무 많이 하는 편이 낫습니다.** 처음에 clarifying questions를 묻는 비용은 assumption에 기반해 잘못된 solution을 구현하는 비용보다 훨씬 작습니다.
