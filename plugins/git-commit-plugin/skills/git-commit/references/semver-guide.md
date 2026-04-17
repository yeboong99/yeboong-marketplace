# 시맨틱 버저닝 가이드

`mathieudutour/github-tag-action@v6.2` 기준으로 커밋 메시지가 버전 태깅에 어떻게 연결되는지 설명합니다.

---

## 버전 결정 규칙

이 액션은 **Angular Commit Message Conventions**을 따르며, 커밋 메시지 타입에 따라 버전을 자동으로 결정합니다.

| 커밋 타입 / 키워드 | 버전 변화 예시 | 비고 |
|---|---|---|
| `BREAKING CHANGE` 또는 `!` | 1.2.3 → **2**.0.0 | Major |
| `feat` | 1.2.3 → 1.**3**.0 | Minor |
| `fix`, `perf` | 1.2.3 → 1.2.**4** | Patch |
| `refactor`, `docs`, `style`, `test`, `chore` | `default_bump` 설정에 따름 | 보통 Patch |

---

## Breaking Change 판단 기준

**Breaking Change로 표기해야 하는 상황:**

- 기존 API 엔드포인트 삭제 또는 경로 변경
- 요청/응답 JSON 필드명 변경 또는 삭제
- 함수/메서드 시그니처 변경 (파라미터 타입, 순서, 개수)
- 설정 파일 구조 변경으로 기존 설정이 동작하지 않는 경우
- 데이터베이스 스키마 변경으로 기존 데이터와 호환 불가

**Breaking Change가 아닌 상황:**

- 새로운 선택적(optional) 파라미터 추가
- 새로운 API 엔드포인트 추가 (기존 유지)
- 내부 구현 변경 (외부 인터페이스 동일)
- 버그 수정 (의도된 동작으로 복원)

---

## Breaking Change 표기 방법

### 방법 A — `!` 붙이기 (권장, 간결)

```
feat!: 결제 API v2로 교체

기존 v1 엔드포인트와 호환되지 않습니다.
```

```
fix!: 날짜 파라미터 타입 변경 (string → Date 객체)
```

### 방법 B — 푸터에 `BREAKING CHANGE:` 명시 (상세 설명 필요 시)

```
feat: 인증 API 응답 구조 개편

새로운 OAuth 2.0 기반 인증 플로우 적용

BREAKING CHANGE: access_token 필드가 accessToken으로 변경되었습니다.
expires_in 필드가 제거되고 expiresAt (ISO 8601) 로 교체되었습니다.
클라이언트는 응답 파싱 로직을 반드시 업데이트해야 합니다.
```

> 두 방법을 혼용하지 마세요. 방법 A는 간결함이 장점, 방법 B는 마이그레이션 가이드 작성이 필요할 때 사용합니다.

---

## default_bump 동작

`refactor`, `docs`, `style`, `test`, `chore` 타입은 액션의 `default_bump` 설정값에 따라 버전이 결정됩니다.

- **기본값**: `patch` (설정 변경 없으면 Patch 증가)
- **`false`로 설정 시**: 해당 커밋은 태그를 생성하지 않음

실제 프로젝트 GitHub Actions 워크플로우에서 `default_bump` 값을 확인하세요.

---

## 자주 하는 실수

| 실수 | 결과 | 올바른 방법 |
|---|---|---|
| API 구조 변경 후 `feat:`만 기재 | Minor 증가 (Major 누락) | `feat!:` 또는 `BREAKING CHANGE:` 추가 |
| 기능 추가에 `chore:` 사용 | default_bump만 증가 | `feat:` 사용 |
| 버그 수정에 `refactor:` 사용 | default_bump만 증가 | `fix:` 사용 |
| 두 가지 변경을 하나의 커밋에 혼합 | 버전 의미 왜곡 | 커밋 분리 |
