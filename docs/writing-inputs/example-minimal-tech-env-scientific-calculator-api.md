# Technical Environment: CalcEngine

## 언어 및 패키지 매니저

- **Python 3.12+**
- 모든 패키지 관리에 **uv** 사용 (pip, poetry, conda 사용 금지)
- 모든 프로젝트 및 도구 설정은 `pyproject.toml`
- `uv.lock`은 Git에 커밋

## 웹 프레임워크

- **FastAPI**와 요청/응답 검증을 위한 Pydantic v2
- AWS Lambda에서 FastAPI를 실행하기 위한 **Mangum**

## 클라우드 및 배포

- **AWS**, 단일 계정, `us-east-1`
- **Serverless**: API Gateway 뒤의 Lambda (HTTP API 타입)
- API 키 저장 및 사용량 미터링을 위한 **DynamoDB**
- 문서 사이트를 위한 **S3 + CloudFront**
- 모든 인프라에 **AWS CDK (Python)** 사용 -- 콘솔 수동 변경 금지

## 테스팅

- **pytest**와 pytest-cov (라인 커버리지 최소 90%)
- 수학 정확도 속성 기반 테스트를 위한 **hypothesis**
- 타입 검사를 위한 **mypy** strict 모드
- 린팅 및 포매팅을 위한 **ruff**
- 테스트에서 AWS 서비스 모킹을 위한 **moto**

## 사용 금지

| 금지                              | 이유                                                | 대신 사용                   |
| --------------------------------- | --------------------------------------------------- | --------------------------- |
| `eval()`, `exec()`, `compile()`   | 보안 -- 임의 코드 실행                              | AST 기반 표현식 파서        |
| Flask, Django                     | 프로젝트는 FastAPI 사용                             | FastAPI                     |
| requests                          | 비동기 이벤트 루프를 블록                           | httpx                       |
| sympy                             | MVP에 비해 너무 무거움                              | 커스텀 표현식 파서          |
| pandas                            | 불필요 -- 데이터프레임이 아닌 단일 계산             | 표준 Python                 |
| pip, poetry, pipenv               | 프로젝트는 uv만 사용                                | uv                          |
| black, flake8, isort              | ruff로 대체됨                                       | ruff                        |
| AWS EC2, ECS, RDS                 | MVP에 서버리스 모델 선호                            | Lambda, DynamoDB            |

## Security Basics

- `Authorization: Bearer {key}` 헤더를 통한 API 키 인증
- 키는 DynamoDB에 bcrypt 해시로 저장, 평문으로는 절대 로깅하지 않음
- 표현식 파서는 문자 허용리스트와 AST 평가를 사용 -- 동적 코드 실행 없음
- 표현식 길이는 4,096자로 캡, 중첩 깊이는 100레벨로 캡
- TLS 1.2+ 강제, HTTP 엔드포인트 없음
- 시크릿은 환경 변수나 코드가 아닌 AWS Secrets Manager에 저장

## 예제 코드 패턴

엔드포인트는 다음 구조를 따라야 합니다.

```python
from fastapi import APIRouter, Depends
from pydantic import BaseModel, Field

from calcengine.api.middleware.auth import get_api_key_id
from calcengine.api.models.errors import error_response
from calcengine.api.models.responses import CalculationResponse
from calcengine.engine.errors import CalcEngineError
from calcengine.engine.trigonometry import sin

router = APIRouter()


class SinRequest(BaseModel):
    value: float
    angle_mode: str = Field(default="radians", pattern="^(radians|degrees)$")


@router.post("/v1/trigonometry/sin", response_model=CalculationResponse)
async def calculate_sin(
    request: SinRequest,
    api_key_id: str = Depends(get_api_key_id),
) -> CalculationResponse | dict:
    try:
        result = sin(request.value, angle_mode=request.angle_mode)
        return CalculationResponse(result=result, expression=f"sin({request.value})")
    except CalcEngineError as e:
        return error_response(e)
```

수학 함수는 다음 구조를 따라야 합니다.

```python
import math

from calcengine.engine.errors import DomainError


def log_base(value: float, base: float = 10.0) -> float:
    """Compute logarithm of value with given base. Raises DomainError for invalid input."""
    if value <= 0:
        raise DomainError(
            code="DOMAIN_ERROR",
            message=f"Cannot compute logarithm of {value}",
            detail="Logarithm is only defined for positive numbers",
        )
    if base <= 0 or base == 1.0:
        raise DomainError(
            code="DOMAIN_ERROR",
            message=f"Invalid logarithm base: {base}",
            detail="Base must be positive and not equal to 1",
        )
    return math.log(value) / math.log(base)
```

테스트는 다음 구조를 따라야 합니다.

```python
import math
import pytest
from hypothesis import given, strategies as st
from calcengine.engine.errors import DomainError
from calcengine.engine.logarithmic import log_base


def test_log10_of_100() -> None:
    assert log_base(100, 10) == pytest.approx(2.0)


def test_log_of_negative_raises_domain_error() -> None:
    with pytest.raises(DomainError):
        log_base(-5)


@given(st.floats(min_value=1e-300, max_value=1e300, allow_nan=False, allow_infinity=False))
def test_log10_matches_stdlib(x: float) -> None:
    assert log_base(x, 10) == pytest.approx(math.log10(x), rel=1e-14)
```
