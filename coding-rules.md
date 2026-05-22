# 코딩 규칙 (에이전트 진입점)

Cursor 에이전트는 **`.cursorrules` → 본 문서(`docs/coding-rules.md`)** 순으로 규칙을 인지한다.  
구현·리팩터·버그 수정 전에 이 페이지를 읽고, 아래 링크된 문서를 **작업 영역에 맞게** 따른다.

> 규칙 본문은 여기에 길게 복사하지 않는다. **한 가지 규칙 = 한 파일**을 유지하고, 이 문서는 **목차·우선순위**만 담는다.

---

## 읽는 순서

| 순서 | 문서 | 용도 |
|------|------|------|
| 1 | [`.cursorrules`](../.cursorrules) | 하네스 계약 (항상 적용) |
| 2 | **본 문서** | 코딩 규칙 지도·영역별 분기 |
| 3 | [`CLAUDE.md`](../CLAUDE.md) | 구현 전 사고, 단순성, 수술적 diff, 완료 조건 |
| 4 | [`AGENTS.md`](../AGENTS.md) | Cursor 에이전트 하네스 (필독) |
| 5 | [`.cursor/rules/`](../.cursor/rules/) | 파일 패턴별 자동 규칙 (`*.mdc`) |
| 6 | [`CURSOR.md`](../CURSOR.md) | 재사용 프롬프트·Cursor 실무 |

**충돌 시:** 검증·범위가 더 엄격한 쪽을 따른다.

---

## 영역별 규칙

작업 경로에 따라 **추가로** 열 문서·규칙이다.

| 영역 | 경로 예시 | 규칙 |
|------|-----------|------|
| Frontend (React/Next) | `frontend/**` | [react-usestate-object.mdc](./Devops/Frontend/react-usestate-object.mdc) — 폼 `useState` 객체 압축, `window.alert` 금지; [CURSOR.md § 재사용 프롬프트](../CURSOR.md#재사용-프롬프트-복사해서-채팅에-붙여넣기) |
| Backend (FastAPI) | `backend/apps/**` | [ENTITY_RULE.md](./Devops/Backend/ENTITY_RULE.md) — PK `id` (int, autoincrement); Controller → Service → Repository, `logging` (`print` 금지) |
| Arcana 설계 | `docs/Arcana_*.md` | [Arcana_Agent_System.md](./Arcana_Agent_System.md) — 파이프라인·에이전트 역할 |
| FlutterFlow 스펙 | `docs/Arcana_FlutterFlow_Spec.md` | UI·플로우 변경 시 참고 |

---

## 공통 코딩 원칙 (요약)

상세는 `CLAUDE.md`에 있다. 여기서는 **분기용 한 줄**만 둔다.

- **범위:** 요청한 파일·동작만 수정한다.
- **맥락:** 주변 코드·네이밍·import 스타일을 먼저 읽고 맞춘다.
- **검증:** 가능하면 린트·실행·API 호출로 완료를 확인한다.
- **비밀:** `.env`, 키, 비밀번호를 커밋·로그·응답에 넣지 않는다.
- **문서:** 같은 규칙을 README·규칙·코드에 세 번 쓰지 않는다 — **한 곳 수정 + 링크**.

---

## 규칙 추가 방법

1. **파일/스택 전용** → `.cursor/rules/<이름>.mdc` 추가 (`globs`, `description` 설정).  
2. **도메인·프로세스** → `docs/` 아래 새 `.md` 작성 후 **본 문서 표에 한 줄 링크**.  
3. **전역 행동** → `CLAUDE.md` 또는 `AGENTS.md`만 수정 (짧게 유지).  
4. **항상 알릴 계약** → `.cursorrules`에 **링크 한 줄**만 추가 (본문 중복 금지).

---

## 관련 문서

- [docs/README.md](./README.md) — `docs/` 목차
- [README.md](../README.md) — 저장소 개요
