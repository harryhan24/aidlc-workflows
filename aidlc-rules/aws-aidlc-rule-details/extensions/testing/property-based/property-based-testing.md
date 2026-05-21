# Property-Based Testing Rules

## Overview

이 property-based testing(PBT) rules는 applicable AI-DLC phases에 걸쳐 적용되는 cross-cutting constraints입니다. identifiable properties가 있는 code를 property-based techniques로 테스트하도록 보장하며, traditional example-based tests를 대체하지 않고 보완합니다.

property-based testing은 모든 valid inputs에 대해 유지되어야 하는 invariants를 정의한 뒤, framework를 사용해 random inputs를 생성하고 counterexamples를 찾습니다. failure가 발견되면 framework는 input을 minimal reproducing case로 shrink합니다. 이 접근은 example-based testing이 자주 놓치는 edge cases와 subtle bugs를 찾아냅니다.

**Enforcement**: 각 applicable stage에서 model은 stage completion message를 사용자에게 제시하기 전에 이 rules의 compliance를 반드시 검증해야 합니다.

### Blocking PBT Finding Behavior

**blocking PBT finding**은 다음을 의미합니다.

1. 해당 finding은 stage completion message의 "PBT Findings" section에 PBT rule ID와 description과 함께 반드시 나열해야 합니다.
2. stage는 모든 blocking findings가 해결될 때까지 "Continue to Next Stage" option을 제시하면 안 됩니다.
3. model은 변경이 필요한 내용을 명확히 설명하면서 "Request Changes" option만 제시해야 합니다.
4. finding은 PBT rule ID, description, stage context와 함께 `aidlc-docs/audit.md`에 반드시 기록해야 합니다.

PBT rule이 current project 또는 unit에 applicable하지 않다면(예: stateful components가 없을 때 PBT-06), compliance summary에서 **N/A**로 표시합니다. 이것은 blocking finding이 아닙니다.

### Default Enforcement

이 문서의 모든 rules는 기본적으로 **blocking**입니다. rule의 verification criteria가 충족되지 않으면 blocking PBT finding입니다. 위에서 정의한 blocking finding behavior를 따르세요.

### Partial Enforcement Mode

사용자가 opt-in 중 **Partial** enforcement를 선택했다면 PBT-02, PBT-03, PBT-07, PBT-08, PBT-09만 enforce합니다. 다른 모든 rules는 advisory(non-blocking)로 취급합니다. enforcement mode는 `aidlc-docs/aidlc-state.md`의 `## Extension Configuration` 아래에 기록합니다.

### Verification Criteria Format

이 문서의 verification items는 compliance checks를 설명하는 일반 bullet points입니다. 각 item은 review 중 compliant 또는 non-compliant로 평가해야 합니다.

---

## Rule PBT-01: Property Identification During Design

**Rule**: business logic, data transformations 또는 algorithmic operations를 포함하는 모든 unit은 Functional Design stage에서 testable properties를 분석해야 합니다. analysis는 다음 property categories 중 어떤 것이 applicable한지 식별해야 합니다.

| Category | Description | Example |
|---|---|---|
| Round-trip | inverse와 짝을 이룬 operation이 original value를 돌려줌 | serialize → deserialize = identity |
| Invariant | transformation이 측정 가능한 characteristic을 보존함 | sort preserves collection size and elements |
| Idempotence | operation을 두 번 적용해도 한 번 적용한 것과 같은 result를 냄 | dedup(dedup(list)) = dedup(list) |
| Commutativity | operation order가 달라도 같은 result를 냄 | add(a, b) = add(b, a) |
| Oracle | reference implementation 또는 simplified model로 results를 검증할 수 있음 | optimized algorithm vs brute-force |
| Induction | smaller inputs에서 증명된 property가 larger ones로 확장됨 | recursive structures, divide-and-conquer |
| Easy verification | result를 계산하기는 어렵지만 검증하기는 쉬움 | maze solver output can be walked to verify |

identified properties는 unit의 functional design artifacts에 문서화되어야 하며, code generation으로 PBT test requirements가 이어져야 합니다.

**Verification**:

- functional design artifacts에 component별 identified properties를 나열한 "Testable Properties" section이 포함됨
- 각 identified property가 위 categories 중 하나를 참조함
- identifiable properties가 없는 components는 brief rationale과 함께 "No PBT properties identified"로 명시됨
- property list가 code generation planning 중 참조됨

---

## Rule PBT-02: Round-Trip Properties

**Rule**: logical inverse가 있는 모든 operation은 round-trip을 검증하는 property-based test를 가져야 합니다. 여기에는 다음이 포함되지만 이에 한정되지 않습니다.

- Serialization / deserialization(JSON, XML, Protobuf, binary formats)
- Encoding / decoding(Base64, URL encoding, compression)
- Parsing / formatting(date parsing, number formatting, structured input을 사용하는 template rendering)
- Encryption / decryption(key를 사용할 수 있는 경우)
- Database write / read(data transformation layer 대상, I/O 자체는 제외)
- 모든 valid `x`에 대해 `f_inverse(f(x)) = x`가 성립하는 함수 쌍

property-based test는 domain-appropriate generator(PBT-07 참조)를 사용해 random valid inputs를 생성하고, round-trip이 original input과 equal한 value를 생성한다고 assert해야 합니다.

**Verification**:

- 모든 serialization/deserialization pair에 round-trip property test가 있음
- 모든 encoding/decoding pair에 round-trip property test가 있음
- 모든 parsing/formatting pair에 round-trip property test가 있음(또는 transformation이 lossy인 이유를 문서화)
- round-trip tests가 hardcoded examples가 아니라 generated inputs를 사용함
- lossy transformations(예: precision loss가 있는 float formatting)는 acceptable deviation을 문서화하고 tolerance 안에서 테스트함

---

## Rule PBT-03: Invariant Properties

**Rule**: documented invariants가 있는 functions는 generated inputs 전반에서 해당 invariants가 유지되는지 검증하는 property-based tests를 가져야 합니다. 일반적인 invariants는 다음과 같습니다.

- **Size preservation**: output collection이 input과 같은 size를 가짐(예: map, sort)
- **Element preservation**: output이 input과 정확히 같은 elements를 포함하며, 순서는 바뀔 수 있음(예: sort, shuffle)
- **Ordering guarantees**: output이 ordering constraint를 만족함(예: sort가 non-decreasing order를 생성)
- **Range constraints**: output values가 defined range 안에 있음(예: normalize가 [0, 1] 범위 values 생성)
- **Type preservation**: 모든 valid inputs에 대해 output type이 expected type과 일치함
- **Business rule invariants**: 항상 유지되어야 하는 domain-specific rules(예: "valid transaction 후 account balance는 음수가 되지 않는다", "discount는 item price를 초과하지 않는다")

**Verification**:

- 각 documented invariant에 corresponding property-based test가 있음
- invariant tests가 boundary values를 포함해 wide range of inputs를 생성함
- functional design에서 식별한 business rule invariants가 PBT로 covered됨
- invariant tests가 example-based tests의 exact assertions를 중복하지 않음. specific cases가 아니라 general rule을 테스트함

---

## Rule PBT-04: Idempotency Properties

**Rule**: idempotency를 주장하거나 요구하는 모든 operation은 이를 증명하는 property-based test를 가져야 합니다. test는 모든 valid generated inputs에 대해 `f(f(x)) = f(x)`를 검증해야 합니다. 이는 다음에 적용됩니다.

- idempotent로 문서화된 API endpoints(PUT, DELETE)
- data normalization 또는 sanitization functions
- cache population operations
- deduplication logic
- configuration application(config를 두 번 적용해도 state가 바뀌지 않아야 함)
- at-least-once delivery systems의 message processing

**Verification**:

- idempotent로 문서화된 모든 operation에 `f(f(x)) = f(x)`를 assert하는 PBT가 있음
- idempotency tests가 primitives만이 아니라 domain-appropriate generators를 사용함
- stateful operations의 경우 single application과 repeated application 후 observable state equivalence를 검증함

---

## Rule PBT-05: Oracle and Model-Based Testing

**Rule**: reference implementation, simplified model 또는 known-correct algorithm이 존재하면 property-based tests는 system under test를 oracle과 비교해야 합니다. 이는 다음에 적용됩니다.

- known brute-force version을 대체하는 optimized algorithms
- legacy implementations를 대체하는 refactored code
- sequential versions와 비교되는 parallel/concurrent implementations
- well-known algorithms(sorting, searching, graph traversal)의 custom implementations
- reference database와 비교되는 new query engines

property-based test는 random valid inputs를 생성하고, system under test가 모든 generated inputs에 대해 oracle과 equivalent results를 생성한다고 assert해야 합니다.

**Verification**:

- reference implementation이 존재하거나 쉽게 작성할 수 있으면 oracle PBT가 있음
- oracle tests가 normal, boundary, adversarial cases를 포함하는 diverse inputs를 생성함
- equivalence가 명확히 정의됨(exact equality, structural equality 또는 documented tolerance)
- oracle이 없으면 이 rule은 rationale과 함께 N/A로 표시됨

---

## Rule PBT-06: Stateful Property Testing

**Rule**: mutable state를 관리하는 components는 stateful property testing 대상인지 평가해야 합니다. Stateful PBT는 system에 대해 random sequences of commands(operations)를 생성하고 각 step 후 invariants가 유지되는지 검증합니다. 이는 다음에 적용됩니다.

- in-memory caches와 data stores
- state machines와 workflow engines
- queue와 buffer implementations
- session management systems
- shopping carts, order pipelines, 그와 유사한 stateful business objects
- operation의 result가 prior operations에 의존하는 모든 component

Stateful PBT는 다음을 수행해야 합니다.

- system under test를 반영하는 simplified model(reference state) 정의
- valid commands(add, remove, update, query 등)의 random sequences 생성
- 각 command를 real system과 model 모두에 대해 실행
- 각 command 후 system과 model의 observable state 또는 query results가 일치하는지 assert
- empty sequences를 포함해 다양한 길이의 sequences 테스트

**Verification**:

- functional design에서 식별된 stateful components가 stateful PBT를 갖거나 applicable하지 않은 이유를 문서화함
- comparison을 위한 simplified model이 정의됨
- command generators가 realistic parameter distributions를 가진 valid operation sequences를 생성함
- invariants가 sequence 끝에서만이 아니라 각 command 후 checked됨
- stateful components가 없으면 이 rule은 N/A로 표시됨

---

## Rule PBT-07: Generator Quality

**Rule**: property-based tests는 primitive types만이 아니라 realistic하고 structured inputs를 생성하는 domain-specific generators를 사용해야 합니다. poor generators(예: email fields에 random strings, age fields에 unbounded integers)는 의미 없는 test cases를 만들고 실제 bugs를 놓칩니다.

Generator requirements:

- **Domain types**: business constraints(valid email format, positive amounts, valid date ranges)를 존중하는 domain objects(예: User, Order, Transaction)용 custom generators를 만들어야 함
- **Constrained primitives**: domain이 요구하는 경우 numeric generators를 realistic ranges로 제한해야 함
- **Structured data**: complex inputs(nested objects, lists of domain objects)용 generators는 structurally valid data를 생성해야 함
- **Edge case inclusion**: generators는 normal values와 함께 boundary values(empty collections, zero, maximum values, Unicode strings)를 포함하도록 configured하는 것이 좋음
- **Reusability**: domain generators는 test files에 중복하지 말고 reusable test utilities로 정의하는 것이 좋음

**Verification**:

- 어떤 PBT도 domain-typed parameters에 raw primitive generators(예: `st.integers()` alone)만 사용하지 않음
- PBT에서 사용하는 domain objects용 custom generators가 존재함
- generators가 documented business constraints(예: positive amounts, valid formats)를 존중함
- 여러 tests가 같은 domain types를 공유하는 경우 generator definitions가 centralized and reusable함

---

## Rule PBT-08: Shrinking and Reproducibility

**Rule**: 모든 property-based tests는 shrinking과 deterministic reproducibility를 지원해야 합니다.

- **Shrinking**: property가 fail하면 PBT framework는 failing input을 minimal reproducing case로 자동 축소해야 합니다. documented technical reason이 없는 한 tests는 framework의 shrinking mechanism을 disable하거나 우회하면 안 됩니다(예: integration tests에서 shrinking이 external service calls와 incompatible한 경우).
- **Reproducibility**: 모든 PBT run은 seed value로 reproducible해야 합니다. exact failing scenario를 replay할 수 있도록 failure 시 seed를 logged해야 합니다. CI configurations는 deterministic runs를 위해 fixed seed를 사용하거나, failure 후 reproduction을 위해 모든 run에서 random seed를 logged해야 합니다.
- **CI integration**: PBT는 project의 CI pipeline에 포함되어야 합니다. flaky PBT failures(code changes 없이 retry하면 pass하는 tests)는 suppressed하지 말고 조사해야 합니다.

**Verification**:

- PBT framework의 shrinking이 enabled됨(overridden 또는 disabled되지 않음)
- failure 시 test output에 seed value와 shrunk minimal failing input이 포함됨
- CI configuration이 모든 PBT run의 seed를 logged하거나 fixed seed를 사용함
- documented justification 없이 CI에서 제외된 PBT가 없음
- flaky PBT failures가 silently retried되지 않고 tracked and investigated됨

---

## Rule PBT-09: Framework Selection

**Rule**: project는 primary language(s)에 적합한 property-based testing framework를 선택하고 configured해야 합니다. framework는 다음을 지원해야 합니다.

- domain types용 custom generators / strategies
- failing cases의 automatic shrinking
- seed-based reproducibility
- project의 existing test runner와 integration

language별 recommended frameworks(전체 목록 아님):

| Language | Framework | Notes |
|---|---|---|
| Python | Hypothesis | Mature, excellent shrinking, Django integration |
| JavaScript / TypeScript | fast-check | Jest, Vitest, Mocha와 integration |
| Java | jqwik | JUnit 5 integration, stateful testing support |
| Kotlin | Kotest Property Testing | Kotest framework integration |
| Scala | ScalaCheck | SBT integration, widely adopted |
| Rust | proptest | Macro-based, good shrinking |
| Go | rapid | Lightweight, idiomatic Go |
| Haskell | QuickCheck | original PBT framework |
| C# / .NET | FsCheck | xUnit, NUnit과 동작 |
| Erlang / Elixir | PropEr / StreamData | OTP-aware, stateful testing |

선택한 framework는 tech stack decisions에 문서화하고 project dependency로 포함해야 합니다.

**Verification**:

- PBT framework가 선택되어 tech stack decisions에 문서화됨
- framework가 project dependencies(package.json, pom.xml, requirements.txt 등)에 포함됨
- framework가 custom generators, shrinking, seed-based reproducibility를 지원함
- project가 여러 languages를 사용한다면 PBT-applicable code가 있는 각 language에 framework가 선택됨

---

## Rule PBT-10: Complementary Testing Strategy

**Rule**: property-based tests는 example-based tests를 대체하지 않고 보완해야 합니다. 두 접근은 서로 다른 목적을 수행합니다.

- **Example-based tests**: specific known scenarios, regression cases, business-critical edge cases를 explicit expected values와 함께 문서화합니다. concrete behavior의 executable documentation 역할을 합니다.
- **Property-based tests**: wide input space에 걸쳐 general invariants를 검증합니다. unknown edge cases를 찾고 properties가 보편적으로 유지되는지 validate합니다.

Requirements:

- user stories 또는 requirements에서 식별한 critical business scenarios는 같은 property를 PBT가 cover하더라도 explicit example-based tests를 가져야 합니다.
- PBT는 business-critical path의 sole test가 되면 안 됩니다. key scenarios의 expected behavior를 고정하는 example-based test가 최소 하나 있어야 합니다.
- PBT가 failing case를 발견하면 shrunk minimal example을 permanent example-based regression test로 추가하는 것이 좋습니다.
- test documentation은 example-based와 property-based tests를 명확히 구분해야 합니다(separate test files, test classes 또는 clearly named test functions).

**Verification**:

- business-critical paths에 example-based와 property-based tests가 모두 있음
- PBT가 critical feature의 유일한 test coverage로 사용되지 않음
- test files 또는 test classes가 PBT와 example-based tests를 명확히 separate하거나 label함
- PBT-discovered failures에서 나온 regression tests가 permanent example-based tests로 captured됨

---

## Enforcement Integration

이 rules는 다음 AI-DLC stages에 적용되는 cross-cutting constraints입니다.

| Stage | Applicable Rules | Enforcement |
|---|---|---|
| Functional Design | PBT-01 | property identification이 design artifacts에 나타나야 함 |
| NFR Requirements | PBT-09 | framework selection이 tech stack decisions에 포함되어야 함 |
| Code Generation (Planning) | PBT-01 through PBT-10 | code generation plan이 identified properties용 PBT test steps를 포함해야 함 |
| Code Generation (Generation) | PBT-02 through PBT-08, PBT-10 | generated tests가 example-based tests와 함께 PBT를 포함해야 함 |
| Build and Test | PBT-08 | test execution instructions가 seed logging 및 CI integration과 함께 PBT를 포함해야 함 |

각 applicable stage에서 다음을 수행합니다.

- 생성된 artifacts에 대해 모든 PBT rule verification criteria를 평가합니다.
- stage completion summary에 "PBT Compliance" section을 포함하고 각 rule을 compliant, non-compliant 또는 N/A로 나열합니다.
- rule이 하나라도 non-compliant이면 blocking PBT finding입니다. Overview에 정의된 blocking finding behavior를 따르세요.
- design documentation과 test instructions에 PBT rule references를 포함합니다.

---

## Appendix: Property Category Quick Reference

Functional Design(PBT-01) 중 properties를 식별하는 developers와 AI models를 위한 참고입니다.

| Pattern Name | Formal Term | Test Shape | When to Use |
|---|---|---|---|
| There and back again | Invertible function | `f_inv(f(x)) == x` | Serialization, encoding, parsing |
| Some things never change | Invariant | `measure(f(x)) == measure(x)` | Sort, map, filter, transform |
| The more things change, the more they stay the same | Idempotence | `f(f(x)) == f(x)` | Normalization, dedup, cache writes |
| Different paths, same destination | Commutativity | `f(g(x)) == g(f(x))` | Arithmetic, set operations, independent transforms |
| Solve a smaller problem first | Structural induction | Property on `x` implies property on `x + element` | Recursive structures, lists, trees |
| Hard to prove, easy to verify | Verification | `verify(solve(x)) == true` | Solvers, optimizers, search algorithms |
| The test oracle | Reference comparison | `f(x) == oracle(x)` | Optimized vs brute-force, refactored vs legacy |

Source: Property category taxonomy는 Scott Wlaschin의 "Choosing properties for property-based testing"([fsharpforfunandprofit.com](https://fsharpforfunandprofit.com/posts/property-based-testing-2/))에서 조정했습니다.
