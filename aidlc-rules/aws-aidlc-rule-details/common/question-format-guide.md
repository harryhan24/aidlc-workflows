# 질문 포맷 가이드 (Question Format Guide)

## MANDATORY: 모든 질문은 이 포맷을 사용해야 합니다

### Rule: 채팅에서 직접 질문하지 말 것
**CRITICAL**: 채팅에서 직접 질문해서는 안 됩니다. 모든 질문은 전용 질문 파일에 두어야 합니다.

### 질문 파일 포맷

#### 파일 명명 규약
- 설명적인 이름 사용: `{phase-name}-questions.md`
- 예:
  - `classification-questions.md`
  - `requirements-questions.md`
  - `story-planning-questions.md`
  - `design-questions.md`

#### 질문 구조
모든 질문에는 의미 있는 옵션과 함께 마지막 옵션으로 "Other"를 포함해야 합니다:

```markdown
## Question [Number]
[명확하고 구체적인 질문 텍스트]

A) [의미 있는 옵션 1]

B) [의미 있는 옵션 2]

[...필요에 따라 옵션 추가...]

X) Other (please describe after [Answer]: tag below)

[Answer]: 
```

**CRITICAL**:
- "Other"는 모든 질문의 **마지막** 옵션으로 필수입니다.
- 의미 있는 옵션만 포함하세요 — 자리를 채우려고 옵션을 지어내지 마세요.
- 의미가 통하는 만큼만 옵션을 두세요 (최소 2개 + Other).
- **각 옵션은 빈 줄로 구분해야** 엄격한 CommonMark 렌더러(IntelliJ, PyCharm 등)가 한 문단으로 뭉치지 않고 분리해서 표시합니다.

### 완전한 예시

```markdown
# Requirements Clarification Questions

요구사항 명확화를 위해 다음 질문에 답해 주세요.

## Question 1
주요 사용자 인증 방식은 무엇인가요?

A) Username and password

B) Social media login (Google, Facebook)

C) Single Sign-On (SSO)

D) Multi-factor authentication

E) Other (please describe after [Answer]: tag below)

[Answer]: 

## Question 2
웹 애플리케이션인가요, 모바일 애플리케이션인가요?

A) Web application

B) Mobile application

C) Both web and mobile

D) Other (please describe after [Answer]: tag below)

[Answer]: 

## Question 3
새 프로젝트인가요, 기존 코드베이스인가요?

A) New project (greenfield)

B) Existing codebase (brownfield)

C) Other (please describe after [Answer]: tag below)

[Answer]: 
```

### 사용자 응답 형식
사용자는 [Answer]: 태그 뒤에 알파벳 선택지를 채워 답변합니다:

```markdown
## Question 1
주요 사용자 인증 방식은 무엇인가요?

A) Username and password

B) Social media login (Google, Facebook)

C) Single Sign-On (SSO)

D) Multi-factor authentication

[Answer]: C
```

### 사용자 응답 읽기
사용자가 완료를 알리면:
1. 질문 파일을 읽음
2. [Answer]: 태그 뒤의 답변을 추출
3. 모든 질문에 답변이 있는지 검증
4. 응답을 기반으로 분석을 진행

### 객관식 가이드라인

#### 옵션 개수
- 최소: 의미 있는 옵션 2개 + "Other" (A, B, C)
- 일반: 의미 있는 옵션 3-4개 + "Other" (A, B, C, D, E)
- 최대: 의미 있는 옵션 5개 + "Other" (A, B, C, D, E, F)
- **CRITICAL**: 자리를 채우려고 옵션을 지어내지 마세요 — 의미 있는 선택지만 포함하세요.

#### 옵션 품질
- 옵션은 상호 배타적으로 만드세요.
- 가장 흔한 시나리오를 다루세요.
- 의미 있고 현실적인 옵션만 포함하세요.
- **"Other"는 항상 마지막 옵션으로 포함**(필수)
- 구체적이고 명확하게.
- **A, B, C, D 슬롯을 채우려고 옵션을 지어내지 마세요.**

#### 좋은 예:
```markdown
## Question 5
어떤 데이터베이스 기술을 사용할 예정인가요?

A) Relational (PostgreSQL, MySQL)

B) NoSQL Document (MongoDB, DynamoDB)

C) NoSQL Key-Value (Redis, Memcached)

D) Graph Database (Neo4j, Neptune)

E) Other (please describe after [Answer]: tag below)

[Answer]: 
```

#### 나쁜 예 (피할 것):
```markdown
## Question 5
어떤 데이터베이스를 쓰시나요?

A) Yes

B) No

C) Maybe

[Answer]: 
```

### 워크플로우 통합

#### Step 1: 질문 파일 생성
```markdown
aidlc-docs/{phase-name}-questions.md를 모든 질문과 함께 생성
```

#### Step 2: 사용자에게 알리기
```
"{phase-name}-questions.md에 [X]개의 질문을 만들었습니다.
각 질문에 대해 [Answer]: 태그 뒤에 알파벳 선택지를 채워 답변해 주세요.
어떤 옵션도 맞지 않으면 마지막 옵션(Other)을 선택하고 선호를 기술해 주세요. 다 끝나면 알려주세요."
```

#### Step 3: 확인 대기
사용자가 "done", "completed", "finished" 또는 비슷한 표현을 할 때까지 기다립니다.

#### Step 4: 읽고 분석
```
aidlc-docs/{phase-name}-questions.md를 읽음
모든 답변을 추출
완전성 검증
분석 진행
```

### 에러 처리

#### 답변 누락
[Answer]: 태그가 비어 있는 경우:
```
"Question [X]에 답변이 없습니다. 진행하려면 모든 질문에 알파벳 선택지 중 하나로 답변해 주세요."
```

#### 잘못된 답변
답변이 유효한 알파벳 선택지가 아닌 경우:
```
"Question [X]의 답변 '[answer]'이(가) 유효하지 않습니다.
질문에 제시된 알파벳 선택지만 사용해 주세요."
```

#### 모호한 답변
사용자가 알파벳 대신 설명을 제공한 경우:
```
"Question [X]에 대해, 답변에 가장 가까운 알파벳 선택지를 알려주세요.
맞는 게 없다면 'Other'를 선택하고 [Answer]: 태그 뒤에 설명을 적어주세요."
```

### 모순 및 모호성 감지

**MANDATORY**: 사용자 응답을 읽은 뒤에는 모순과 모호성을 반드시 확인해야 합니다.

#### 모순 감지
논리적으로 일치하지 않는 답변을 찾으세요:
- 범위 불일치: "Bug fix"인데 "Entire codebase affected"
- 리스크 불일치: "Low risk"인데 "Breaking changes"
- 일정 불일치: "Quick fix"인데 "Multiple subsystems"
- 영향 불일치: "Single component"인데 "Significant architecture changes"

#### 모호성 감지
불명확하거나 경계선에 있는 응답을 찾으세요:
- 여러 분류에 모두 맞는 답변
- 구체성이 부족한 응답
- 여러 질문 간의 신호가 상충하는 경우

#### 명확화 질문 만들기
모순/모호성이 감지되면:

1. **명확화 파일 생성**: `{phase-name}-clarification-questions.md`
2. **이슈 설명**: 어떤 모순/모호성이 감지되었는지 명확히 기술
3. **표적 질문**: 객관식 포맷으로 이슈 해소
4. **원 질문 참조**: 어느 질문에서 충돌하는 답변이 나왔는지 보여주기

**예**:
```markdown
# [Phase Name] Clarification Questions

응답에서 다음 모순을 감지했고, 명확화가 필요합니다:

## Contradiction 1: [Brief Description]
"[Answer A]" (Q[X]:[Letter])이라고 하셨지만 "[Answer B]" (Q[Y]:[Letter])이라고도 하셨습니다.
이 응답들은 [설명] 때문에 모순됩니다.

### Clarification Question 1
[모순 해소를 위한 구체적 질문]

A) [첫 번째 답으로 해소되는 옵션]

B) [두 번째 답으로 해소되는 옵션]

C) [중간 지점 옵션]

D) [질문 재구성 옵션]

[Answer]: 

## Ambiguity 1: [Brief Description]
Q[X]의 응답("[Answer]")은 [설명] 때문에 모호합니다.

### Clarification Question 2
[모호성 해소를 위한 구체적 질문]

A) [명확한 옵션 1]

B) [명확한 옵션 2]

C) [명확한 옵션 3]

D) [명확한 옵션 4]

[Answer]: 
```

#### 명확화 워크플로우

1. **감지**: 모든 응답에서 모순/모호성을 분석
2. **생성**: 이슈가 발견되면 명확화 질문 파일을 생성
3. **알리기**: 사용자에게 이슈와 명확화 파일을 알림
4. **대기**: 사용자가 명확화를 제공할 때까지 진행하지 마세요
5. **재검증**: 명확화 후 일관성을 다시 확인
6. **진행**: 모든 모순이 해소되었을 때만 다음으로 이동

#### 사용자 메시지 예시
```
"응답에서 2개의 모순을 감지했습니다:

1. Bug fix 범위 vs. 코드베이스 영향 (Q1 vs Q2)
2. 낮은 리스크 vs. 호환성을 깨는 변경 (Q7 vs Q4)

이를 해소할 2개의 질문을 담은 classification-clarification-questions.md를 만들었습니다.
분류를 진행하기 전에 이 명확화 질문들에 답해 주세요."
```

### 모범 사례

1. **Be Specific**: 질문은 명확하고 모호하지 않아야 함
2. **Be Comprehensive**: 필요한 모든 정보를 다룸
3. **Be Concise**: 질문은 한 주제에 집중
4. **Be Practical**: 옵션은 현실적이고 실행 가능해야 함
5. **Be Consistent**: 모든 질문 파일에서 동일한 포맷 사용

### 단계별 예시

#### 의미 있는 옵션 2개:
```markdown
## Question 1
새 프로젝트인가요, 기존 코드베이스인가요?

A) New project (greenfield)

B) Existing codebase (brownfield)

C) Other (please describe after [Answer]: tag below)

[Answer]: 
```

#### 의미 있는 옵션 3개:
```markdown
## Question 2
배포 타깃은 무엇인가요?

A) Cloud (AWS, Azure, GCP)

B) On-premises servers

C) Hybrid (both cloud and on-premises)

D) Other (please describe after [Answer]: tag below)

[Answer]: 
```

#### 의미 있는 옵션 4개:
```markdown
## Question 3
어떤 아키텍처 패턴을 사용해야 하나요?

A) Monolithic architecture

B) Microservices architecture

C) Serverless architecture

D) Event-driven architecture

E) Other (please describe after [Answer]: tag below)

[Answer]: 
```

## 요약

**기억하세요**: 
- ✅ 항상 질문 파일을 만들 것
- ✅ 항상 객관식 포맷을 사용할 것
- ✅ **"Other"는 항상 마지막 옵션으로 포함**(필수)
- ✅ 의미 있는 옵션만 포함 — 자리를 채우려고 옵션 지어내지 말 것
- ✅ 항상 [Answer]: 태그 사용
- ✅ 항상 사용자 완료를 기다릴 것
- ✅ 응답의 모순 여부를 항상 검증할 것
- ✅ 필요하면 항상 명확화 파일을 만들 것
- ✅ 진행 전에 항상 모순을 해소할 것
- ❌ 채팅에서 질문하지 말 것
- ❌ A, B, C, D를 채우려고 옵션 지어내지 말 것
- ❌ 답변 없이 진행하지 말 것
- ❌ 미해소 모순을 안고 진행하지 말 것
- ❌ 모호한 응답에 가정을 하지 말 것
