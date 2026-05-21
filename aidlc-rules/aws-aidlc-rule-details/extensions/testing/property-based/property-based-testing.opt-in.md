# Property-Based Testing — Opt-In

**Extension**: Property-Based Testing

## Opt-In Prompt

이 extension이 로드되면 다음 질문이 Requirements Analysis clarifying questions에 자동으로 포함됩니다.

```markdown
## Question: Property-Based Testing Extension
이 project에 property-based testing(PBT) rules를 적용할까요?

A) Yes — 모든 PBT rules를 blocking constraints로 적용합니다(business logic, data transformations, serialization, stateful components가 있는 projects에 권장).

B) Partial — pure functions와 serialization round-trips에만 PBT rules를 적용합니다(algorithmic complexity가 제한적인 projects에 적합).

C) No — 모든 PBT rules를 건너뜁니다(simple CRUD applications, UI-only projects, significant business logic이 없는 thin integration layers에 적합).

X) Other (아래 [Answer]: tag 뒤에 설명하세요)

[Answer]:
```
