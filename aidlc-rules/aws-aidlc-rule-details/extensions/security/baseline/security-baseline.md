# Baseline Security Rules

## Overview

이 security rules는 모든 AI-DLC phases에 걸쳐 적용되는 MANDATORY cross-cutting constraints입니다. 선택적 guidance가 아니라, questions 생성, design artifacts 작성, code 생성, completion messages 제시 시 stages가 반드시 enforce해야 하는 hard constraints입니다.

**Enforcement**: 각 applicable stage에서 model은 stage completion message를 사용자에게 제시하기 전에 이 rules의 compliance를 반드시 검증해야 합니다.

### Blocking Security Finding Behavior

**blocking security finding**은 다음을 의미합니다.

1. 해당 finding은 stage completion message의 "Security Findings" section에 SECURITY rule ID와 description과 함께 반드시 나열해야 합니다.
2. stage는 모든 blocking findings가 해결될 때까지 "Continue to Next Stage" option을 제시하면 안 됩니다.
3. model은 변경이 필요한 내용을 명확히 설명하면서 "Request Changes" option만 제시해야 합니다.
4. finding은 SECURITY rule ID, description, stage context와 함께 `aidlc-docs/audit.md`에 반드시 기록해야 합니다.

SECURITY rule이 current project에 applicable하지 않다면(예: data stores가 없을 때 SECURITY-01), compliance summary에서 **N/A**로 표시합니다. 이것은 blocking finding이 아닙니다.

### Default Enforcement

이 문서의 모든 rules는 기본적으로 **blocking**입니다. rule의 verification criteria가 충족되지 않으면 blocking security finding입니다. 위에서 정의한 blocking finding behavior를 따르세요.

### Verification Criteria Format

이 문서의 verification items는 compliance checks를 설명하는 일반 bullet points입니다. stage plan files에서 progress tracking에 사용하는 `- [ ]` / `- [x]` checkboxes와는 다릅니다. 각 item은 review 중 compliant 또는 non-compliant로 평가해야 합니다.

---

## Rule SECURITY-01: Encryption at Rest and in Transit

**Rule**: 모든 data persistence store(databases, object storage, file systems, caches 또는 그와 동등한 것)는 다음을 반드시 갖춰야 합니다.

- managed key service 또는 customer-managed keys를 사용한 encryption at rest enabled
- store 안팎으로 이동하는 모든 data에 대해 encryption in transit 강제(TLS 1.2+)

**Verification**:

- encryption configuration block 없이 정의된 storage resource가 없음
- unencrypted protocol을 사용하는 database connection string이 없음
- object storage가 encryption at rest를 강제하고 policy로 non-TLS requests를 거부함
- database instances가 storage encryption을 enabled하고 TLS connections를 강제함

---

## Rule SECURITY-02: Access Logging on Network Intermediaries

**Rule**: external traffic을 처리하는 모든 network-facing intermediary는 access logging을 enabled해야 합니다. 여기에는 다음이 포함됩니다.

- Load balancers: access logs를 persistent store에 기록
- API gateways: execution logging과 access logging을 centralized log service에 기록
- CDN distributions: standard logging 또는 real-time logs

**Verification**:

- access logging enabled 없이 정의된 load balancer resource가 없음
- access logging configured 없이 정의된 API gateway stage가 없음
- logging configuration 없이 정의된 CDN distribution이 없음

---

## Rule SECURITY-03: Application-Level Logging

**Rule**: 배포되는 모든 application component는 structured logging infrastructure를 포함해야 합니다.

- logging framework가 반드시 configured되어야 함
- log output은 centralized log service로 전달되어야 함
- logs에는 timestamp, correlation/request ID, log level, message가 포함되어야 함
- sensitive data(passwords, tokens, PII)는 log output에 나타나면 안 됨

**Verification**:

- 모든 service/function entry point에 configured logger가 포함됨
- production code의 primary logging mechanism으로 ad-hoc logging statements를 사용하지 않음
- log configuration이 output을 centralized log service로 routing함
- secrets, tokens, PII가 logging되지 않음

---

## Rule SECURITY-04: HTTP Security Headers for Web Applications

**Rule**: HTML을 제공하는 모든 endpoints에는 다음 HTTP response headers를 설정해야 합니다.

| Header | Required Value |
|---|---|
| `Content-Security-Policy` | restrictive policy 정의(최소: `default-src 'self'`) |
| `Strict-Transport-Security` | `max-age=31536000; includeSubDomains` |
| `X-Content-Type-Options` | `nosniff` |
| `X-Frame-Options` | `DENY`(framing이 필요한 경우 `SAMEORIGIN`) |
| `Referrer-Policy` | `strict-origin-when-cross-origin` |

**Note**: `X-XSS-Protection`은 modern browsers에서 deprecated되었습니다. 대신 `Content-Security-Policy`를 사용하세요.

**Verification**:

- middleware 또는 response interceptor가 모든 required headers를 설정함
- CSP policy가 documented justification 없이 `unsafe-inline` 또는 `unsafe-eval`을 사용하지 않음
- HSTS max-age가 최소 31536000(1년)임

---

## Rule SECURITY-05: Input Validation on All API Parameters

**Rule**: 모든 API endpoint(REST, GraphQL, gRPC, WebSocket)는 processing 전에 모든 input parameters를 validate해야 합니다. validation에는 다음이 포함되어야 합니다.

- **Type checking**: 예상하지 못한 types 거부
- **Length/size bounds**: strings의 maximum lengths, arrays와 payloads의 maximum sizes 강제
- **Format validation**: structured inputs(emails, dates, IDs)에 allowlists(regex 또는 schema) 사용
- **Sanitization**: XSS 방지를 위해 user-supplied strings의 HTML/script content를 escape하거나 거부
- **Injection prevention**: 모든 database operations에 parameterized queries 사용(절대 string concatenation 사용 금지)

**Verification**:

- 모든 API handler가 validation library 또는 schema를 사용함
- raw user input이 SQL, NoSQL 또는 OS commands에 concatenated되지 않음
- string inputs에 explicit max-length constraints가 있음
- request body size limits가 framework 또는 gateway level에서 configured됨

---

## Rule SECURITY-06: Least-Privilege Access Policies

**Rule**: 모든 identity and access management policy, role 또는 permission boundary는 least privilege를 따라야 합니다.

- specific resource identifiers를 사용합니다. API가 resource-level permissions를 지원하지 않는 경우가 아니라면 wildcard resources를 절대 사용하지 마세요(예외는 문서화).
- specific actions를 사용합니다. wildcard actions를 절대 사용하지 마세요.
- 가능한 곳에서는 conditions를 scope합니다.
- read와 write permissions를 별도의 policy statements로 분리합니다.

**Verification**:

- documented exception 없이 wildcard actions 또는 wildcard resources를 포함한 policy가 없음
- service role이 service가 실제 호출하는 것보다 더 넓은 permissions를 갖지 않음
- 가능한 경우 inline policies 대신 managed policies를 사용함
- 모든 role이 specific service 또는 account로 scoped된 trust policy를 가짐

---

## Rule SECURITY-07: Restrictive Network Configuration

**Rule**: 모든 network configurations(security groups, network ACLs, route tables)는 deny-by-default를 따라야 합니다.

- Firewall rules: application에 필요한 specific ports만 open
- ports 80/443의 public-facing load balancers를 제외하고 source `0.0.0.0/0` inbound rule 금지
- 명시적 justification 없이 모든 ports에 대해 `0.0.0.0/0` outbound rule 금지
- Private subnets에는 direct internet gateway routes가 있으면 안 됨
- 가능한 경우 cloud service access에 private endpoints 사용

**Verification**:

- public load balancer의 80/443 외에는 어떤 port에서도 inbound `0.0.0.0/0`를 허용하는 firewall rule이 없음
- database와 application firewall rules가 source를 specific CIDR blocks 또는 security group references로 제한함
- private subnets가 internet gateway가 아니라 NAT gateway를 통해 route됨
- high-traffic cloud service calls에 private endpoints를 사용함

---

## Rule SECURITY-08: Application-Level Access Control

**Rule**: resource에 접근하거나 resource를 변경하는 모든 application endpoint는 application layer에서 authorization checks를 강제해야 합니다.

- **Deny by default**: public으로 명시된 경우를 제외하고 모든 routes/endpoints는 authentication을 요구해야 함
- **Object-level authorization**: ID로 resource를 참조하는 모든 request는 requesting user/principal이 해당 resource를 소유하거나 접근 권한을 가졌는지 검증해야 함(IDOR 방지)
- **Function-level authorization**: administrative 또는 privileged operations는 caller의 role/permissions를 server-side에서 확인해야 함. client-side hiding에 의존하지 마세요.
- **CORS policy**: Cross-origin resource sharing은 명시적으로 허용된 origins로 제한되어야 함. authenticated endpoints에서 `Access-Control-Allow-Origin: *`를 절대 사용하지 마세요.
- **Token validation**: JWTs 또는 session tokens는 모든 request에서 server-side로 validate해야 함(signature, expiration, audience, issuer)

**Verification**:

- 모든 controller/handler에 authorization middleware 또는 guard가 적용됨
- caller의 ownership 또는 permission 검증 없이 resource ID에 대한 data를 반환하는 endpoint가 없음
- admin/privileged routes에 explicit role checks가 server-side에서 강제됨
- CORS configuration이 authenticated endpoints에서 wildcard origins를 사용하지 않음
- token validation이 login 시점뿐 아니라 모든 request에서 server-side로 수행됨

---

## Rule SECURITY-09: Security Hardening and Misconfiguration Prevention

**Rule**: 배포되는 모든 components는 hardening baseline을 따라야 합니다.

- **No default credentials**: deployment 전에 default usernames/passwords를 변경하거나 disabled해야 함
- **Minimal installation**: 사용하지 않는 features, frameworks, sample applications, documentation endpoints를 제거하거나 disabled함
- **Error handling**: production error responses는 stack traces, internal paths, framework versions 또는 database details를 end users에게 노출하면 안 됨
- **Directory listing**: web servers는 directory listing을 disabled해야 함
- **Cloud storage**: cloud object storage는 명시적으로 필요하고 문서화된 경우가 아니라면 public access를 block해야 함
- **Patch management**: runtime environments, frameworks, OS images는 current, supported versions를 사용해야 함

**Verification**:

- configuration files, environment variables 또는 IaC templates에 default credentials가 없음
- production의 error responses가 generic messages를 반환함(stack traces 또는 internal details 없음)
- documented exception이 없는 한 cloud object storage가 public access를 blocked함
- sample/demo applications 또는 default pages가 배포되지 않음
- framework와 runtime versions가 current and supported 상태임

---

## Rule SECURITY-10: Software Supply Chain Security

**Rule**: 모든 project는 software supply chain을 관리해야 합니다.

- **Dependency pinning**: 모든 dependencies는 exact versions 또는 lock files를 사용해야 함
- **Vulnerability scanning**: dependency vulnerability scanner가 configured되어야 함
- **No unused dependencies**: active하게 사용하지 않는 packages 제거
- **Trusted sources only**: dependencies는 official registries 또는 verified private registries에서 가져와야 함. 검증되지 않은 third-party sources 금지
- **SBOM**: projects는 production deployments를 위해 Software Bill of Materials를 생성해야 함
- **CI/CD integrity**: build pipelines는 pinned tool versions와 verified base images를 사용해야 함. production Dockerfiles 또는 CI configurations에서 `latest` tags 금지

**Verification**:

- lock file이 존재하고 version control에 committed됨
- dependency vulnerability scanning step이 CI/CD에 포함되거나 build instructions에 문서화됨
- unused 또는 abandoned dependencies가 포함되지 않음
- production용 Dockerfiles와 CI configs가 `latest` 또는 unpinned image tags를 사용하지 않음
- dependencies가 official 또는 verified registries에서 sourced됨

---

## Rule SECURITY-11: Secure Design Principles

**Rule**: application design은 시작부터 security를 포함해야 합니다.

- **Separation of concerns**: security-critical logic(authentication, authorization, payment processing)은 dedicated modules에 isolated되어야 함. codebase 전체에 흩어져 있으면 안 됨
- **Defense in depth**: 하나의 control이 sole line of defense가 되면 안 됨. controls를 layered합니다(validation + authorization + encryption).
- **Rate limiting**: public-facing endpoints는 abuse 방지를 위해 rate limiting 또는 throttling을 구현해야 함
- **Business logic abuse**: design은 happy-path scenarios뿐 아니라 misuse cases도 고려해야 함

**Verification**:

- security-critical logic이 dedicated modules 또는 services에 encapsulated됨
- public-facing APIs에 rate limiting이 configured됨
- design documentation이 최소 하나의 misuse/abuse scenario를 다룸

---

## Rule SECURITY-12: Authentication and Credential Management

**Rule**: user authentication이 있는 모든 application은 다음을 구현해야 합니다.

- **Password policy**: 최소 8자, breached password lists와 대조
- **Credential storage**: passwords는 adaptive algorithms로 hashed되어야 함. weak 또는 non-adaptive hashing 금지
- **Multi-factor authentication**: MFA는 administrative accounts에 대해 지원되어야 하며 모든 users에게도 제공하는 것이 좋음
- **Session management**: sessions는 server-side expiration을 갖고, logout 시 invalidated되어야 하며 secure/httpOnly/sameSite cookie attributes를 사용해야 함
- **Brute-force protection**: login endpoints는 repeated failures 후 account lockout, progressive delays 또는 CAPTCHA를 구현해야 함
- **No hardcoded credentials**: source code 또는 IaC templates에 passwords, API keys, secrets 금지. secrets manager 사용

**Verification**:

- password hashing이 adaptive algorithms를 사용함(weak 또는 non-adaptive hashing 아님)
- session cookies가 `Secure`, `HttpOnly`, `SameSite` attributes를 설정함
- login endpoints에 brute-force protection(lockout, delay 또는 CAPTCHA)이 있음
- source code 또는 configuration files에 hardcoded credentials가 없음
- admin accounts에 MFA가 supported됨
- sessions가 logout 시 invalidated되고 defined expiration을 가짐

---

## Rule SECURITY-13: Software and Data Integrity Verification

**Rule**: systems는 software와 data의 integrity를 검증해야 합니다.

- **Deserialization safety**: untrusted data는 validation 없이 deserialized되면 안 됨. safe deserialization libraries 또는 permitted types allowlists 사용
- **Artifact integrity**: downloaded dependencies, plugins, updates는 checksums 또는 digital signatures로 verified되어야 함
- **CI/CD pipeline security**: build pipelines는 pipeline definitions를 수정할 수 있는 주체를 제한해야 함. code authors와 deployment approvers의 duties를 분리
- **CDN and external resources**: external CDNs에서 로드하는 scripts 또는 resources는 Subresource Integrity(SRI) hashes를 사용해야 함
- **Data integrity**: critical data modifications는 누가, 무엇을, 언제 변경했는지 auditable해야 함

**Verification**:

- untrusted input의 unsafe deserialization이 없음
- CDNs에서 로드되는 external scripts가 SRI integrity attributes를 포함함
- CI/CD pipeline definitions가 access-controlled되어 있고 changes가 auditable함
- critical data changes가 actor, timestamp, before/after values와 함께 logged됨

---

## Rule SECURITY-14: Alerting and Monitoring

**Rule**: logging(SECURITY-02, SECURITY-03)에 더해 systems는 다음을 포함해야 합니다.

- **Security event alerting**: repeated authentication failures, privilege escalation attempts, unusual locations에서의 access, authorization failures 같은 high-value security events에 alerts를 configured해야 함
- **Log integrity**: logs는 append-only 또는 tamper-evident storage에 저장되어야 함. application code가 자체 audit logs를 delete하거나 modify할 수 없어야 함
- **Log retention**: logs는 application's compliance requirements에 적합한 최소 기간 동안 retained되어야 함(default: 최소 90일)
- **Monitoring dashboards**: key operational and security metrics에 대한 monitoring dashboard 또는 alarm configuration이 정의되어야 함

**Verification**:

- authentication failures와 authorization violations에 대해 alerting이 configured됨
- application log groups에 retention policies가 설정됨(최소 90일)
- application roles가 자체 log groups/streams를 delete할 permission을 갖지 않음
- security-relevant events(login failures, access denied, privilege changes)가 alerts를 생성함

---

## Rule SECURITY-15: Exception Handling and Fail-Safe Defaults

**Rule**: 모든 application은 exceptional conditions를 안전하게 처리해야 합니다.

- **Catch and handle**: 모든 external calls(database, API, file I/O)는 explicit error handling을 가져야 함. production에서 unhandled promise rejections 또는 uncaught exceptions 금지
- **Fail closed**: error 발생 시 system은 access를 deny하거나 operation을 halt해야 함. 절대 fail open하지 마세요.
- **Resource cleanup**: error paths는 resources(connections, file handles, locks)를 release해야 함. try/finally, using statements 또는 equivalent patterns 사용
- **User-facing errors**: users에게 표시되는 error messages는 generic해야 함. internal details 또는 system information 노출 금지
- **Global error handler**: applications는 unhandled exceptions를 catch하고, SECURITY-03에 따라 log하며, safe response를 반환하는 global/top-level error handler를 가져야 함

**Verification**:

- 모든 external calls(DB, HTTP, file I/O)에 explicit error handling(try/catch, .catch(), error callbacks)이 있음
- application entry point에 global error handler가 configured됨
- error paths가 authorization 또는 validation checks를 우회하지 않음(fail closed)
- error paths에서 resources가 cleaned up됨(connections closed, transactions rolled back)
- application code에 unhandled promise rejections 또는 uncaught exception warnings가 없음

---

## Enforcement Integration

이 rules는 모든 AI-DLC stage에 적용되는 cross-cutting constraints입니다. 각 stage에서 다음을 수행합니다.

- 생성된 artifacts에 대해 모든 SECURITY rule verification criteria를 평가합니다.
- stage completion summary에 "Security Compliance" section을 포함하고 각 rule을 compliant, non-compliant 또는 N/A로 나열합니다.
- rule이 하나라도 non-compliant이면 blocking security finding입니다. Overview에 정의된 blocking finding behavior를 따르세요.
- design documentation과 test instructions에 security rule references를 포함합니다.

---

## Appendix: OWASP Reference Mapping

<!-- TODO: CRITICAL - 이 OWASP mapping table 전체를 검증해야 합니다. "2025" edition은 존재하지 않을 수 있습니다. 이 mapping에 의존하기 전에 실제 published standard를 기준으로 category IDs(A01-A10), numbering, names를 검증해야 합니다. 최신 published OWASP Top 10은 2021일 수 있습니다. -->

human reviewers를 위해 다음은 SECURITY rules를 OWASP Top 10(2025) categories에 매핑합니다.

| SECURITY Rule | OWASP Category |
|---|---|
| SECURITY-08 | A01:2025 – Broken Access Control |
| SECURITY-09 | A02:2025 – Security Misconfiguration |
| SECURITY-10 | A03:2025 – Software Supply Chain Failures |
| SECURITY-11 | A06:2025 – Insecure Design |
| SECURITY-12 | A07:2025 – Authentication Failures |
| SECURITY-13 | A08:2025 – Software or Data Integrity Failures |
| SECURITY-14 | A09:2025 – Logging & Alerting Failures |
| SECURITY-15 | A10:2025 – Mishandling of Exceptional Conditions |
