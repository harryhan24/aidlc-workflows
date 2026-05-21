# Requirements Analysis — Validation Spec

## 입력

- Artifact: `requirements.md`
- Answered question file: `requirements-analysis-questions.md`
- Upstream: `intent.md`

## 규칙

1. skill의 Output에 정의된 5개 섹션 모두가 존재해야 합니다. 섹션에 내용이 없다면 "None identified."로 명시합니다.
2. 사용자 intent에 기술된 모든 capability는 최소 하나의 functional 또는 non-functional requirement로 추적 가능해야 합니다. 완전한 커버리지를 분석하여 다루지 않은 capability가 없도록 합니다.
3. functional requirements는 번호가 매겨져 있어야 하며 pass/fail로 검증 가능해야 합니다 — 모호한 진술은 허용되지 않습니다. `FR-<n>` 패턴을 사용합니다(예: FR-1, FR-2).
4. non-functional requirements는 가능한 한 측정 가능한 기준을 포함해야 합니다 — 정량화 없는 정성적 진술만 있어서는 안 됩니다.
5. 가정(assumption)은 사실이 아닌 가정으로 명시적으로 플래그 처리되어야 합니다.

## Deterministic post-conditions

규칙 1과 3은 `scripts/verify-structure.sh`에 결정론적 사후 조건이 있습니다.
