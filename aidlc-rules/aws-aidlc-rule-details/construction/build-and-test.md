# Build and Test

**Purpose**: 모든 units를 build하고 comprehensive testing strategy를 실행합니다.

## Prerequisites

- Code Generation이 모든 units에 대해 완료되어야 합니다.
- 모든 code artifacts가 생성되어야 합니다.
- project가 build and testing 준비 상태여야 합니다.

---

## Step 1: Analyze Testing Requirements

project를 분석해 적절한 testing strategy를 결정합니다.

- **Unit tests**: code generation 중 unit별로 이미 생성됨
- **Integration tests**: units/services 사이의 interactions 테스트
- **Performance tests**: load, stress, scalability testing
- **End-to-end tests**: complete user workflows
- **Contract tests**: services 사이의 API contract validation
- **Security tests**: vulnerability scanning, penetration testing

---

## Step 2: Generate Build Instructions

`aidlc-docs/construction/build-and-test/build-instructions.md`를 생성합니다.

```markdown
# Build Instructions

## Prerequisites

- **Build Tool**: [tool name and version]
- **Dependencies**: [required dependencies 전체 목록]
- **Environment Variables**: [required env vars 목록]
- **System Requirements**: [OS, memory, disk space]

## Build Steps

### 1. Install Dependencies

\`\`\`bash
[Command to install dependencies]
# 예: npm install, mvn dependency:resolve, pip install -r requirements.txt
\`\`\`

### 2. Configure Environment

\`\`\`bash
[Commands to set up environment]
# 예: variables export, credentials configure
\`\`\`

### 3. Build All Units

\`\`\`bash
[Command to build all units]
# 예: mvn clean install, npm run build, brazil-build
\`\`\`

### 4. Verify Build Success

- **Expected Output**: [successful build output 설명]
- **Build Artifacts**: [generated artifacts and locations 목록]
- **Common Warnings**: [허용 가능한 warnings 기록]

## Troubleshooting

### Build Fails with Dependency Errors

- **Cause**: [common causes]
- **Solution**: [step-by-step fix]

### Build Fails with Compilation Errors

- **Cause**: [common causes]
- **Solution**: [step-by-step fix]
```

---

## Step 3: Generate Unit Test Execution Instructions

`aidlc-docs/construction/build-and-test/unit-test-instructions.md`를 생성합니다.

```markdown
# Unit Test Execution

## Run Unit Tests

### 1. Execute All Unit Tests

\`\`\`bash
[Command to run all unit tests]
# 예: mvn test, npm test, pytest tests/unit
\`\`\`

### 2. Review Test Results

- **Expected**: [X] tests pass, 0 failures
- **Test Coverage**: [expected coverage percentage]
- **Test Report Location**: [test reports path]

### 3. Fix Failing Tests

tests가 실패하면:

1. [location]의 test output을 검토합니다.
2. failing test cases를 식별합니다.
3. code issues를 수정합니다.
4. 모든 tests가 pass할 때까지 다시 실행합니다.
```

---

## Step 4: Generate Integration Test Instructions

`aidlc-docs/construction/build-and-test/integration-test-instructions.md`를 생성합니다.

```markdown
# Integration Test Instructions

## Purpose

units/services가 함께 올바르게 동작하는지 확인하기 위해 interactions를 테스트합니다.

## Test Scenarios

### Scenario 1: [Unit A] → [Unit B] Integration

- **Description**: [test 대상 설명]
- **Setup**: [required test environment setup]
- **Test Steps**: [step-by-step test execution]
- **Expected Results**: [예상 동작]
- **Cleanup**: [test 후 cleanup 방법]

### Scenario 2: [Unit B] → [Unit C] Integration

[Similar structure]

## Setup Integration Test Environment

### 1. Start Required Services

\`\`\`bash
[Commands to start services]
# 예: docker-compose up, test database 시작
\`\`\`

### 2. Configure Service Endpoints

\`\`\`bash
[Commands to configure endpoints]
# 예: export API_URL=http://localhost:8080
\`\`\`

## Run Integration Tests

### 1. Execute Integration Test Suite

\`\`\`bash
[Command to run integration tests]
# 예: mvn integration-test, npm run test:integration
\`\`\`

### 2. Verify Service Interactions

- **Test Scenarios**: [key integration test scenarios 목록]
- **Expected Results**: [expected outcomes 설명]
- **Logs Location**: [logs 확인 위치]

### 3. Cleanup

\`\`\`bash
[Commands to clean up test environment]
# 예: docker-compose down, test services 중지
\`\`\`
```

---

## Step 5: Generate Performance Test Instructions (If Applicable)

`aidlc-docs/construction/build-and-test/performance-test-instructions.md`를 생성합니다.

```markdown
# Performance Test Instructions

## Purpose

system performance가 requirements를 충족하는지 확인하기 위해 load 상태에서 검증합니다.

## Performance Requirements

- **Response Time**: requests 중 [Y]%에 대해 < [X]ms
- **Throughput**: [X] requests/second
- **Concurrent Users**: [X] concurrent users 지원
- **Error Rate**: < [X]%

## Setup Performance Test Environment

### 1. Prepare Test Environment

\`\`\`bash
[Commands to set up performance testing]
# 예: services scale, load balancers configure
\`\`\`

### 2. Configure Test Parameters

- **Test Duration**: [X] minutes
- **Ramp-up Time**: [X] seconds
- **Virtual Users**: [X] users

## Run Performance Tests

### 1. Execute Load Tests

\`\`\`bash
[Command to run load tests]
# 예: jmeter -n -t test.jmx, k6 run script.js
\`\`\`

### 2. Execute Stress Tests

\`\`\`bash
[Command to run stress tests]
# 예: failure가 발생할 때까지 load를 점진적으로 증가
\`\`\`

### 3. Analyze Performance Results

- **Response Time**: [actual vs expected]
- **Throughput**: [actual vs expected]
- **Error Rate**: [actual vs expected]
- **Bottlenecks**: [identified bottlenecks]
- **Results Location**: [performance reports path]

## Performance Optimization

performance가 requirements를 충족하지 못하면:

1. test results에서 bottlenecks를 식별합니다.
2. code/queries/configurations를 최적화합니다.
3. improvements를 검증하기 위해 tests를 다시 실행합니다.
```

---

## Step 6: Generate Additional Test Instructions (As Needed)

project requirements를 기준으로 additional test instruction files를 생성합니다.

### Contract Tests (For Microservices)

`aidlc-docs/construction/build-and-test/contract-test-instructions.md`를 생성합니다.

- services 사이의 API contract validation
- consumer-driven contract testing
- schema validation

### Security Tests

`aidlc-docs/construction/build-and-test/security-test-instructions.md`를 생성합니다.

- vulnerability scanning
- dependency security checks
- authentication/authorization testing
- input validation testing

### End-to-End Tests

`aidlc-docs/construction/build-and-test/e2e-test-instructions.md`를 생성합니다.

- complete user workflow testing
- cross-service scenarios
- UI testing(applicable한 경우)

---

## Step 7: Generate Test Summary

`aidlc-docs/construction/build-and-test/build-and-test-summary.md`를 생성합니다.

```markdown
# Build and Test Summary

## Build Status

- **Build Tool**: [tool name]
- **Build Status**: [Success/Failed]
- **Build Artifacts**: [artifacts 목록]
- **Build Time**: [duration]

## Test Execution Summary

### Unit Tests

- **Total Tests**: [X]
- **Passed**: [X]
- **Failed**: [X]
- **Coverage**: [X]%
- **Status**: [Pass/Fail]

### Integration Tests

- **Test Scenarios**: [X]
- **Passed**: [X]
- **Failed**: [X]
- **Status**: [Pass/Fail]

### Performance Tests

- **Response Time**: [actual] (Target: [expected])
- **Throughput**: [actual] (Target: [expected])
- **Error Rate**: [actual] (Target: [expected])
- **Status**: [Pass/Fail]

### Additional Tests

- **Contract Tests**: [Pass/Fail/N/A]
- **Security Tests**: [Pass/Fail/N/A]
- **E2E Tests**: [Pass/Fail/N/A]

## Overall Status

- **Build**: [Success/Failed]
- **All Tests**: [Pass/Fail]
- **Ready for Operations**: [Yes/No]

## Next Steps

[If all pass]: deployment planning을 위해 Operations phase로 진행할 준비가 되었습니다.
[If failures]: failing tests를 해결하고 다시 build합니다.
```

---

## Step 8: Update State Tracking

`aidlc-docs/aidlc-state.md`를 업데이트합니다.

- Build and Test stage를 complete로 표시합니다.
- current status를 업데이트합니다.

---

## Step 9: Present Results to User

다음 구조로 completion message를 제시합니다.
     1. **Completion Announcement**(mandatory): 항상 다음으로 시작합니다.

```markdown
# 🔨 Build and Test Complete
```

     2. **AI Summary**(optional): build and test results에 대한 structured bullet-point summary를 제공합니다.
        - Format: "Build and test has completed with the following results:"
        - build status와 artifacts를 나열합니다.
        - category별 test results를 나열합니다(unit, integration, performance 등).
        - generated instruction files를 나열합니다.
        - workflow instructions("please review", "let me know", "proceed to next phase", "before we proceed")를 포함하지 마세요.
        - factual하고 content-focused하게 유지합니다.
     3. **Formatted Workflow Message**(mandatory): 항상 다음 exact format으로 끝냅니다.

```markdown
> **📋 <u>**REVIEW REQUIRED:**</u>**  
> build and test summary를 검토해 주세요: `aidlc-docs/construction/build-and-test/build-and-test-summary.md`



> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - 검토 결과를 바탕으로 build and test instructions 수정을 요청하세요
> ✅ **Approve & Continue** - build and test results를 승인하고 **Operations**로 진행합니다.

---
```

---

## Step 10: Log Interaction

**MANDATORY**: stage completion을 `aidlc-docs/audit.md`에 기록합니다.

```markdown
## Build and Test Stage

**Timestamp**: [ISO timestamp]
**Build Status**: [Success/Failed]
**Test Status**: [Pass/Fail]
**Files Generated**:

- build-instructions.md
- unit-test-instructions.md
- integration-test-instructions.md
- performance-test-instructions.md
- build-and-test-summary.md

---
```
