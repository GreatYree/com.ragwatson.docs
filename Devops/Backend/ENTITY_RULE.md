# Backend — 엔티티·테이블 PK 규칙

이 프로젝트의 **모든 DB 테이블**은 시스템 내부용 **정수형 자동 증가 기본 키**를 반드시 갖는다.  
컬럼 이름은 **`id`로 통일**한다. (`user_id`, `order_id` 등은 **비즈니스 식별자**용으로 별도 두고, PK `id`와 혼동하지 않는다.)

> 상세 구현은 **SQLModel** 기준. 기존 SQLAlchemy `DeclarativeBase` 모델도 동일 의미로 `id: Mapped[int]` + `autoincrement=True`를 유지한다.

---

## 필수 규칙

| 항목 | 규칙 |
|------|------|
| 기본 키 컬럼명 | **`id`** (다른 이름 금지: `pk`, `idx`, `user_pk` 등) |
| 타입 | **`int`** (DB: `INTEGER` / `SERIAL` / `BIGSERIAL` — 프로젝트 기본은 `int`) |
| 역할 | 시스템 내부용 자동 증가 고유 번호 |
| `primary_key` | `True` |
| 신규 INSERT | `id`는 보통 `None` / 미지정 → DB가 자동 할당 |

---

## SQLModel — 기본 PK 필드 (참조 코드)

```python
from typing import Optional

from sqlmodel import Field, SQLModel


class ExampleEntity(SQLModel, table=True):
    __tablename__ = "examples"

    # 시스템 내부용 자동 증감 고유 번호 (기본 키)
    id: Optional[int] = Field(
        default=None,
        primary_key=True,
        sa_column_kwargs={"name": "id"},  # DB 컬럼명: id
    )

    # 비즈니스 식별자는 id와 별도 컬럼으로 정의
    # example_code: str = Field(max_length=64, unique=True, index=True)
```

### 새 엔티티 체크리스트

1. `SQLModel` + `table=True`, `__tablename__` 명시
2. **첫 번째 필드(관례)** 로 `id` 정의 (위 패턴 복사)
3. 로그인 ID·이메일·외부 키 등은 **`id`가 아닌** 전용 컬럼명 사용
4. Alembic 마이그레이션·`init_db` 메타데이터에 모델 import 등록
5. Repository는 조회·삭제 시 `entity.id` 사용, API 노출용 문자열 키와 구분

---

## SQLAlchemy 2.0 (`database.Base`) — 동등 표현

현재 `secom` 등 일부 모델이 `DeclarativeBase`를 쓰는 경우:

```python
from sqlalchemy.orm import Mapped, mapped_column

from database import Base


class User(Base):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(primary_key=True, autoincrement=True)
    # ...
```

신규 테이블도 **`id` + int + autoincrement** 원칙은 동일하다. SQLModel로 통일할 때는 위 `Field` 패턴으로 옮긴다.

---

## 금지·주의

- PK를 `user_id`(문자열)나 UUID만으로 두고 **`id` int PK 없음**
- 테이블마다 PK 컬럼명을 다르게 짓기 (`member_id`를 PK로 사용 등)
- `id`에 수동으로 0, 1, 2를 넣어 충돌 유발 (테스트 시드 제외)
- 응답·로그에 `id`와 비밀번호를 함께 과도 노출

---

## 재사용 프롬프트 (복사용)

```
이 프로젝트 ENTITY 규칙(docs/Devops/Backend/ENTITY_RULE.md)에 맞게 엔티티/테이블을 만들어 줘.
- 모든 테이블: int 자동 증가 PK, 컬럼명은 id 로 통일
- SQLModel 사용 시 id 는 Optional[int] = Field(default=None, primary_key=True, sa_column_kwargs={"name": "id"})
- 비즈니스 키(user_id, email 등)는 id 와 별도 컬럼
- 요청 범위 밖 테이블은 건드리지 마
```

**한 줄 버전:**

```
ENTITY_RULE.md 따라 SQLModel 엔티티 추가해 줘. PK는 int autoincrement id 필수.
```

---

## 관련 코드

| 경로 | 설명 |
|------|------|
| `backend/apps/secom/app/models/user_entity.py` | User 테이블 (`id` + `user_id` 분리) |
| `backend/apps/database.py` | `Base`, `init_db()` |
| `backend/requirements.txt` | `sqlmodel`, `sqlalchemy`, `alembic` |
