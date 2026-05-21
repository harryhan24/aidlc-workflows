# Security Baseline — Opt-In

**Extension**: Security Baseline

## Opt-In Prompt

이 extension이 로드되면 다음 질문이 Requirements Analysis clarifying questions에 자동으로 포함됩니다.

```markdown
## Question: Security Extensions
이 project에 security extension rules를 blocking constraints로 적용할까요?

A) Yes — 모든 SECURITY rules를 blocking constraints로 적용합니다(production-grade applications에 권장).

B) No — 모든 SECURITY rules를 건너뜁니다(PoCs, prototypes, experimental projects에 적합).

X) Other (아래 [Answer]: tag 뒤에 설명하세요)

[Answer]:
```
