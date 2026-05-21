# Question 포맷

## 파일 포맷

한 clarification 라운드의 모든 질문은 한 번에 question 파일에 저장됩니다. 각 질문은 다음 포맷을 사용합니다.

```
### Q<n>: <question text>

a) Option A
b) Option B
c) Option C
d) Other

**Trade Offs:** <explain the trade-offs between options, if applicable>

**Recommendation:** <AI's recommended option with brief reasoning>

[Answer]:
```

## 채팅 표시 방식

- 채팅에서는 위와 동일한 포맷을 사용하여 질문을 한 번에 하나씩 제시합니다.
- 밀접하게 연관된 두 질문은 함께 제시합니다.
- 진행 상황을 표시합니다: "Q1 of N", "Q2 of N" 등.
- 다음 질문을 제시하기 전에 사용자의 답변을 기다립니다.
- 사용자는 채팅에서 답변하거나, question 파일로 이동하여 한꺼번에 모두 답변할 수 있습니다.
- Trade Offs와 Recommendation 섹션은 질문마다 선택 사항입니다 — 선택이 의미 있는 영향을 갖는 경우에는 포함하고, 단순한 질문에서는 생략합니다.
