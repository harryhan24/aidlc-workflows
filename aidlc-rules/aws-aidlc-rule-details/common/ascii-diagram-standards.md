# ASCII Diagram Standards

## MANDATORY: Use Basic ASCII Only

**CRITICAL**: diagram에는 항상 basic ASCII characters만 사용하세요(maximum compatibility).

### ✅ ALLOWED: `+` `-` `|` `^` `v` `<` `>` 및 alphanumeric text

### ❌ FORBIDDEN: Unicode box-drawing characters

- 사용 금지: `┌` `─` `│` `└` `┐` `┘` `├` `┤` `┬` `┴` `┼` `▼` `▲` `►` `◄`
- 이유: font/platform에 따라 rendering이 일관되지 않습니다.

## Standard ASCII Diagram Patterns

### CRITICAL: Character Width Rule

**box 안의 모든 line은 반드시 정확히 같은 character count를 가져야 합니다(spaces 포함).**

✅ CORRECT(모든 line = 67 chars):

```
+---------------------------------------------------------------+
|                      Component Name                           |
|  Description text here                                        |
+---------------------------------------------------------------+
```

❌ WRONG(width가 일관되지 않음):

```
+---------------------------------------------------------------+
|                      Component Name                           |
|  Description text here                                   |
+---------------------------------------------------------------+
```

### Box Pattern

```
+-----------------------------------------------------+
|                                                     |
|              Calculator Application                 |
|                                                     |
|  Provides basic arithmetic operations for users     |
|  through a web-based interface                      |
|                                                     |
+-----------------------------------------------------+
```

### Nested Boxes

```
+-------------------------------------------------------+
|              Web Server (PHP Runtime)                 |
|  +-------------------------------------------------+  |
|  |  index.php (Monolithic Application)             |  |
|  |  +-------------------------------------------+  |  |
|  |  |  HTML Template (View Layer)               |  |  |
|  |  |  - Form rendering                         |  |  |
|  |  |  - Result display                         |  |  |
|  |  +-------------------------------------------+  |  |
|  +-------------------------------------------------+  |
+-------------------------------------------------------+
```

### Arrows and Connections

```
+----------+
|  Source  |
+----------+
     |
     | HTTP POST
     v
+----------+
|  Target  |
+----------+
```

### Horizontal Flow

```
+-------+     +-------+     +-------+
| Step1 | --> | Step2 | --> | Step3 |
+-------+     +-------+     +-------+
```

### Vertical Flow with Labels

```
User Action Flow:
    |
    v
+----------+
|  Input   |
+----------+
    |
    | validates
    v
+----------+
| Process  |
+----------+
    |
    | returns
    v
+----------+
|  Output  |
+----------+
```

## Validation

diagram을 만들기 전에 다음을 확인합니다.

- [ ] Basic ASCII only: `+` `-` `|` `^` `v` `<` `>`
- [ ] Unicode box-drawing 없음
- [ ] alignment에는 spaces만 사용(tabs 금지)
- [ ] corner는 `+` 사용
- [ ] **모든 box line의 character width가 같음**(spaces 포함 character 수 계산)
- [ ] Test: monospace font에서 corner가 세로로 정렬되는지 확인

## Alternative

복잡한 diagram에는 Mermaid를 사용하세요(`content-validation.md` 참조).
