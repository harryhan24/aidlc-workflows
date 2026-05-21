# Content Validation Rules

## MANDATORY: Content Validation Before File Creation

**CRITICAL**: parsing error를 방지하기 위해 생성되는 모든 content는 파일에 쓰기 전에 반드시 검증해야 합니다.

## ASCII Diagram Standards

**CRITICAL**: ASCII diagram이 포함된 어떤 파일이든 만들기 전에 다음을 수행하세요.

1. `common/ascii-diagram-standards.md`를 **LOAD**합니다.
2. 각 diagram을 **VALIDATE**합니다.
   - line별 character 수를 셉니다(모든 line은 반드시 같은 width여야 함).
   - 다음 문자만 사용합니다: `+` `-` `|` `^` `v` `<` `>` 및 spaces
   - Unicode box-drawing characters를 사용하지 않습니다.
   - spaces만 사용합니다(tabs 금지).
3. box corners가 세로로 정렬되는지 확인해 alignment를 **TEST**합니다.

**pattern 및 validation checklist는 `common/ascii-diagram-standards.md`를 참조하세요.**

## Mermaid Diagram Validation

### Required Validation Steps

1. **Syntax Check**: 파일 생성 전에 Mermaid syntax를 검증합니다.
2. **Character Escaping**: special characters가 올바르게 escaped되었는지 확인합니다.
3. **Fallback Content**: Mermaid 검증이 실패하면 text alternative를 제공합니다.

### Mermaid Validation Rules

```markdown
## Mermaid diagram이 포함된 파일을 만들기 전에:

1. node ID에 invalid characters가 없는지 확인합니다(alphanumeric + underscore만 사용).
2. label의 special characters를 escape합니다: " → \" and ' → \'
3. flowchart syntax를 검증합니다. node connections는 valid해야 합니다.
4. simple validation으로 diagram parsing을 테스트합니다.

## FALLBACK: Mermaid validation이 실패하면 text-based workflow representation을 사용합니다.
```

### Implementation Pattern

````markdown
## Workflow Visualization

### Mermaid Diagram(문법이 valid한 경우)

```mermaid
[validated diagram content]
```

### Text Alternative(항상 포함)

```
Phase 1: INCEPTION
- Stage 1: Workspace Detection (COMPLETED)
- Stage 2: Requirements Analysis (COMPLETED)
[continue with text representation]
```
````

## General Content Validation

### Pre-Creation Validation Checklist

- [ ] embedded code blocks를 검증합니다(Mermaid, JSON, YAML).
- [ ] special character escaping을 확인합니다.
- [ ] markdown syntax correctness를 확인합니다.
- [ ] content parsing compatibility를 테스트합니다.
- [ ] complex elements에는 fallback content를 포함합니다.

### Error Prevention Rules

1. **tools/commands로 파일을 쓰기 전에 항상 검증합니다**: 검증되지 않은 content를 쓰지 마세요.
2. **special characters를 escape합니다**: 특히 diagrams와 code blocks에서 중요합니다.
3. **alternatives를 제공합니다**: visual content에는 text version을 포함합니다.
4. **syntax를 테스트합니다**: 복잡한 content structures를 검증합니다.

## Validation Failure Handling

### When Validation Fails

1. **Log the error**: validation에 실패한 항목을 기록합니다.
2. **Use fallback content**: text-based alternative로 전환합니다.
3. **Continue workflow**: content validation failure 때문에 workflow를 막지 않습니다.
4. **Inform user**: parsing constraints 때문에 simplified content를 사용했다고 알립니다.
