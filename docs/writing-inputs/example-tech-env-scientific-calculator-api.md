# Technical Environment Document: CalcEngine Scientific Calculator API

## Project Technical Summary

- **Project Name**: CalcEngine
- **Project Type**: Greenfield
- **Primary Runtime Environment**: Cloud
- **Cloud Provider**: AWS
- **Target Deployment Model**: Serverless (API Gateway + Lambda)
- **Package Manager**: uv
- **Team Size**: 4 (백엔드 개발자 2명, 문서 포털용 프론트엔드 개발자 1명, QA 엔지니어 1명)
- **Team Experience**: 강력한 Python 백엔드 경험, 중간 정도의 AWS 경험, 수학 라이브러리 개발 경험 없음. 팀은 FastAPI와 Flask를 전문적으로 사용한 경험이 있음. pytest에 익숙. CDK 경험 제한적(예제가 필요할 것).

---

## Programming Languages

### Required Languages

| Language    | Version   | Purpose                                                                  | Rationale                                                                                                                            |
| ----------- | --------- | ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| Python      | 3.12+     | API 서비스, 수학 엔진, Lambda 핸들러, CDK 인프라                          | 팀의 주 언어. 풍부한 수학 에코시스템 (mpmath, numpy, scipy). uv가 빠르고 신뢰할 수 있는 의존성 관리 제공.                              |
| HTML/CSS/JS | ES2022+   | 문서 포털 (정적 사이트)                                                  | API 문서를 위한 최소 프론트엔드. 프레임워크 불필요; Jinja2 템플릿으로 정적 생성.                                                       |

### Permitted Languages

| Language   | Conditions for Use                                                                                                                                                                                                       |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Rust       | Python 성능이 부족할 경우 성능이 중요한 수학 함수(예: 표현식 파서)에 대해 승인됨. 채택 전 프로파일링 증거 필요. PyO3/maturin을 통해 Python에 노출.                                                                         |
| TypeScript | 팀이 Python CDK보다 TypeScript CDK를 선호하는 경우 CDK 인프라에 대해 승인됨. 결정은 프로젝트 중간이 아닌 construction 시작 전에 이루어져야 함.                                                                              |

### Prohibited Languages

| Language   | Reason                                                                       | Use Instead                                   |
| ---------- | ---------------------------------------------------------------------------- | --------------------------------------------- |
| Java       | 팀 전문성 없음. 운영 복잡도 추가 (Lambda에서 JVM cold start).                | Python                                        |
| Go         | 팀 전문성 없음. Python이 모든 현재 요구사항을 커버.                          | Python                                        |
| C/C++      | 네이티브 확장의 유지 보수 부담.                                              | 네이티브 성능이 필요한 경우 PyO3를 통한 Rust  |

---

## Package and Environment Management

### 표준 도구로서의 uv

uv는 이 프로젝트의 **유일한 패키지 및 환경 관리 도구**입니다. pip, pip-tools, poetry, pipenv, conda를 사용하지 마세요.

### uv 사용 표준

```bash
# 프로젝트 초기화 (이미 완료됨; 다시 실행하지 마세요)
uv init calcengine
cd calcengine

# 의존성 추가
uv add fastapi                      # 런타임 의존성 추가
uv add uvicorn[standard]            # extras와 함께 추가
uv add --dev pytest pytest-cov      # 개발 의존성 추가
uv add --dev mypy ruff              # 개발 도구 추가

# 의존성 제거
uv remove requests                  # 의존성 제거

# 프로젝트 환경에서 커맨드 실행
uv run python -m calcengine.main    # 애플리케이션 실행
uv run pytest                       # 테스트 실행
uv run mypy src/                    # 타입 체커 실행
uv run ruff check src/              # 린터 실행

# 락파일에서 환경 동기화
uv sync                             # uv.lock에서 모든 의존성 설치
uv sync --dev                       # 개발 의존성 포함

# 락파일 관리
# uv.lock은 자동 생성됩니다. 절대 수동으로 편집하지 마세요.
# uv.lock은 반드시 버전 관리에 커밋되어야 합니다.
```

### 의존성 파일 표준

| File              | Purpose                                                       | Committed to Git  |
| ----------------- | ------------------------------------------------------------- | ----------------- |
| `pyproject.toml`  | 프로젝트 메타데이터, 의존성 선언, 도구 설정                   | Yes               |
| `uv.lock`         | 정확히 해결된 버전을 가진 결정적(deterministic) 락파일        | Yes               |
| `.python-version` | 프로젝트의 Python 버전 핀 (예: `3.12`)                        | Yes               |

### pyproject.toml 컨벤션

모든 프로젝트 설정은 `pyproject.toml`에 살아 있습니다. pyproject.toml 설정을 지원하는 도구에 대해 별도의 설정 파일을 만들지 마세요.

```toml
[project]
name = "calcengine"
version = "0.1.0"
description = "Scientific calculator REST API"
requires-python = ">=3.12"
dependencies = [
    # uv add로 추가된 런타임 의존성이 여기에 나열됨
]

[dependency-groups]
dev = [
    # uv add --dev로 추가된 개발 의존성이 여기에 나열됨
]

[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "-v --tb=short --strict-markers"
markers = [
    "unit: Unit tests (fast, no external dependencies)",
    "integration: Integration tests (may require services)",
    "accuracy: Mathematical accuracy validation tests",
]

[tool.mypy]
python_version = "3.12"
strict = true
warn_return_any = true
warn_unused_configs = true
disallow_untyped_defs = true

[tool.ruff]
target-version = "py312"
line-length = 100

[tool.ruff.lint]
select = ["E", "F", "W", "I", "N", "UP", "B", "A", "SIM", "TCH"]

[tool.coverage.run]
source = ["src/calcengine"]
branch = true

[tool.coverage.report]
fail_under = 90
show_missing = true
```

---

## Frameworks and Libraries

### Required Frameworks

| Framework/Library   | Version   | Domain                                              | Rationale                                                                                                  |
| ------------------- | --------- | --------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| FastAPI             | 0.115+    | REST API 프레임워크                                 | 비동기 지원, 자동 OpenAPI 스펙 생성, Pydantic 검증, 강력한 Python 타이핑 통합.                              |
| Pydantic            | 2.x       | 요청/응답 검증, 설정 관리                           | 타입 안전 데이터 모델, JSON 직렬화, FastAPI에 핵심적.                                                       |
| uvicorn             | 0.30+     | ASGI 서버                                           | FastAPI의 표준 프로덕션 서버. 로컬과 Mangum을 통한 Lambda에서 사용.                                          |
| Mangum              | 1.x       | Lambda 어댑터                                       | FastAPI ASGI 앱을 AWS Lambda 핸들러로 래핑. 제로 설정 어댑터.                                                |
| pytest              | 8.x       | 테스팅 프레임워크                                   | 팀 표준. 풍부한 플러그인 에코시스템.                                                                          |
| mypy                | 1.x       | 정적 타입 체크                                      | 런타임 전에 타입 에러 잡기. strict 모드 강제.                                                                |
| ruff                | 0.8+      | 린팅 및 포매팅                                      | flake8, isort, black을 단일 빠른 도구로 대체.                                                                |
| structlog           | 24.x+     | 구조화된 JSON 로깅                                  | 모든 Lambda 핸들러와 API 엔드포인트가 구조화된 JSON 로그를 emit해야 함. 공유 모듈에서 한 번 설정.            |
| aws-cdk-lib         | 2.x       | Infrastructure as Code                              | AWS 배포. 모든 인프라에 Python CDK construct.                                                                |

### Preferred Libraries

해당 기능이 필요할 때 사용하세요. 미리 추가하지 마세요.

| Library        | Purpose                                          | Use When                                                                                                                       |
| -------------- | ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------ |
| mpmath         | 임의 정밀도 산술                                 | Phase 2: 임의 정밀도 모드가 구현될 때. MVP에는 불필요 (IEEE 754 더블로 충분).                                                  |
| numpy          | 배열 연산, 선형대수                              | Phase 2: 행렬/벡터 연산이 구현될 때. 기본 산술에는 사용하지 마세요.                                                            |
| scipy          | 통계 분포, 수치 적분                             | Phase 2+: 고급 통계와 미적분이 구현될 때.                                                                                      |
| httpx          | 비동기 HTTP 클라이언트                           | 외부 HTTP 호출(예: Phase 3의 환율 가져오기). 비동기 호환성을 위해 requests보다 선호.                                            |
| boto3          | AWS SDK                                          | 배포 시점에 CDK가 처리하지 않는 직접 AWS 서비스 상호작용(예: 런타임의 DynamoDB 쿼리, Secrets Manager 읽기).                       |
| pytest-cov     | 테스트 커버리지 리포팅                           | 항상. 프로젝트 시작부터 dev 의존성에 포함.                                                                                      |
| pytest-asyncio | 비동기 테스트 지원                               | 비동기 FastAPI 엔드포인트나 비동기 함수를 테스트할 때.                                                                          |
| hypothesis     | 속성 기반 테스팅                                 | 수학 함수 테스트. 엣지 케이스를 찾기 위해 무작위 입력 생성. 모든 수학 모듈에 강력히 권장.                                       |
| freezegun      | 시간 모킹                                        | 시간 의존 로직(레이트 리미팅, 토큰 만료, 감사 타임스탬프)을 테스트할 때.                                                       |

### Prohibited Libraries

| Library                     | Reason                                                                              | Alternative                                                                                       |
| --------------------------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| Flask                       | 프로젝트는 FastAPI 사용. 웹 프레임워크를 섞지 마세요.                               | FastAPI                                                                                           |
| Django                      | API 서비스에 과함. ORM 불필요.                                                       | FastAPI + 직접 DynamoDB 접근                                                                      |
| requests                    | 동기식만. FastAPI에서 비동기 이벤트 루프를 블록.                                    | httpx                                                                                             |
| sympy                       | MVP 스코프에 비해 너무 무거움. 큰 의존성 트리를 끌어옴.                              | 표현식 파서를 직접 구현. Phase 3 심볼릭 계산을 위해 재평가.                                       |
| pandas                      | 불필요. CalcEngine은 데이터프레임이 아닌 개별 계산을 처리.                          | 필요할 때 표준 Python 또는 배열 연산을 위한 numpy.                                                |
| SQLAlchemy                  | MVP에 관계형 데이터베이스 없음. DynamoDB가 데이터 저장소.                            | boto3 DynamoDB resource/client                                                                    |
| celery                      | MVP에 불필요한 복잡도. 모든 계산은 동기식이고 빠름(<50ms).                            | Phase 3에서 배치 처리를 위해 재평가. 비동기가 더 일찍 필요하면 SQS + Lambda 사용.                  |
| poetry / pipenv / pip-tools | 프로젝트는 uv만 사용. 대체 패키지 매니저를 도입하지 마세요.                          | uv                                                                                                |
| black / isort / flake8      | ruff로 대체됨 (셋을 결합).                                                          | ruff                                                                                              |

### 라이브러리 승인 프로세스

required 또는 preferred 리스트에 없는 라이브러리를 추가하려면:

1. "Dependency Request: [library-name]" 제목의 GitHub 이슈를 엽니다
2. 다음을 포함: 목적, 고려한 대안, 라이선스 (MIT, Apache 2.0, BSD여야 함), 유지 보수 상태 (마지막 릴리스 날짜, 열린 이슈 수), 크기 영향
3. 기술 리드가 검토하고 승인 또는 거부
4. 승인되면 `uv add`로 추가하고 이 문서를 업데이트

---

## Cloud Environment

### Cloud Provider

- **Primary Provider**: AWS
- **Account Structure**: MVP는 단일 AWS 계정. Phase 2에서 별도 dev/staging/prod 계정.
- **Regions**: `us-east-1` (primary). MVP에 재해 복구 리전 없음. 멀티 리전은 Phase 2 계획.

### Service Allow List

| Service                       | Approved Use Cases                                                          | Constraints                                                                                                                  |
| ----------------------------- | --------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| AWS Lambda                    | API 요청 핸들러, 수학 계산                                                  | Python 3.12 런타임. MVP에 최대 256MB 메모리 (프로파일링으로 필요가 입증되면 증가). 30초 타임아웃.                              |
| Amazon API Gateway (HTTP API) | 공개 REST API 엔드포인트                                                    | HTTP API 타입 (REST API 타입 아님). TLS가 있는 커스텀 도메인. 레이트 리미팅을 위한 usage plan.                                  |
| Amazon DynamoDB               | API 키 저장, 사용량 미터링, 레이트 리미트 카운터                            | on-demand 용량 모드. 단일 테이블 설계. 레이트 리미트 윈도우에 TTL.                                                             |
| Amazon S3                     | OpenAPI 스펙 호스팅, 정적 문서 사이트, Lambda 배포 패키지                   | 버킷 암호화 활성화. 문서 사이트 버킷을 제외하고 공개 접근 차단 (CloudFront 배포).                                              |
| Amazon CloudFront             | 문서 포털 및 API 스펙용 CDN                                                 | HTTPS만. 정적 에셋을 적극적으로 캐싱.                                                                                          |
| Amazon CloudWatch             | 로깅, 메트릭, 알람, 대시보드                                                | 모든 Lambda의 구조화된 JSON 로그. 계산 수, 레이턴시 백분위, 에러율에 대한 커스텀 메트릭.                                       |
| AWS Secrets Manager           | Stripe API 키, 서명 키                                                      | 지원되는 곳에 자동 회전. Lambda가 cold start에 읽고 메모리에 캐시.                                                              |
| AWS Certificate Manager       | 커스텀 도메인용 TLS 인증서                                                  | API Gateway와 CloudFront에 사용.                                                                                              |
| Amazon Cognito                | 문서 포털 및 API 키 관리를 위한 개발자 계정 인증                            | 개발자 가입/로그인을 위한 user pool. API 호출 인증에는 사용 안 함 (그건 API 키).                                                |
| Amazon SQS                    | 실패한 비동기 작업을 위한 dead-letter queue                                  | 표준 큐. 실패한 빌링 이벤트와 에러 캡처에 사용. MVP에서 계산 요청에는 사용 안 함.                                              |
| AWS CDK                       | Infrastructure as Code 배포                                                 | Python CDK. 모든 인프라가 CDK에 정의됨. 콘솔 수동 변경 없음.                                                                   |
| AWS CloudTrail                | API 감사 로깅                                                               | 모든 관리 이벤트에 활성화. 프로덕션에서 S3와 Lambda의 데이터 이벤트.                                                            |
| AWS IAM                       | 서비스 권한                                                                 | Lambda 함수별 최소 권한 정책. 와일드카드 리소스 권한 없음.                                                                     |

### Service Disallow List

| Service                    | Reason                                                                   | Alternative                                                          |
| -------------------------- | ------------------------------------------------------------------------ | -------------------------------------------------------------------- |
| Amazon EC2                 | 운영 오버헤드. 서버리스 모델 선호.                                       | 컴퓨트에 Lambda.                                                     |
| Amazon ECS / Fargate       | MVP의 요청/응답 워크로드에 over-engineering.                              | Lambda. cold start가 문제가 되면 재평가.                              |
| Amazon RDS / Aurora        | 관계형 데이터베이스 불필요. API 키와 사용 데이터는 DynamoDB에 적합.       | DynamoDB.                                                            |
| Amazon ElastiCache / Redis | MVP에 캐싱 레이어 불필요. 계산은 stateless이고 빠름.                      | 필요하면 Lambda 실행 컨텍스트 내의 인메모리 캐싱.                     |
| AWS Elastic Beanstalk      | IaC 모델에 맞지 않음.                                                    | CDK + Lambda.                                                        |
| Amazon Kinesis             | 스트리밍 불필요. 모든 계산이 동기식 요청/응답.                            | 비동기 처리가 필요하면 SQS.                                          |
| AWS Step Functions         | MVP에 다단계 오케스트레이션 없음.                                        | 직접 Lambda 호출. Phase 3 배치 처리를 위해 재평가.                    |
| Amazon SNS                 | MVP에 pub/sub 불필요.                                                    | dead-letter queue용 SQS.                                              |

### 서비스 승인 프로세스

allow list에 없는 서비스를 사용하려면:

1. "AWS Service Request: [service-name]" 제목의 GitHub 이슈를 엽니다
2. 다음을 포함: 사용 사례, 비용 추정 (월간), 보안 함의, 운영 부담, 그리고 허용된 서비스가 필요를 충족할 수 없는 이유
3. 기술 리드가 검토. PII 접근이나 네트워크 노출이 있는 서비스는 추가 보안 리뷰 필요.
4. 승인되면 CDK construct 추가 및 이 문서 업데이트

---

## Preferred Technologies and Patterns

### 아키텍처 패턴

**서버리스 함수로 배포되는 모듈러 모노리스.**

CalcEngine은 내부 모듈(arithmetic, trigonometry, statistics 등)을 가진 단일 Python 패키지이며, 단일 FastAPI 애플리케이션을 통해 노출되어 API Gateway 뒤의 AWS Lambda로 배포됩니다. 마이크로서비스 아키텍처가 아닙니다.

| Decision           | Choice                                                          | Rationale                                                                                                                                                              |
| ------------------ | --------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 아키텍처 스타일    | 모듈러 모노리스                                                  | 소규모 팀 (4명), 단일 도메인, MVP에 모듈별 독립적인 스케일링 요구사항 없음.                                                                                              |
| 배포 모델          | Mangum을 통해 모든 API 라우트를 처리하는 단일 Lambda 함수        | 단순성. 하나의 배포 아티팩트. 모든 엔드포인트에 cold start 분할 상환.                                                                                                    |
| 모듈 경계          | `src/calcengine/` 내의 Python 패키지                            | 별도 서비스의 운영 비용 없는 깨끗한 내부 경계. 특정 엔드포인트가 다른 메모리/타임아웃이 필요하면 나중에 별도 Lambda로 추출 가능.                                          |

### API Design Standards

- **Style**: HTTPS 상의 REST. JSON 요청 및 응답 본문.
- **Base URL**: `https://api.calcengine.io/v1/`
- **Versioning**: URL path prefix (`/v1/`, `/v2/`). major 버전만. non-breaking 변경은 버전을 증가시키지 않음.
- **Documentation**: FastAPI가 자동 생성하는 OpenAPI 3.1 명세. `https://docs.calcengine.io`에 호스팅.
- **Naming Convention**: JSON 필드 이름에 snake_case (Python 컨벤션). URL 경로에 kebab-case.
- **Content Type**: 모든 요청과 응답에 `application/json`. XML 지원 없음.

**표준 요청 형식:**

```json
{
  "expression": "sin(pi/4) * 2 + sqrt(16)",
  "options": {
    "angle_mode": "radians",
    "precision": 15
  }
}
```

**표준 성공 응답 형식:**

```json
{
  "result": 5.414213562373095,
  "expression": "sin(pi/4) * 2 + sqrt(16)",
  "computation_time_ms": 2.3,
  "engine_version": "0.1.0"
}
```

**표준 에러 응답 형식:**

```json
{
  "error": {
    "code": "DOMAIN_ERROR",
    "message": "Cannot compute logarithm of a negative number",
    "detail": "log(-5) is undefined for real numbers",
    "parameter": "expression",
    "documentation_url": "https://docs.calcengine.io/errors/DOMAIN_ERROR"
  }
}
```

**에러 코드 (MVP):**

| Code                  | HTTP Status  | Meaning                                                                  |
| --------------------- | ------------ | ------------------------------------------------------------------------ |
| `PARSE_ERROR`         | 400          | 표현식을 파싱할 수 없음. 형식 오류.                                       |
| `DOMAIN_ERROR`        | 422          | 수학적으로 정의되지 않음 (log(-1), sqrt(-1), 0으로 나누기).                |
| `OVERFLOW_ERROR`      | 422          | 결과가 표현 가능한 범위를 초과.                                            |
| `INVALID_PARAMETER`   | 400          | 요청 파라미터가 잘못된 타입 또는 값.                                       |
| `EXPRESSION_TOO_LONG` | 400          | 표현식이 최대 허용 길이를 초과.                                            |
| `RATE_LIMIT_EXCEEDED` | 429          | API 키가 레이트 리미트를 초과.                                            |
| `UNAUTHORIZED`        | 401          | API 키 누락 또는 잘못됨.                                                  |
| `INTERNAL_ERROR`      | 500          | 예기치 못한 서버 에러.                                                    |

### 데이터 패턴

- **Primary Data Store**: DynamoDB (단일 테이블 설계)
- **DynamoDB의 엔티티**: API 키, 사용량 카운터 (키별, 월별), 레이트 리미트 윈도우 (키별, 분별)
- **Access Pattern**: 모든 읽기와 쓰기는 primary key (API 키 ID)로. 스캔 없음. 복잡한 쿼리 없음.
- **Caching**: 외부 캐시 없음. Lambda가 warm 호출 간 DynamoDB 연결 재사용. API 키 검증 결과는 Lambda 메모리에 60초 동안 캐시.
- **No relational database**: 관계형 쿼리가 필요해지면(리포팅, 분석) RDS 추가 전에 DynamoDB export to S3 + Athena를 평가.

### 로깅 패턴

모든 로그 출력은 structlog를 통해 구조화된 JSON이어야 합니다. 사람이 읽을 수 있는 콘솔 출력은 로컬 개발 전용입니다.

```python
import structlog

logger = structlog.get_logger()

# 표준 로그 호출
logger.info(
    "calculation_completed",
    expression=expression,
    result=result,
    computation_time_ms=elapsed,
    api_key_id=api_key_id,
)

# 에러 로그 호출
logger.error(
    "calculation_failed",
    expression=expression,
    error_code="DOMAIN_ERROR",
    error_detail=str(e),
    api_key_id=api_key_id,
)
```

**모든 API 요청에 필수 로그 필드:**

| Field         | Description                                              |
| ------------- | -------------------------------------------------------- |
| `request_id`  | 요청별 고유 ID (API Gateway에서 또는 생성됨)             |
| `api_key_id`  | 해시된 API 키 식별자 (원시 키는 절대 로깅하지 않음)      |
| `endpoint`    | 호출된 API 경로                                          |
| `http_method` | GET, POST 등                                             |
| `http_status` | 응답 상태 코드                                           |
| `duration_ms` | 총 요청 처리 시간                                        |
| `timestamp`   | ISO 8601 타임스탬프                                      |

---

## Security Requirements

### Authentication and Authorization

- **API Call Authentication**: `Authorization: Bearer {key}` 헤더로 전달된 API 키. API 키는 32자 임의 문자열, DynamoDB에 bcrypt 해시로 저장.
- **Developer Portal Authentication**: Amazon Cognito user pool. 이메일 + 패스워드 가입 및 이메일 검증.
- **Authorization Model**: 평면 구조. 모든 API 키가 모든 엔드포인트 접근 가능. 티어 기반 레이트 리미트 (free, starter, professional)는 엔드포인트 레벨 권한이 아닌 사용량 미터링으로 강제.
- **API Key Management**: 개발자가 개발자 포털을 통해 키를 생성, 회전, 폐기. 계정당 최대 3개의 활성 키.

### Data Protection

- **Encryption at Rest**: DynamoDB는 AWS 관리 KMS 키로 암호화. S3 버킷은 SSE-S3로 암호화.
- **Encryption in Transit**: API Gateway 커스텀 도메인과 CloudFront 배포에 TLS 1.2+ 강제. HTTP (평문) 엔드포인트 없음.
- **PII Handling**: 개발자 계정은 이메일과 해시된 패스워드를 저장. 다른 PII는 수집하지 않음. 수학 표현식은 PII가 아님. 표현식은 디버깅을 위해 로깅되지만 영구 저장되지는 않음 (CloudWatch 로그 보존: 30일).
- **Data Classification**: API 키 = Confidential. 개발자 이메일 = Internal. 수학 표현식과 결과 = Public.

### Input Validation

- **표현식 길이 한도**: 최대 4,096자. 더 긴 표현식은 `EXPRESSION_TOO_LONG`으로 거부.
- **표현식 문자 허용리스트**: 영숫자, 산술 연산자 (`+ - * / ^ %`), 괄호, 소수점, 콤마, 공백, 인식되는 함수 이름. 인식되지 않는 문자는 거부.
- **코드 실행 없음**: 표현식 파서는 절대 `eval()`, `exec()`, `compile()`, 또는 어떤 동적 코드 실행도 호출해서는 안 됨. 표현식은 AST로 파싱되어 수학 엔진에 의해 평가됨.
- **재귀 깊이 한도**: 표현식 파서는 중첩 깊이를 100레벨로 제한. 깊이 중첩된 표현식 `(((((...))))`에서 스택 오버플로우 방지.
- **수치 범위 검증**: IEEE 754 더블 정밀도 범위를 초과하는 결과는 `Infinity`나 `NaN` 대신 `OVERFLOW_ERROR`를 반환.

### Secrets Management

- **Stripe API Keys**: AWS Secrets Manager에 저장. cold start에 Lambda가 읽어 메모리에 캐시.
- **Cognito Client Secret**: AWS Secrets Manager에 저장.
- **Prohibited Practices**:
  - `pyproject.toml`, 소스 코드, Git에 커밋된 `.env` 파일의 시크릿 금지
  - Lambda 환경 변수의 시크릿 금지 (런타임에 Secrets Manager 사용)
  - 코드의 AWS 액세스 키 금지 (Lambda는 IAM execution role 사용)
  - 로컬 개발용 `.env` 파일만 허용, `.gitignore`에 나열

### Dependency Security

- **Scanning**: GitHub Dependabot이 Python 의존성에 대해 활성화. 알려진 취약점에 알림.
- **License Policy**: 허용: MIT, Apache 2.0, BSD (2-clause 및 3-clause), PSF, ISC. 금지: GPL, LGPL, AGPL, SSPL, proprietary. 새 의존성을 추가하기 전에 `uv tree`로 확인.
- **Update Policy**: Critical/High CVE는 7일 이내 패치. Medium은 30일 이내. Low는 분기별 평가.

### OWASP Top 10 Compliance (2021)

#### A01:2021 - Broken Access Control

| Control                                 | CalcEngine 구현                                                                                                                                                                                                          |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Authorization enforcement               | 모든 요청에서 라우트 핸들러가 실행되기 전에 FastAPI 미들웨어(`api/middleware/auth.py`)에서 API 키 검증. 유효한 키 없이 접근 가능한 엔드포인트 없음.                                                                       |
| Default deny                            | API Gateway가 게이트웨이 레벨(401)에서 `Authorization` 헤더 없는 요청을 거부. Lambda 핸들러가 잘못되었거나 폐기된 키를 가진 요청을 거부 (401).                                                                            |
| Resource ownership                      | 각 API 키는 Cognito 계정에 묶임. 개발자는 자신의 키만 나열, 회전, 폐기 가능. DynamoDB 쿼리는 인증된 사용자의 파티션 키로 범위 지정.                                                                                       |
| Rate limiting                           | 키별 레이트 리미트가 미들웨어(`api/middleware/rate_limit.py`)에서 강제. Free: 월 10,000 호출, 초당 10회. Starter: 월 1M, 초당 50. Professional: 월 10M, 초당 200. 한도 초과 시 429 반환.                                  |
| CORS policy                             | API Gateway CORS가 문서 포털 origin (`https://docs.calcengine.io`)만 허용하도록 설정. 와일드카드 origin 없음. `GET`과 `POST` 메서드만.                                                                                     |
| Directory traversal / path manipulation | 해당 없음. CalcEngine은 파일을 제공하지 않고 파일 경로를 입력으로 받지 않음.                                                                                                                                              |

#### A02:2021 - Cryptographic Failures

| Control                     | CalcEngine 구현                                                                                                                                                                                            |
| --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Data in transit             | API Gateway 커스텀 도메인과 CloudFront에 TLS 1.2+ 강제. HTTP 엔드포인트 존재하지 않음. API Gateway는 `SecurityPolicy: TLS_1_2`로 설정.                                                                       |
| Data at rest                | DynamoDB는 AWS 관리 KMS 키로 암호화. S3 버킷은 SSE-S3로 암호화. CloudWatch 로그는 서비스 관리 키로 암호화.                                                                                                  |
| Password/credential storage | 개발자 포털 패스워드는 bcrypt (Cognito 관리)로 해시. API 키는 DynamoDB에 bcrypt 해시로 저장. 원시 API 키는 생성 시 정확히 한 번 반환되고 절대 저장되거나 로깅되지 않음.                                          |
| Sensitive data in responses | API 응답에는 API 키, 계정 자격 증명, 내부 식별자가 절대 포함되지 않음. 에러 메시지는 테이블 이름, ARN, 스택 트레이스를 누출하지 않음.                                                                          |
| Sensitive data in logs      | API 키 ID (해시된 식별자, 키 자체가 아님)는 로깅됨. 원시 API 키는 절대 로깅되지 않음. 개발자 이메일은 계산 로그에 포함되지 않음.                                                                                 |

#### A03:2021 - Injection

| Control               | CalcEngine 구현                                                                                                                                                                                                                                                                                       |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Expression injection  | 표현식 파서는 엄격한 문법에서 AST를 빌드. `eval()`, `exec()`, `compile()`, 또는 어떤 Python 코드 실행 메커니즘도 **사용하지 않음**. 인식되는 토큰(숫자, 연산자, 괄호, 화이트리스트된 함수 이름)만 수용. 인식되지 않는 토큰은 `PARSE_ERROR` (400)를 야기.                                              |
| Character allowlist   | 표현식 입력은 다음으로 제한: 숫자, 소수점, 산술 연산자 (`+ - * / ^ %`), 괄호, 콤마, 공백, 고정된 함수 이름 집합 (`sin`, `cos`, `tan`, `log`, `sqrt` 등). 다른 모든 문자는 파싱 전에 거부.                                                                                                              |
| NoSQL injection       | DynamoDB 쿼리는 매개변수화된 키 조건과 함께 boto3 SDK 사용. 사용자 입력을 쿼리 표현식에 문자열 연결하지 않음. 파티션 키와 정렬 키는 요청 본문에서 보간되지 않고 프로그래밍 방식으로 설정.                                                                                                              |
| HTTP header injection | FastAPI와 Pydantic이 모든 요청 입력을 검증하고 타입 체크. 응답 헤더는 사용자 입력이 아니라 프레임워크에 의해 프로그래밍 방식으로 설정.                                                                                                                                                                  |
| Log injection         | structlog가 로그 값의 특수 문자를 이스케이프. 사용자 제공 표현식은 로그 포맷 문자열에 보간되지 않고 구조화된 JSON 필드 내의 문자열 값으로 로깅.                                                                                                                                                       |

#### A04:2021 - Insecure Design

| Control               | CalcEngine 구현                                                                                                                                                                                                                |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Threat modeling       | AIDLC NFR Requirements 스테이지 동안 위협 모델 생성. 새 엔드포인트나 통합 지점이 추가될 때 검토. 주요 위협: 표현식 인젝션, 리소스 고갈, API 키 남용.                                                                            |
| Defense in depth      | 세 레이어에서 검증: (1) API Gateway 요청 검증, (2) FastAPI의 Pydantic 모델 검증, (3) 엔진 함수의 도메인 검증. 각 레이어는 독립적으로 거부.                                                                                       |
| Business logic limits | 표현식 길이는 4,096자로 캡. 파서 재귀 깊이는 100레벨로 캡. 통계 엔드포인트의 최대 배열 크기: 10,000 요소. 이러한 한도는 합법적인 사용에 영향을 주지 않고 리소스 고갈을 방지.                                                       |
| Abuse case testing    | 테스트 스위트는 부정적/남용 테스트를 포함: 과대 표현식, 깊이 중첩된 괄호, 느린 평가를 야기하도록 설계된 표현식, 레이트 리미트를 초과하는 빠른 연사 요청, 잘못/만료/폐기된 API 키.                                                  |

#### A05:2021 - Security Misconfiguration

| Control                | CalcEngine 구현                                                                                                                                                                                                                                                              |
| ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Infrastructure as Code | 모든 인프라가 AWS CDK (Python)에 정의됨. 콘솔 수동 변경 없음. 배포 전 풀 리퀘스트에서 CDK diff 검토.                                                                                                                                                                          |
| Default credentials    | 어떤 환경에도 기본 API 키, 관리자 계정, 하드코딩된 패스워드 없음. Cognito user pool은 이메일 검증 요구.                                                                                                                                                                       |
| Error messages         | 프로덕션 에러 응답은 CalcEngine 에러 코드, 사용자 친화적 메시지, 문서 URL을 반환. Python traceback, Lambda ARN, DynamoDB 테이블 이름, 내부 파일 경로를 절대 노출하지 않음. 프로덕션에서 FastAPI `debug=False`.                                                                  |
| Unnecessary features   | 프로덕션 Lambda에 노출된 `/docs`나 `/redoc` 인터랙티브 엔드포인트 없음. OpenAPI 스펙은 정적 문서 사이트에서만 제공. `engine_version` 이상의 버전 세부 정보를 드러내는 헬스 체크 엔드포인트 없음.                                                                                |
| Security headers       | API Gateway 응답은 다음을 포함: `Strict-Transport-Security: max-age=31536000; includeSubDomains`, `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY`, API 응답에 `Cache-Control: no-store`. CloudFront가 문서 사이트에 보안 헤더 추가.                                  |
| Lambda configuration   | Lambda 함수는 최소 필요 메모리 (256MB) 사용. 타임아웃은 30초로 설정. 무한 스케일링 방지를 위한 예약 동시성 설정. 시크릿을 포함한 환경 변수 없음 (런타임에 Secrets Manager).                                                                                                    |

#### A06:2021 - Vulnerable and Outdated Components

| Control              | CalcEngine 구현                                                                                                                                                          |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Dependency scanning  | GitHub Dependabot 활성화. `pyproject.toml`과 `uv.lock`에서 알려진 취약점 스캔. 알림이 GitHub 이슈를 자동 생성.                                                            |
| Patch SLA            | Critical/High CVE: 7일 이내 패치. Medium: 30일. Low: 분기별 평가.                                                                                                       |
| License compliance   | 허용: MIT, Apache 2.0, BSD, PSF, ISC. 금지: GPL, LGPL, AGPL, SSPL, proprietary. 의존성 추가 전 `uv tree`로 확인.                                                          |
| Lockfile integrity   | `uv.lock`이 Git에 커밋되고 CI에서 강제됨. CI 파이프라인의 `uv sync --locked`는 락파일이 최신이 아니면 실패. CI에서 ad-hoc `uv add` 없음.                                  |
| Minimal dependencies | 금지된 라이브러리 리스트가 부풀어진 의존성 트리 방지 (MVP에 pandas, Django, SQLAlchemy, sympy 없음). 각 새 의존성은 정당화가 포함된 GitHub 이슈 필요.                       |

#### A07:2021 - Identification and Authentication Failures

| Control                     | CalcEngine 구현                                                                                                                                                                                                                                                |
| --------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| API key hashing             | API 키는 32자 암호학적 랜덤 문자열 (`secrets.token_urlsafe` 경유). bcrypt 해시로 저장. 조회는 키 prefix(첫 8자, 평문 저장)를 사용해 레코드를 찾은 다음 bcrypt verify로 전체 키 확인.                                                                            |
| Brute force protection      | API Gateway throttling: 모든 엔드포인트에 걸쳐 IP별 초당 100 요청. 실패한 인증 시도 (잘못된 키)는 `api_key_prefix`와 소스 IP와 함께 로깅. 단일 IP에서 5분 동안 50회 실패한 auth 시도 후 WAF 룰을 통한 임시 IP 차단.                                                 |
| Developer portal auth       | Cognito가 강제: 최소 12자 패스워드, 이메일 검증 필수, 5회 실패한 로그인 시도 후 계정 잠금.                                                                                                                                                                      |
| Key rotation                | 개발자는 기존 키를 폐기하기 전에 새 키를 만들 수 있음 (무중단 회전을 위한 중복 기간). 계정당 최대 3개 활성 키로 키 축적 방지.                                                                                                                                    |
| Credential exposure         | API 키는 생성 시 정확히 한 번 반환됨 (HTTP 응답 본문에서). 어디에도 평문으로 저장되지 않음. 이메일에 포함되지 않음. 생성 후 개발자 포털에 표시되지 않음.                                                                                                          |
| Multi-factor authentication | MVP에 필요 없음. Cognito MFA 지원이 가능하며 팀/엔터프라이즈 계정이 도입되는 Phase 2에서 옵션으로 활성화될 것.                                                                                                                                                   |

#### A08:2021 - Software and Data Integrity Failures

| Control                       | CalcEngine 구현                                                                                                                                                                                                                                            |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CI/CD pipeline security       | GitHub Actions. `main` 브랜치 보호: PR 필요, 최소 1개 리뷰, 모든 CI 체크 통과. `main`으로의 직접 푸시 없음. 배포 워크플로우는 `main`으로의 머지에만 트리거.                                                                                                  |
| Dependency integrity          | `uv.lock`은 모든 의존성에 대한 해시를 포함. `uv sync --locked`가 설치 시 해시 검증. PR의 락파일 변경은 명시적으로 검토.                                                                                                                                       |
| Deployment artifact integrity | Lambda 배포 패키지는 깨끗한 `uv sync --locked` 설치에서 CI에서 빌드됨. 프로덕션에 배포되는 로컬 빌드 없음. CDK 배포는 개발자 머신이 아닌 CI 파이프라인에서만 실행.                                                                                              |
| Deserialization safety        | Pydantic v2 모델이 모든 들어오는 JSON을 파싱하고 검증. `pickle`, `yaml.load()` (안전하지 않은 loader), `marshal` 사용 없음. Pydantic의 JSON 파싱을 통한 `json.loads()`만. Pydantic `model_config`는 예상치 못한 필드를 거부하도록 `extra = "forbid"`.            |

#### A09:2021 - Security Logging and Monitoring Failures

| Control                | CalcEngine 구현                                                                                                                                                                                                                                                            |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Security events logged | 아래 모든 이벤트는 구조화된 JSON으로 CloudWatch에 로깅됨: 인증 실패 (invalid/expired/revoked key), 레이트 리미트 초과 (429), 입력 검증 실패 (400), 권한 부여 이상, 모든 5xx 에러.                                                                                            |
| Log protection         | CloudWatch 로그는 30일 동안 보존. 로그 그룹 리소스 정책이 Lambda execution role에 의한 삭제를 방지. CloudTrail이 object lock이 있는 별도 S3 버킷에 관리 이벤트를 로깅.                                                                                                       |
| Alerting               | CloudWatch 알람 설정: 5분 동안 5xx 에러율 > 1%, 분당 인증 실패율 > 100, 단일 API 키가 시도 중 자신의 레이트 리미트의 10배 초과 생성, Lambda 동시 실행 > 예약 동시성의 80%. 알람은 SNS를 통해 on-call 이메일/SMS로 알림.                                                       |
| Monitoring dashboard   | CloudWatch 대시보드 표시: 요청 수, 에러율 (4xx 및 5xx), p50/p95/p99 레이턴시, auth 실패 수, 레이트 리미트 적중 수, Lambda cold start 백분율, DynamoDB 소비 용량. 매주 검토.                                                                                                    |

#### A10:2021 - Server-Side Request Forgery (SSRF)

| Control               | CalcEngine 구현                                                                                                                                                                                                                                                                                                                                                                            |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Applicability         | **MVP에 위험 낮음.** CalcEngine은 사용자 입력 기반의 아웃바운드 HTTP 요청을 하지 않음. 표현식 파서는 수학 표현식을 평가; URL을 가져오거나, 호스트 이름을 해석하거나, 네트워크 호출을 하지 않음.                                                                                                                                                                                              |
| Outbound requests     | Lambda의 유일한 아웃바운드 네트워크 호출은: (1) AWS SDK를 통한 DynamoDB 쿼리(엔드포인트가 사용자 입력이 아닌 AWS 리전에 의해 결정됨), (2) cold start에 Secrets Manager 읽기(시크릿 이름이 사용자 입력이 아닌 설정에 하드코딩됨).                                                                                                                                                              |
| Phase 3 consideration | 통화 변환이 추가될 때 (Phase 3), 서비스는 금융 데이터 공급자로부터 환율을 가져올 것. 그 시점에: 공급자 URL은 환경 변수가 될 것 (사용자 입력 아님), 요청은 허용리스트된 호스트 이름을 사용하고, 응답은 사용 전 예상 스키마에 대해 검증될 것. 이 섹션은 Phase 3 출시 전에 업데이트되어야 함.                                                                                                          |
| Network segmentation  | Lambda 함수는 AWS 관리 VPC에서 실행 (MVP에는 고객 VPC 없음). 공개 엔드포인트를 통해서만 AWS 서비스에 도달 가능. 이 설정에서 Lambda에서 도달 가능한 내부 서비스, 데이터베이스, 메타데이터 엔드포인트 없음.                                                                                                                                                                                       |

---

## Testing Requirements

### Test Strategy Overview

| Test Type                   | Required          | Coverage Target                                       | Tooling                               |
| --------------------------- | ----------------- | ----------------------------------------------------- | ------------------------------------- |
| Unit Tests                  | Yes               | 90% 라인, 80% 브랜치                                  | pytest + pytest-cov                   |
| Mathematical Accuracy Tests | Yes               | 구현된 함수의 100%                                    | pytest + hypothesis                   |
| Integration Tests           | Yes               | 모든 API 엔드포인트, DynamoDB 상호작용                | pytest + moto (AWS 모킹)              |
| Load Tests                  | Yes (출시 전)     | 1,000 동시 요청, p50 < 50ms                           | Locust                                |
| Security Tests              | Yes               | 입력 검증, 인젝션 방지                                | pytest (커스텀) + 수동 OWASP 리뷰     |
| End-to-End Tests            | Conditional       | 배포된 스테이징에 대한 핵심 사용자 여정               | pytest + 라이브 API에 대한 httpx      |

### Unit Testing Standards

- **Coverage Minimum**: 90% 라인 커버리지, 80% 브랜치 커버리지. `pyproject.toml`의 `fail_under = 90`으로 `pytest-cov`가 강제.
- **Mocking Policy**: AWS 서비스(DynamoDB, Secrets Manager)는 moto로 모킹. 시간은 freezegun으로 모킹. 내부 수학 함수는 모킹하지 마세요. 수학 함수는 실제 계산으로 테스트되어야 합니다.
- **Naming Convention**: 테스트 파일은 소스 파일을 미러링. `src/calcengine/trig.py`는 `tests/unit/test_trig.py`에서 테스트. 테스트 함수는 `test_<function>_<scenario>`로 명명 (예: `test_sin_zero_returns_zero`, `test_sin_negative_pi_returns_zero`).
- **Test Location**: 별도의 `tests/` 디렉토리 트리. 소스와 함께 위치하지 않음.

```text
tests/
  unit/
    test_arithmetic.py
    test_trig.py
    test_statistics.py
    test_expression_parser.py
    test_error_handling.py
  integration/
    test_api_evaluate.py
    test_api_trig.py
    test_api_keys.py
    test_rate_limiting.py
  accuracy/
    test_trig_accuracy.py
    test_arithmetic_accuracy.py
    test_statistics_accuracy.py
  conftest.py
```

### Mathematical Accuracy Testing

이는 대부분의 프로젝트에는 존재하지 않는 CalcEngine 특정 테스팅 카테고리입니다.

- **Reference implementation**: 모든 수학 함수는 Python의 `math` 모듈, `mpmath` 라이브러리 (높은 정밀도), 또는 게시된 수학 표에 대해 테스트되어야 함.
- **hypothesis를 사용한 속성 기반 테스팅**: hypothesis를 사용해 무작위 유효 입력을 생성하고 속성이 유지되는지 검증 (예: `sin(x)^2 + cos(x)^2 == 1`, `log(a*b) == log(a) + log(b)`).
- **Edge cases**: 모든 함수는 다음에 대한 명시적 테스트가 있어야 함: 0, 음의 0, 매우 작은 숫자 (epsilon 근처), 매우 큰 숫자, 도메인 경계 (예: asin(1), asin(1.0000001)), 특수 값 (pi, e, 삼각함수에 대한 pi/2의 배수).
- **Tolerance**: 결과는 기본 함수에 대해 1 ULP (unit in the last place) 내에서 참조 값과 일치해야 함. 더 넓은 허용 오차가 수용되는 모든 함수는 정당화와 함께 문서화.

**예제 정확도 테스트 패턴:**

```python
import math
import pytest
from hypothesis import given, strategies as st
from calcengine.trig import sin, cos

class TestSinAccuracy:
    """Validate sin() accuracy against math.sin and known exact values."""

    @pytest.mark.accuracy
    @pytest.mark.parametrize("input_val, expected", [
        (0, 0.0),
        (math.pi / 6, 0.5),
        (math.pi / 4, math.sqrt(2) / 2),
        (math.pi / 2, 1.0),
        (math.pi, 0.0),
        (3 * math.pi / 2, -1.0),
        (2 * math.pi, 0.0),
        (-math.pi / 2, -1.0),
    ])
    def test_sin_known_values(self, input_val: float, expected: float) -> None:
        result = sin(input_val)
        assert result == pytest.approx(expected, abs=1e-15)

    @pytest.mark.accuracy
    @given(st.floats(min_value=-1e6, max_value=1e6, allow_nan=False, allow_infinity=False))
    def test_sin_matches_stdlib(self, x: float) -> None:
        assert sin(x) == pytest.approx(math.sin(x), rel=1e-15)

    @pytest.mark.accuracy
    @given(st.floats(min_value=-1e6, max_value=1e6, allow_nan=False, allow_infinity=False))
    def test_pythagorean_identity(self, x: float) -> None:
        assert sin(x) ** 2 + cos(x) ** 2 == pytest.approx(1.0, abs=1e-14)
```

### Integration Testing Standards

- **Scope**: FastAPI 테스트 클라이언트를 통한 전체 API 요청/응답 사이클 테스트. moto로 DynamoDB 상호작용 테스트.
- **Environment**: 로컬. 배포된 서비스 필요 없음. `moto`가 모든 AWS 서비스를 모킹.
- **Data Management**: 각 테스트가 moto 픽스처를 통해 자체 DynamoDB 테이블을 생성하고 끝나면 해체. 공유 테스트 상태 없음.

### CI/CD Testing Gates

| Pipeline Stage           | Required Tests                                                | Tooling                          | Failure Action                                |
| ------------------------ | ------------------------------------------------------------- | -------------------------------- | --------------------------------------------- |
| Pre-commit               | ruff check, ruff format --check, mypy                         | pre-commit hooks를 통한 ruff, mypy | 커밋 차단                                     |
| Pull Request             | Unit 테스트, accuracy 테스트, integration 테스트, 커버리지 체크 | pytest, GitHub Actions           | 머지 차단                                     |
| Pre-deploy (staging)     | 모든 PR 테스트 + 부하 테스트 (100 동시, 60초)                 | pytest + Locust, GitHub Actions  | 배포 차단                                     |
| Post-deploy (production) | Smoke 테스트 (라이브 API에 대한 10개의 대표적 계산)            | pytest + httpx                   | on-call에 알림. 50% 초과 실패 시 자동 롤백.    |

### 로컬에서 테스트 실행

```bash
# 모든 테스트 실행
uv run pytest

# 유닛 테스트만 실행
uv run pytest tests/unit/ -m unit

# 정확도 테스트만 실행
uv run pytest tests/accuracy/ -m accuracy

# 커버리지 리포트와 함께 실행
uv run pytest --cov --cov-report=term-missing

# 타입 체크 실행
uv run mypy src/

# 린터 실행
uv run ruff check src/ tests/

# 포매터 체크 (변경 없음)
uv run ruff format --check src/ tests/

# 포매터 실행 (변경 적용)
uv run ruff format src/ tests/
```

---

## Project Structure

```text
calcengine/
  .github/
    workflows/
      ci.yml                         # GitHub Actions: PR에서 lint, type check, test
      deploy.yml                     # GitHub Actions: main으로의 머지에 CDK 배포
  src/
    calcengine/
      __init__.py
      main.py                        # FastAPI 앱 생성, Mangum 핸들러
      config.py                      # Pydantic BaseSettings를 통한 설정
      api/
        __init__.py
        router.py                    # 최상위 API 라우터
        endpoints/
          __init__.py
          evaluate.py                # POST /v1/evaluate (표현식 평가)
          arithmetic.py              # POST /v1/arithmetic/{operation}
          trigonometry.py            # POST /v1/trigonometry/{function}
          statistics.py              # POST /v1/statistics/{function}
          constants.py               # GET  /v1/constants/{name}
        middleware/
          __init__.py
          auth.py                    # API 키 검증 미들웨어
          rate_limit.py              # 레이트 리미팅 미들웨어
          request_logging.py         # 구조화된 요청/응답 로깅
        models/
          __init__.py
          requests.py                # Pydantic 요청 모델
          responses.py               # Pydantic 응답 모델
          errors.py                  # 에러 응답 모델 및 에러 코드
      engine/
        __init__.py
        expression_parser.py         # 토크나이저, AST 빌더, 평가기
        arithmetic.py                # 기본 수학 연산
        trigonometry.py              # 도메인 검증이 있는 삼각함수
        statistics.py                # 기술 통계 함수
        constants.py                 # 수학 상수
        combinatorics.py             # 팩토리얼, 순열, 조합
        logarithmic.py               # Log, ln, exp 함수
        validation.py                # 입력 검증, 도메인 체크
        errors.py                    # 수학 도메인 예외 타입
      storage/
        __init__.py
        dynamodb.py                  # DynamoDB 클라이언트, 테이블 연산
        api_keys.py                  # API 키 CRUD, 검증, 해싱
        usage.py                     # 사용량 미터링, 레이트 리미트 카운터
      logging.py                     # structlog 설정
  infrastructure/
    app.py                           # CDK 앱 진입점
    stacks/
      __init__.py
      api_stack.py                   # Lambda, API Gateway, 커스텀 도메인
      data_stack.py                  # DynamoDB 테이블
      monitoring_stack.py            # CloudWatch 대시보드, 알람
      auth_stack.py                  # Cognito user pool
      docs_stack.py                  # 문서 사이트를 위한 S3 + CloudFront
  tests/
    unit/
      test_arithmetic.py
      test_trig.py
      test_statistics.py
      test_expression_parser.py
      test_combinatorics.py
      test_logarithmic.py
      test_validation.py
      test_api_keys.py
    integration/
      test_api_evaluate.py
      test_api_arithmetic.py
      test_api_trig.py
      test_api_statistics.py
      test_api_auth.py
      test_api_rate_limiting.py
    accuracy/
      test_trig_accuracy.py
      test_arithmetic_accuracy.py
      test_statistics_accuracy.py
      test_logarithmic_accuracy.py
      test_expression_parser_accuracy.py
    conftest.py                      # 공유 픽스처 (FastAPI 테스트 클라이언트, moto 모킹)
  examples/
    api-endpoint/
      README.md
      example_endpoint.py
      test_example_endpoint.py
    math-function/
      README.md
      example_function.py
      test_example_function.py
    cdk-construct/
      README.md
      example_stack.py
  docs/
    static/                          # 문서 포털 소스 (Jinja2 템플릿)
  pyproject.toml
  uv.lock
  .python-version                    # 내용: 3.12
  .gitignore
  .pre-commit-config.yaml
  README.md
```

### Directory Rules

| Directory                 | Contains                                  | Rules                                                                                                              |
| ------------------------- | ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| `src/calcengine/`         | 모든 애플리케이션 소스 코드                | Python만. 설정 파일, 테스트, 문서 없음.                                                                            |
| `src/calcengine/engine/`  | 순수 수학 함수                            | AWS 임포트 없음. HTTP 임포트 없음. 부수 효과 없음. 순수 함수만. 모킹 없이 테스트 가능해야 함.                       |
| `src/calcengine/api/`     | FastAPI 라우트, 미들웨어, 모델            | HTTP 레이어만. 엔진 함수를 호출. 수학 로직을 포함하지 않음.                                                          |
| `src/calcengine/storage/` | DynamoDB 접근 레이어                       | 모든 AWS 데이터 접근이 여기에 격리됨. 비즈니스 로직 없음.                                                            |
| `infrastructure/`         | CDK 스택                                  | Python CDK만. 애플리케이션 코드 없음.                                                                              |
| `tests/`                  | 모든 테스트                               | `src/` 구조를 미러링. 별도의 `unit/`, `integration/`, `accuracy/` 디렉토리.                                         |
| `examples/`               | 패턴을 위한 템플릿 코드                   | 테스트와 README가 있는 동작하는 코드. 표준이 변경되면 업데이트됨.                                                  |

---

## Example and Template Code

### Example 1: API Endpoint Pattern

`examples/api-endpoint/README.md`:

```markdown
# API Endpoint Pattern

## What This Demonstrates
Standard pattern for adding a new calculation endpoint to CalcEngine.
Shows: route definition, Pydantic models, engine call, error handling, logging.

## When to Use
- Adding any new calculation endpoint
- Adding any new HTTP route to the API

## When Not to Use
- Internal engine functions (see math-function example)
- Infrastructure changes (see cdk-construct example)

## Customization Guide
| Element | Customize? | Notes |
|---------|-----------|-------|
| Route path and method | Yes | Follow /v1/{category}/{function} convention |
| Request/response models | Yes | Define Pydantic models specific to the endpoint |
| Engine function call | Yes | Call the appropriate engine module function |
| Error handling structure | No | Always use CalcEngineError hierarchy and error_response() |
| Logging calls | No | Always log with request_id, api_key_id, duration_ms |
| Response envelope | No | Always return {"result": ..., "expression": ..., "computation_time_ms": ..., "engine_version": ...} |
```

`examples/api-endpoint/example_endpoint.py`:

```python
"""Example: Standard API endpoint pattern for CalcEngine."""

import time

import structlog
from fastapi import APIRouter, Depends
from pydantic import BaseModel, Field

from calcengine.api.middleware.auth import get_api_key_id
from calcengine.api.models.errors import error_response
from calcengine.api.models.responses import CalculationResponse
from calcengine.engine.errors import CalcEngineError
from calcengine.engine.trigonometry import sin

logger = structlog.get_logger()

router = APIRouter()


class SinRequest(BaseModel):
    """Request model for sine calculation."""

    value: float = Field(..., description="Input angle")
    angle_mode: str = Field(
        default="radians",
        pattern="^(radians|degrees)$",
        description="Angle unit: 'radians' or 'degrees'",
    )


@router.post("/v1/trigonometry/sin", response_model=CalculationResponse)
async def calculate_sin(
    request: SinRequest,
    api_key_id: str = Depends(get_api_key_id),
) -> CalculationResponse | dict:
    """Calculate the sine of the given value."""
    start = time.perf_counter()

    try:
        result = sin(request.value, angle_mode=request.angle_mode)
        elapsed = (time.perf_counter() - start) * 1000

        logger.info(
            "calculation_completed",
            endpoint="/v1/trigonometry/sin",
            input_value=request.value,
            angle_mode=request.angle_mode,
            result=result,
            computation_time_ms=round(elapsed, 3),
            api_key_id=api_key_id,
        )

        return CalculationResponse(
            result=result,
            expression=f"sin({request.value})",
            computation_time_ms=round(elapsed, 3),
        )

    except CalcEngineError as e:
        elapsed = (time.perf_counter() - start) * 1000
        logger.warning(
            "calculation_failed",
            endpoint="/v1/trigonometry/sin",
            input_value=request.value,
            error_code=e.code,
            error_detail=str(e),
            computation_time_ms=round(elapsed, 3),
            api_key_id=api_key_id,
        )
        return error_response(e)
```

`examples/api-endpoint/test_example_endpoint.py`:

```python
"""Example: Standard test pattern for a CalcEngine API endpoint."""

import math

import pytest
from fastapi.testclient import TestClient

from calcengine.main import app


@pytest.fixture
def client() -> TestClient:
    """Create a test client with a mocked API key."""
    return TestClient(app)


class TestSinEndpoint:
    """Tests for POST /v1/trigonometry/sin."""

    @pytest.mark.unit
    def test_sin_zero_radians(self, client: TestClient) -> None:
        response = client.post(
            "/v1/trigonometry/sin",
            json={"value": 0, "angle_mode": "radians"},
            headers={"Authorization": "Bearer test-api-key"},
        )
        assert response.status_code == 200
        data = response.json()
        assert data["result"] == pytest.approx(0.0)
        assert "computation_time_ms" in data

    @pytest.mark.unit
    def test_sin_pi_over_2_radians(self, client: TestClient) -> None:
        response = client.post(
            "/v1/trigonometry/sin",
            json={"value": math.pi / 2, "angle_mode": "radians"},
            headers={"Authorization": "Bearer test-api-key"},
        )
        assert response.status_code == 200
        assert response.json()["result"] == pytest.approx(1.0)

    @pytest.mark.unit
    def test_sin_90_degrees(self, client: TestClient) -> None:
        response = client.post(
            "/v1/trigonometry/sin",
            json={"value": 90, "angle_mode": "degrees"},
            headers={"Authorization": "Bearer test-api-key"},
        )
        assert response.status_code == 200
        assert response.json()["result"] == pytest.approx(1.0)

    @pytest.mark.unit
    def test_sin_invalid_angle_mode(self, client: TestClient) -> None:
        response = client.post(
            "/v1/trigonometry/sin",
            json={"value": 1.0, "angle_mode": "gradians"},
            headers={"Authorization": "Bearer test-api-key"},
        )
        assert response.status_code == 422  # Pydantic validation error

    @pytest.mark.unit
    def test_sin_missing_auth(self, client: TestClient) -> None:
        response = client.post(
            "/v1/trigonometry/sin",
            json={"value": 0},
        )
        assert response.status_code == 401
```

### Example 2: Pure Math Function Pattern

`examples/math-function/README.md`:

```markdown
# Math Function Pattern

## What This Demonstrates
Standard pattern for implementing a pure math function in the engine layer.
Shows: function signature, type hints, domain validation, error raising, docstring format.

## When to Use
- Adding any new mathematical function to src/calcengine/engine/

## When Not to Use
- API endpoints (see api-endpoint example)
- Functions that require AWS or HTTP access (those belong in api/ or storage/)

## Key Rules
- No imports from api/, storage/, or any external service
- Pure functions only: same input always produces same output
- Raise CalcEngineError subclasses for domain errors, never return None or NaN
- Type hints on all parameters and return values
```

`examples/math-function/example_function.py`:

```python
"""Example: Standard pattern for a pure math function in CalcEngine engine layer."""

import math

from calcengine.engine.errors import DomainError


def log_base(value: float, base: float = 10.0) -> float:
    """Compute the logarithm of a value with the given base.

    Args:
        value: The number to compute the logarithm of. Must be positive.
        base: The logarithm base. Must be positive and not equal to 1.
              Defaults to 10 (common logarithm).

    Returns:
        The logarithm of value in the given base.

    Raises:
        DomainError: If value <= 0, base <= 0, or base == 1.
    """
    if value <= 0:
        raise DomainError(
            code="DOMAIN_ERROR",
            message=f"Cannot compute logarithm of {value}",
            detail="Logarithm is only defined for positive numbers",
            parameter="value",
        )

    if base <= 0:
        raise DomainError(
            code="DOMAIN_ERROR",
            message=f"Cannot use {base} as logarithm base",
            detail="Logarithm base must be positive",
            parameter="base",
        )

    if base == 1.0:
        raise DomainError(
            code="DOMAIN_ERROR",
            message="Cannot use 1 as logarithm base",
            detail="Logarithm base 1 is undefined (division by zero in change-of-base)",
            parameter="base",
        )

    return math.log(value) / math.log(base)
```

`examples/math-function/test_example_function.py`:

```python
"""Example: Standard test pattern for a pure math function."""

import math

import pytest
from hypothesis import given, strategies as st

from calcengine.engine.errors import DomainError
from calcengine.engine.logarithmic import log_base


class TestLogBase:
    """Tests for log_base function."""

    # --- Known values ---

    @pytest.mark.unit
    def test_log10_of_100(self) -> None:
        assert log_base(100, 10) == pytest.approx(2.0)

    @pytest.mark.unit
    def test_log2_of_8(self) -> None:
        assert log_base(8, 2) == pytest.approx(3.0)

    @pytest.mark.unit
    def test_ln_of_e(self) -> None:
        assert log_base(math.e, math.e) == pytest.approx(1.0)

    @pytest.mark.unit
    def test_log_of_1_any_base(self) -> None:
        assert log_base(1, 10) == pytest.approx(0.0)
        assert log_base(1, 2) == pytest.approx(0.0)
        assert log_base(1, math.e) == pytest.approx(0.0)

    # --- Default base ---

    @pytest.mark.unit
    def test_default_base_is_10(self) -> None:
        assert log_base(1000) == pytest.approx(3.0)

    # --- Domain errors ---

    @pytest.mark.unit
    def test_log_of_zero_raises_domain_error(self) -> None:
        with pytest.raises(DomainError, match="Cannot compute logarithm"):
            log_base(0)

    @pytest.mark.unit
    def test_log_of_negative_raises_domain_error(self) -> None:
        with pytest.raises(DomainError, match="Cannot compute logarithm"):
            log_base(-5)

    @pytest.mark.unit
    def test_log_base_zero_raises_domain_error(self) -> None:
        with pytest.raises(DomainError, match="Cannot use 0"):
            log_base(10, 0)

    @pytest.mark.unit
    def test_log_base_one_raises_domain_error(self) -> None:
        with pytest.raises(DomainError, match="Cannot use 1"):
            log_base(10, 1)

    @pytest.mark.unit
    def test_log_base_negative_raises_domain_error(self) -> None:
        with pytest.raises(DomainError, match="Cannot use -2"):
            log_base(10, -2)

    # --- Property-based: accuracy against stdlib ---

    @pytest.mark.accuracy
    @given(
        st.floats(min_value=1e-300, max_value=1e300, allow_nan=False, allow_infinity=False),
    )
    def test_log10_matches_stdlib(self, x: float) -> None:
        assert log_base(x, 10) == pytest.approx(math.log10(x), rel=1e-14)

    @pytest.mark.accuracy
    @given(
        st.floats(min_value=1e-300, max_value=1e300, allow_nan=False, allow_infinity=False),
    )
    def test_log2_matches_stdlib(self, x: float) -> None:
        assert log_base(x, 2) == pytest.approx(math.log2(x), rel=1e-14)

    # --- Property-based: mathematical identity ---

    @pytest.mark.accuracy
    @given(
        a=st.floats(min_value=1e-100, max_value=1e100, allow_nan=False, allow_infinity=False),
        b=st.floats(min_value=1e-100, max_value=1e100, allow_nan=False, allow_infinity=False),
    )
    def test_log_product_identity(self, a: float, b: float) -> None:
        """log(a * b) should equal log(a) + log(b)."""
        if a * b > 0:
            assert log_base(a * b, 10) == pytest.approx(
                log_base(a, 10) + log_base(b, 10), rel=1e-10
            )
```

### Example 3: CDK Construct Pattern

`examples/cdk-construct/README.md`:

```markdown
# CDK Construct Pattern

## What This Demonstrates
Standard pattern for defining a CDK stack for CalcEngine infrastructure.
Shows: Lambda function, API Gateway integration, DynamoDB table, IAM permissions.

## When to Use
- Adding new infrastructure resources to the project

## Key Rules
- All infrastructure in infrastructure/stacks/ directory
- One stack per logical group (api, data, monitoring, auth, docs)
- Use environment variables from CDK context, never hardcode
- Least-privilege IAM: each Lambda gets only the permissions it needs
```

`examples/cdk-construct/example_stack.py`:

```python
"""Example: Standard CDK stack pattern for CalcEngine."""

from aws_cdk import Duration, Stack
from aws_cdk import aws_apigatewayv2 as apigwv2
from aws_cdk import aws_dynamodb as dynamodb
from aws_cdk import aws_lambda as lambda_
from aws_cdk import aws_logs as logs
from aws_cdk.aws_apigatewayv2_integrations import HttpLambdaIntegration
from constructs import Construct


class ExampleApiStack(Stack):
    """Example stack showing Lambda + API Gateway + DynamoDB pattern."""

    def __init__(self, scope: Construct, construct_id: str, **kwargs) -> None:
        super().__init__(scope, construct_id, **kwargs)

        # DynamoDB table - single table design
        table = dynamodb.Table(
            self,
            "ExampleTable",
            partition_key=dynamodb.Attribute(
                name="PK", type=dynamodb.AttributeType.STRING
            ),
            sort_key=dynamodb.Attribute(
                name="SK", type=dynamodb.AttributeType.STRING
            ),
            billing_mode=dynamodb.BillingMode.PAY_PER_REQUEST,
            encryption=dynamodb.TableEncryption.AWS_MANAGED,
            point_in_time_recovery=True,
        )

        # Lambda function
        handler = lambda_.Function(
            self,
            "ExampleHandler",
            runtime=lambda_.Runtime.PYTHON_3_12,
            handler="calcengine.main.handler",
            code=lambda_.Code.from_asset("src/"),
            memory_size=256,
            timeout=Duration.seconds(30),
            environment={
                "TABLE_NAME": table.table_name,
                "LOG_LEVEL": "INFO",
            },
            log_retention=logs.RetentionDays.ONE_MONTH,
        )

        # Grant Lambda read/write access to DynamoDB (least privilege)
        table.grant_read_write_data(handler)

        # HTTP API Gateway with Lambda integration
        api = apigwv2.HttpApi(
            self,
            "ExampleHttpApi",
            api_name="calcengine-api",
            default_integration=HttpLambdaIntegration(
                "LambdaIntegration",
                handler,
            ),
        )
```

---

## 이 문서가 AI-DLC에 어떻게 공급되는가

| 섹션                            | AI-DLC Stage                       | 어떻게 사용되는가                                                              |
| ------------------------------- | ---------------------------------- | ------------------------------------------------------------------------------ |
| Project Technical Summary       | Workspace Detection                | 그린필드 분류, 팀 컨텍스트                                                     |
| Programming Languages           | Code Generation                    | Python 3.12 강제, 승인 없이 다른 언어 사용 안 함                                |
| uv Standards                    | Code Generation                    | 모든 의존성 작업에 uv 사용, pyproject.toml이 단일 설정 소스                     |
| Frameworks and Libraries        | Code Generation, NFR Design        | FastAPI + Pydantic + Mangum 스택, 금지 라이브러리 강제                          |
| Cloud Services Allow/Disallow   | Infrastructure Design              | MVP에 Lambda + API Gateway + DynamoDB만                                        |
| Architecture Pattern            | Application Design                 | 모듈러 모노리스, engine/ vs api/ vs storage/ 모듈 경계                          |
| API Design Standards            | Functional Design, Code Generation | 엔드포인트 컨벤션, 에러 코드, 응답 형식                                         |
| Security Requirements           | NFR Requirements, NFR Design       | 입력 검증 룰, eval() 없음, API 키 auth 패턴                                     |
| Testing Requirements            | Code Generation, Build and Test    | pytest + hypothesis, 90% 커버리지, accuracy 테스트 필수                         |
| Project Structure               | Code Generation                    | 정확한 디렉토리 레이아웃 및 파일 배치 룰                                        |
| Example Code                    | Code Generation                    | 엔드포인트, 엔진 함수, CDK 스택을 위한 정규(canonical) 패턴                     |
