# Question Format Guide

## MANDATORY: All Questions Must Use This Format

### Rule: Never Ask Questions in Chat

**CRITICAL**: 질문을 chat에서 직접 물어서는 절대 안 됩니다. 모든 질문은 전용 question file에 배치해야 합니다.

### Question File Format

#### File Naming Convention

- 설명적인 이름을 사용합니다: `{phase-name}-questions.md`
- 예:
  - `classification-questions.md`
  - `requirements-questions.md`
  - `story-planning-questions.md`
  - `design-questions.md`

#### Question Structure

모든 질문에는 의미 있는 options와 마지막 option인 "Other"가 포함되어야 합니다.

```markdown
## Question [Number]
[명확하고 구체적인 질문 텍스트]

A) [첫 번째 의미 있는 option]

B) [두 번째 의미 있는 option]

[...필요한 만큼 추가 options...]

X) Other (아래 [Answer]: tag 뒤에 설명하세요)

[Answer]:
```

**CRITICAL**:

- "Other"는 모든 질문에서 반드시 마지막 option이어야 합니다.
- 의미 있는 option만 포함하세요. slot을 채우기 위해 option을 지어내지 마세요.
- 상황에 맞게 option 수를 정하세요(최소 2개 + Other).
- strict CommonMark renderer(IntelliJ, PyCharm 등)가 각 option을 한 문단으로 합치지 않고 별도 줄로 표시하도록 **각 option 사이에는 blank line을 넣어야 합니다**.

### Complete Example

```markdown
# Requirements Clarification Questions

requirements를 명확히 하기 위해 다음 질문에 답해 주세요.

## Question 1
주요 사용자 인증 방식은 무엇인가요?

A) 사용자 이름과 비밀번호

B) 소셜 로그인(Google, Facebook)

C) Single Sign-On (SSO)

D) Multi-factor authentication

E) Other (아래 [Answer]: tag 뒤에 설명하세요)

[Answer]:

## Question 2
이 애플리케이션은 web인가요, mobile인가요?

A) Web application

B) Mobile application

C) Web과 mobile 모두

D) Other (아래 [Answer]: tag 뒤에 설명하세요)

[Answer]:

## Question 3
새 프로젝트인가요, 기존 코드베이스인가요?

A) 새 프로젝트(greenfield)

B) 기존 코드베이스(brownfield)

C) Other (아래 [Answer]: tag 뒤에 설명하세요)

[Answer]:
```

### User Response Format

사용자는 [Answer]: tag 뒤에 letter choice를 채워 답변합니다.

```markdown
## Question 1
주요 사용자 인증 방식은 무엇인가요?

A) 사용자 이름과 비밀번호

B) 소셜 로그인(Google, Facebook)

C) Single Sign-On (SSO)

D) Multi-factor authentication

[Answer]: C
```

### Reading User Responses

사용자가 완료를 확인하면 다음을 수행합니다.

1. question file을 읽습니다.
2. [Answer]: tags 뒤의 답변을 추출합니다.
3. 모든 질문에 답변했는지 검증합니다.
4. 응답을 바탕으로 analysis를 진행합니다.

### Multiple Choice Guidelines

#### Option Count

- Minimum: 의미 있는 option 2개 + "Other"(A, B, C)
- Typical: 의미 있는 option 3-4개 + "Other"(A, B, C, D, E)
- Maximum: 의미 있는 option 5개 + "Other"(A, B, C, D, E, F)
- **CRITICAL**: slot을 채우기 위해 option을 지어내지 마세요. 의미 있는 choice만 포함합니다.

#### Option Quality

- options는 mutually exclusive하게 만듭니다.
- 가장 일반적인 scenario를 포괄합니다.
- 의미 있고 현실적인 option만 포함합니다.
- **"Other"를 항상 마지막 option으로 포함합니다**(MANDATORY).
- 구체적이고 명확하게 작성합니다.
- **A, B, C, D slot을 채우기 위해 option을 지어내지 마세요.**

#### Good Example:

```markdown
## Question 5
어떤 database technology를 사용할 예정인가요?

A) Relational (PostgreSQL, MySQL)

B) NoSQL Document (MongoDB, DynamoDB)

C) NoSQL Key-Value (Redis, Memcached)

D) Graph Database (Neo4j, Neptune)

E) Other (아래 [Answer]: tag 뒤에 설명하세요)

[Answer]:
```

#### Bad Example (Avoid):

```markdown
## Question 5
어떤 database를 사용할 예정인가요?

A) Yes

B) No

C) Maybe

[Answer]:
```

### Workflow Integration

#### Step 1: Create Question File

```markdown
aidlc-docs/{phase-name}-questions.md를 만들고 모든 질문을 포함합니다.
```

#### Step 2: Inform User

```text
"{phase-name}-questions.md 파일에 [X]개의 질문을 만들었습니다.
각 질문의 [Answer]: tag 뒤에 letter choice를 채워 답변해 주세요.
선택지 중 맞는 것이 없다면 마지막 option(Other)을 선택하고 선호 사항을 설명하세요.
완료되면 알려 주세요."
```

#### Step 3: Wait for Confirmation

사용자가 "done", "completed", "finished" 또는 유사한 표현으로 말할 때까지 기다립니다.

#### Step 4: Read and Analyze

```text
aidlc-docs/{phase-name}-questions.md를 읽습니다.
모든 answers를 추출합니다.
completeness를 검증합니다.
analysis를 진행합니다.
```

### Error Handling

#### Missing Answers

[Answer]: tag가 비어 있으면 다음처럼 말합니다.

```text
"Question [X]에 답변이 없습니다. 진행하기 전에 모든 질문에 대해 제공된 letter choice 중 하나로 답변해 주세요."
```

#### Invalid Answers

답변이 valid letter choice가 아니면 다음처럼 말합니다.

```text
"Question [X]의 답변 '[answer]'가 유효하지 않습니다.
질문에 제공된 letter choice만 사용해 주세요."
```

#### Ambiguous Answers

사용자가 letter 대신 설명을 제공하면 다음처럼 말합니다.

```text
"Question [X]에는 답변과 가장 잘 맞는 letter choice를 제공해 주세요.
맞는 선택지가 없다면 'Other'를 선택하고 [Answer]: tag 뒤에 설명을 추가하세요."
```

### Contradiction and Ambiguity Detection

**MANDATORY**: 사용자 응답을 읽은 뒤 contradiction과 ambiguity를 반드시 확인해야 합니다.

#### Detecting Contradictions

논리적으로 일관되지 않은 답변을 찾습니다.

- Scope mismatch: "Bug fix"라고 했지만 "Entire codebase affected"라고 답함
- Risk mismatch: "Low risk"라고 했지만 "Breaking changes"라고 답함
- Timeline mismatch: "Quick fix"라고 했지만 "Multiple subsystems"라고 답함
- Impact mismatch: "Single component"라고 했지만 "Significant architecture changes"라고 답함

#### Detecting Ambiguities

명확하지 않거나 경계에 있는 응답을 찾습니다.

- 여러 classification에 들어맞을 수 있는 답변
- specificity가 부족한 응답
- 여러 질문 간 conflicting indicators

#### Creating Clarification Questions

contradiction 또는 ambiguity가 감지되면 다음을 수행합니다.

1. **Create clarification file**: `{phase-name}-clarification-questions.md`
2. **Explain the issue**: 어떤 contradiction/ambiguity가 감지되었는지 명확히 설명합니다.
3. **Ask targeted questions**: 문제를 해결하기 위해 multiple choice format으로 질문합니다.
4. **Reference original questions**: 어떤 질문의 답변이 충돌했는지 보여줍니다.

**Example**:

```markdown
# [Phase Name] Clarification Questions

응답에서 clarification이 필요한 contradiction을 발견했습니다.

## Contradiction 1: [간단한 설명]
사용자는 "[Answer A]"(Q[X]:[Letter])라고 답했지만, 동시에 "[Answer B]"(Q[Y]:[Letter])라고 답했습니다.
이 응답들이 모순되는 이유는 다음과 같습니다: [explanation].

### Clarification Question 1
[contradiction을 해결하기 위한 구체적인 질문]

A) [첫 번째 답변 방향으로 해결하는 option]

B) [두 번째 답변 방향으로 해결하는 option]

C) [중간 지점을 제공하는 option]

D) [질문을 재구성하는 option]

[Answer]:

## Ambiguity 1: [간단한 설명]
Q[X]에 대한 응답("[Answer]")은 다음 이유로 ambiguous합니다: [explanation].

### Clarification Question 2
[ambiguity를 명확히 하기 위한 구체적인 질문]

A) [명확한 option 1]

B) [명확한 option 2]

C) [명확한 option 3]

D) [명확한 option 4]

[Answer]:
```

#### Workflow for Clarifications

1. **Detect**: 모든 response를 분석해 contradictions/ambiguities를 찾습니다.
2. **Create**: 문제가 발견되면 clarification question file을 생성합니다.
3. **Inform**: 사용자에게 issues와 clarification file을 알립니다.
4. **Wait**: 사용자가 clarification을 제공할 때까지 진행하지 않습니다.
5. **Re-validate**: clarification 후 consistency를 다시 확인합니다.
6. **Proceed**: 모든 contradiction이 해결된 경우에만 앞으로 진행합니다.

#### Example User Message

```text
"응답에서 2개의 contradiction을 발견했습니다.

1. Bug fix scope vs. codebase impact(Q1 vs Q2)
2. Low risk vs. breaking changes(Q7 vs Q4)

이를 해결하기 위해 classification-clarification-questions.md에 2개의 질문을 만들었습니다.
classification을 진행하기 전에 이 clarification questions에 답해 주세요."
```

### Best Practices

1. **Be Specific**: 질문은 명확하고 모호하지 않아야 합니다.
2. **Be Comprehensive**: 필요한 정보를 모두 포괄합니다.
3. **Be Concise**: 질문은 하나의 topic에 집중합니다.
4. **Be Practical**: options는 현실적이고 actionable해야 합니다.
5. **Be Consistent**: 모든 question file에서 같은 format을 사용합니다.

### Phase-Specific Examples

#### Example with 2 meaningful options:

```markdown
## Question 1
새 프로젝트인가요, 기존 코드베이스인가요?

A) 새 프로젝트(greenfield)

B) 기존 코드베이스(brownfield)

C) Other (아래 [Answer]: tag 뒤에 설명하세요)

[Answer]:
```

#### Example with 3 meaningful options:

```markdown
## Question 2
deployment target은 무엇인가요?

A) Cloud (AWS, Azure, GCP)

B) On-premises servers

C) Hybrid (cloud와 on-premises 모두)

D) Other (아래 [Answer]: tag 뒤에 설명하세요)

[Answer]:
```

#### Example with 4 meaningful options:

```markdown
## Question 3
어떤 architectural pattern을 사용해야 하나요?

A) Monolithic architecture

B) Microservices architecture

C) Serverless architecture

D) Event-driven architecture

E) Other (아래 [Answer]: tag 뒤에 설명하세요)

[Answer]:
```

## Summary

**Remember**:

- ✅ 항상 question files를 만드세요.
- ✅ 항상 multiple choice format을 사용하세요.
- ✅ **"Other"를 항상 마지막 option으로 포함하세요(MANDATORY).**
- ✅ 의미 있는 option만 포함하고, slot을 채우기 위해 option을 지어내지 마세요.
- ✅ 항상 [Answer]: tags를 사용하세요.
- ✅ 항상 user completion을 기다리세요.
- ✅ 항상 response의 contradiction을 검증하세요.
- ✅ 필요하면 항상 clarification files를 만드세요.
- ✅ 진행 전 contradiction을 항상 해결하세요.
- ❌ chat에서 질문하지 마세요.
- ❌ A, B, C, D를 만들기 위해 option을 지어내지 마세요.
- ❌ 답변 없이 진행하지 마세요.
- ❌ 해결되지 않은 contradiction이 있는 상태로 진행하지 마세요.
- ❌ ambiguous response에 대해 assumption을 만들지 마세요.
