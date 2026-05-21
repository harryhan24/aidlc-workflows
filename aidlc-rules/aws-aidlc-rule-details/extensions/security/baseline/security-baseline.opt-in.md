# Security Baseline — Opt-In

**Extension**: Security Baseline

## 옵트인 프롬프트

본 익스텐션이 로드되면 Requirements Analysis 단계의 명확화 질문에 다음 질문이 자동으로 포함됩니다.

```markdown
## Question: Security Extensions
Should security extension rules be enforced for this project?

A) Yes — enforce all SECURITY rules as blocking constraints (recommended for production-grade applications)

B) No — skip all SECURITY rules (suitable for PoCs, prototypes, and experimental projects)

X) Other (please describe after [Answer]: tag below)

[Answer]: 
```
