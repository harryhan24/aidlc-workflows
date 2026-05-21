# Build and Test

**목적**: 모든 단위(unit)를 빌드하고 종합적인 테스트 전략을 실행합니다.

## Prerequisites
- 모든 단위에 대해 Code Generation이 완료되어 있어야 합니다.
- 모든 코드 아티팩트가 생성되어 있어야 합니다.
- 프로젝트가 빌드 및 테스트 준비 상태여야 합니다.

---

## Step 1: 테스트 요구사항 분석

프로젝트를 분석하여 적절한 테스트 전략을 결정하십시오.
- **Unit tests**: 코드 생성 단계에서 단위별로 이미 생성됨
- **Integration tests**: 단위/서비스 간 상호작용 테스트
- **Performance tests**: 부하, 스트레스, 확장성 테스트
- **End-to-end tests**: 완전한 사용자 워크플로우
- **Contract tests**: 서비스 간 API 계약 검증
- **Security tests**: 취약점 스캐닝, 침투 테스트

---

## Step 2: 빌드 지침 생성

`aidlc-docs/construction/build-and-test/build-instructions.md`를 생성합니다.

```markdown
# Build Instructions

## Prerequisites
- **Build Tool**: [Tool name and version]
- **Dependencies**: [List all required dependencies]
- **Environment Variables**: [List required env vars]
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
# 예: export 변수 설정, 자격 증명 구성
\`\`\`

### 3. Build All Units
\`\`\`bash
[Command to build all units]
# 예: mvn clean install, npm run build, brazil-build
\`\`\`

### 4. Verify Build Success
- **Expected Output**: [Describe successful build output]
- **Build Artifacts**: [List generated artifacts and locations]
- **Common Warnings**: [Note any acceptable warnings]

## Troubleshooting

### Build Fails with Dependency Errors
- **Cause**: [Common causes]
- **Solution**: [Step-by-step fix]

### Build Fails with Compilation Errors
- **Cause**: [Common causes]
- **Solution**: [Step-by-step fix]
```

---

## Step 3: 유닛 테스트 실행 지침 생성

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
- **Test Coverage**: [Expected coverage percentage]
- **Test Report Location**: [Path to test reports]

### 3. Fix Failing Tests
테스트가 실패하면:
1. [location]에서 테스트 출력 확인
2. 실패한 테스트 케이스 식별
3. 코드 이슈 수정
4. 모두 통과할 때까지 테스트 재실행
```

---

## Step 4: 통합 테스트 지침 생성

`aidlc-docs/construction/build-and-test/integration-test-instructions.md`를 생성합니다.

```markdown
# Integration Test Instructions

## Purpose
단위/서비스 간 상호작용을 테스트하여 함께 올바르게 동작하는지 검증합니다.

## Test Scenarios

### Scenario 1: [Unit A] → [Unit B] Integration
- **Description**: [What is being tested]
- **Setup**: [Required test environment setup]
- **Test Steps**: [Step-by-step test execution]
- **Expected Results**: [What should happen]
- **Cleanup**: [How to clean up after test]

### Scenario 2: [Unit B] → [Unit C] Integration
[Similar structure]

## Setup Integration Test Environment

### 1. Start Required Services
\`\`\`bash
[Commands to start services]
# 예: docker-compose up, 테스트 데이터베이스 시작
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
- **Test Scenarios**: [List key integration test scenarios]
- **Expected Results**: [Describe expected outcomes]
- **Logs Location**: [Where to check logs]

### 3. Cleanup
\`\`\`bash
[Commands to clean up test environment]
# 예: docker-compose down, 테스트 서비스 중지
\`\`\`
```

---

## Step 5: 성능 테스트 지침 생성 (해당되는 경우)

`aidlc-docs/construction/build-and-test/performance-test-instructions.md`를 생성합니다.

```markdown
# Performance Test Instructions

## Purpose
요구사항을 충족하는지 확인하기 위해 부하 상황에서 시스템 성능을 검증합니다.

## Performance Requirements
- **Response Time**: < [X]ms for [Y]% of requests
- **Throughput**: [X] requests/second
- **Concurrent Users**: Support [X] concurrent users
- **Error Rate**: < [X]%

## Setup Performance Test Environment

### 1. Prepare Test Environment
\`\`\`bash
[Commands to set up performance testing]
# 예: 서비스 확장, 로드 밸런서 구성
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
# 예: 실패할 때까지 부하를 점진적으로 증가
\`\`\`

### 3. Analyze Performance Results
- **Response Time**: [Actual vs Expected]
- **Throughput**: [Actual vs Expected]
- **Error Rate**: [Actual vs Expected]
- **Bottlenecks**: [Identified bottlenecks]
- **Results Location**: [Path to performance reports]

## Performance Optimization

성능이 요구사항을 충족하지 못하면:
1. 테스트 결과에서 병목 식별
2. 코드/쿼리/구성 최적화
3. 개선 여부 검증을 위해 테스트 재실행
```

---

## Step 6: 추가 테스트 지침 생성 (필요 시)

프로젝트 요구사항에 따라 추가 테스트 지침 파일을 생성하십시오.

### Contract Tests (마이크로서비스용)
`aidlc-docs/construction/build-and-test/contract-test-instructions.md`를 생성합니다.
- 서비스 간 API 계약 검증
- Consumer-driven contract testing
- 스키마 검증

### Security Tests
`aidlc-docs/construction/build-and-test/security-test-instructions.md`를 생성합니다.
- 취약점 스캐닝
- 의존성 보안 점검
- 인증/인가 테스트
- 입력 검증 테스트

### End-to-End Tests
`aidlc-docs/construction/build-and-test/e2e-test-instructions.md`를 생성합니다.
- 완전한 사용자 워크플로우 테스트
- 서비스 간 시나리오
- UI 테스트 (해당되는 경우)

---

## Step 7: 테스트 요약 생성

`aidlc-docs/construction/build-and-test/build-and-test-summary.md`를 생성합니다.

```markdown
# Build and Test Summary

## Build Status
- **Build Tool**: [Tool name]
- **Build Status**: [Success/Failed]
- **Build Artifacts**: [List artifacts]
- **Build Time**: [Duration]

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
- **Response Time**: [Actual] (Target: [Expected])
- **Throughput**: [Actual] (Target: [Expected])
- **Error Rate**: [Actual] (Target: [Expected])
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
[If all pass]: Operations 단계로 진행하여 배포 계획을 수립할 준비 완료
[If failures]: 실패한 테스트를 해결하고 재빌드
```

---

## Step 8: 상태 추적 업데이트

`aidlc-docs/aidlc-state.md`를 업데이트하십시오.
- Build and Test 스테이지를 완료로 표시
- 현재 상태(current status) 업데이트

---

## Step 9: 사용자에게 결과 제시

다음 구조로 완료 메시지를 제시합니다.
     1. **Completion Announcement** (mandatory): 항상 다음으로 시작합니다.

```markdown
# 🔨 Build and Test Complete
```

     2. **AI Summary** (optional): 빌드 및 테스트 결과를 구조화된 글머리 기호 요약으로 제공합니다.
        - 형식: "Build and test has completed with the following results:"
        - 빌드 상태 및 아티팩트 나열
        - 카테고리(unit, integration, performance 등)별 테스트 결과 나열
        - 생성된 지침 파일 나열
        - 워크플로우 지시문("please review", "let me know", "proceed to next phase", "before we proceed")은 포함하지 않을 것
        - 사실 위주, 내용 중심으로 유지
     3. **Formatted Workflow Message** (mandatory): 항상 다음 형식으로 정확히 종료합니다.

```markdown
> **📋 <u>**REVIEW REQUIRED:**</u>**  
> Please examine the build and test summary at: `aidlc-docs/construction/build-and-test/build-and-test-summary.md`



> **🚀 <u>**WHAT'S NEXT?**</u>**
>
> **You may:**
>
> 🔧 **Request Changes** - Ask for modifications to the build and test instructions based on your review
> ✅ **Approve & Continue** - Approve build and test results and proceed to **Operations**

---
```

---

## Step 10: 상호작용 로깅

**MANDATORY**: 스테이지 완료를 `aidlc-docs/audit.md`에 기록합니다.

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
