# Technical Environment: Returns and Refunds Module — OrderFlow Platform

> **브라운필드 프로젝트.** 기존 스택이 베이스라인입니다. 새 코드는 정립된
> 패턴에 맞춰져야 합니다. 아래 나열되지 않은 선택은 기존 코드베이스를 따르세요 —
> 정당화 없이 새 패턴을 도입하지 마세요.

---

## 기존 스택 (반드시 보존)

| 레이어             | 현재 기술            | 버전      | 비고                                                                   |
| ------------------ | -------------------- | --------- | ---------------------------------------------------------------------- |
| Language           | TypeScript           | 5.x       | strict 모드. JavaScript 파일을 도입하지 마세요.                        |
| Runtime            | Node.js              | 20.x LTS  |                                                                        |
| API framework      | Express              | 4.x       | 모든 기존 서비스가 Express를 사용. Fastify나 Koa를 도입하지 마세요.    |
| Database           | PostgreSQL           | 15        | pg와 node-postgres 경유. ORM 없음 — 타입화된 쿼리 헬퍼와 raw SQL 사용. |
| Infrastructure     | AWS ECS Fargate      | —         | 서비스는 Docker 컨테이너로 배포. 모든 인프라에 CDK 사용.               |
| Message bus        | Amazon SQS           | —         | notification-service에서 비동기 이메일 디스패치에 사용.                |
| Auth               | AWS Cognito          | —         | API Gateway에서 JWT 토큰을 검증. 새 auth 레이어를 만들지 마세요.       |
| Package manager    | npm                  | 10.x      | yarn이나 pnpm을 도입하지 마세요.                                       |
| Test framework     | Jest                 | 29.x      | ts-jest 사용. 모든 테스트는 소스와 함께 `__tests__/`에 위치.           |
| Linter / formatter | ESLint + Prettier    | —         | 설정 파일은 레포 루트에 있음. 수정하지 마세요.                         |

---

## 추가할 항목 (이 모듈에 새로 도입)

- `order-service`와 동일한 구조를 따르는 새 `returns-service`
- 새 PostgreSQL 테이블: `return_requests`, `return_items`, `return_status_history`
- 고객 반품 폼과 운영 대시보드를 위한 새 React 컴포넌트
- 이 추가는 기존 테이블이나 서비스 계약을 수정해서는 안 됩니다

---

## 변경하지 말아야 할 항목

- `order-service`, `payment-service`, `notification-service` — 이 서비스들을 수정하지 마세요
- 기존 PostgreSQL 테이블 — 추가 마이그레이션만 허용 (새 테이블, 새 테이블의 새 컬럼)
- `notification-service` API 계약 — 문서대로 호출, 확장하지 마세요
- 기존 CDK 스택 — `returns-service`를 위한 새 스택 추가, 기존 스택 편집하지 마세요
- 프론트엔드 디자인 시스템 컴포넌트 — 기존 컴포넌트 사용, 대체품을 만들지 마세요

---

## 제거할 항목 / 도입하지 말 항목

| 금지                                | 이유                                                                                          | 대신 사용                                                                   |
| ----------------------------------- | --------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| ORMs (TypeORM, Prisma, Sequelize)   | 기존 코드베이스는 타입화된 헬퍼와 raw SQL을 사용. ORM을 도입하면 일관성이 깨집니다.           | node-postgres와 타입화된 쿼리 함수, 기존 패턴에 맞춤                        |
| Axios                               | 프로젝트는 네이티브 fetch를 사용 (Node 20 빌트인).                                            | fetch                                                                       |
| 새 CSS 프레임워크                   | 기존 프론트엔드는 Tailwind CSS를 사용.                                                        | Tailwind CSS, 기존 디자인 시스템 컴포넌트                                   |
| 새 state management 라이브러리      | 기존 프론트엔드는 React Context + useReducer를 사용.                                          | React Context + useReducer                                                  |
| 새 테스트 러너 (Vitest, Mocha)      | 프로젝트는 전반에 걸쳐 Jest를 사용.                                                           | Jest                                                                        |
| 별도 auth 서비스 또는 미들웨어      | Auth는 Cognito JWT를 통해 API Gateway에서 처리됨.                                             | Authorization 헤더로 전달된 JWT를 검증 (다른 서비스와 동일)                 |

---

## Security Basics

- 인증: Cognito JWT를 API Gateway에서 검증. 서비스는 `x-user-id`와 `x-user-role` 헤더를 받음 — 이를 신뢰하고 서비스에서 JWT를 다시 검증하지 마세요
- 권한 부여: 운영 대시보드 엔드포인트는 `role === 'operations'`를 요구 — 이 헤더를 확인
- 입력 검증: 처리 전에 Zod 스키마로 모든 request body를 검증
- PII: 반품 요청은 고객 이름과 주소를 포함 — 이 필드를 로깅하지 마세요
- 시크릿: 데이터베이스 자격 증명과 서비스 URL은 AWS Secrets Manager 경유, 기존 서비스와 동일

---

## 예제 코드 패턴

기존 코드베이스의 다음 패턴을 따르세요. 대안을 발명하지 마세요.

**서비스 엔드포인트 (Express route handler):**

```typescript
import { Router, Request, Response } from 'express';
import { z } from 'zod';
import { createReturnRequest } from '../domain/returns';
import { AppError } from '../errors';

const router = Router();

const CreateReturnSchema = z.object({
  orderId: z.string().uuid(),
  items: z.array(z.object({ orderItemId: z.string().uuid(), reason: z.string().min(1) })).min(1),
});

router.post('/returns', async (req: Request, res: Response) => {
  const parsed = CreateReturnSchema.safeParse(req.body);
  if (!parsed.success) {
    return res.status(400).json({ error: 'VALIDATION_ERROR', details: parsed.error.flatten() });
  }
  try {
    const result = await createReturnRequest(parsed.data, req.headers['x-user-id'] as string);
    return res.status(201).json(result);
  } catch (err) {
    if (err instanceof AppError) {
      return res.status(err.statusCode).json({ error: err.code, message: err.message });
    }
    throw err;
  }
});

export default router;
```

**데이터베이스 쿼리 함수:**

```typescript
import { pool } from '../db/pool';

export interface ReturnRequest {
  id: string;
  orderId: string;
  customerId: string;
  status: 'submitted' | 'approved' | 'rejected' | 'refunded';
  createdAt: Date;
}

export async function getReturnRequestById(id: string): Promise<ReturnRequest | null> {
  const { rows } = await pool.query<ReturnRequest>(
    'SELECT id, order_id AS "orderId", customer_id AS "customerId", status, created_at AS "createdAt" FROM return_requests WHERE id = $1',
    [id]
  );
  return rows[0] ?? null;
}
```

**Jest 테스트:**

```typescript
import { getReturnRequestById } from '../db/return-requests';
import { pool } from '../db/pool';

jest.mock('../db/pool');
const mockQuery = pool.query as jest.Mock;

describe('getReturnRequestById', () => {
  it('returns the request when found', async () => {
    mockQuery.mockResolvedValueOnce({ rows: [{ id: 'abc', orderId: '123', status: 'submitted' }] });
    const result = await getReturnRequestById('abc');
    expect(result?.id).toBe('abc');
  });

  it('returns null when not found', async () => {
    mockQuery.mockResolvedValueOnce({ rows: [] });
    const result = await getReturnRequestById('missing');
    expect(result).toBeNull();
  });
});
```
