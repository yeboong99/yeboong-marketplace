---
name: git-commit
description: Git 변경사항을 분석하여 컨벤셔널 커밋 형식의 한국어 커밋 메시지를 자동으로 생성합니다. mathieudutour/github-tag-action과 호환되는 시맨틱 버저닝을 지원합니다. 사용자가 커밋 메시지 작성, git commit, 변경사항 요약을 요청하거나 Git diff 결과가 있을 때 사용하세요.
---

# Git 커밋 메시지 생성기

이 스킬은 Git 변경사항을 분석하여 컨벤셔널 커밋(Conventional Commits) 형식의 한국어 커밋 메시지를 생성합니다.
`mathieudutour/github-tag-action`과 호환되어 커밋 메시지만으로 시맨틱 버저닝(SemVer)이 자동 관리됩니다.

---

> ## 🚨 작업 시작 전 필수 참고 파일 읽기
>
> **아래 두 파일을 반드시 먼저 읽은 후 작업을 시작하세요. 이 단계를 건너뛰면 잘못된 버전 태깅이나 품질 낮은 커밋 메시지가 생성될 수 있습니다.**
>
> | 파일 | 내용 | 읽어야 하는 이유 |
> |---|---|---|
> | [`references/semver-guide.md`](references/semver-guide.md) | 시맨틱 버저닝 규칙, github-tag-action 연동 방식 | 커밋 타입이 버전에 어떤 영향을 주는지 반드시 숙지 |
> | [`references/writing-tips.md`](references/writing-tips.md) | 작성 팁, 주의사항, 추가 예시 | 고품질 커밋 메시지 작성 기준 및 흔한 실수 방지 |

---

## 실행 흐름

### 1단계: 변경사항 확인

```bash
git diff --staged
```

staged 변경사항이 없다면:

```bash
git diff
```

---

### ⚠️ 2단계: 커밋 분리 판단 (필수 — 절대 생략 불가)

> **변경사항을 확인한 즉시 커밋 분리 여부를 판단하세요. 이 단계를 건너뛰고 바로 메시지를 작성하지 마세요.**

| 판단 기준 | 조치 |
|---|---|
| 서로 다른 타입(feat + fix, feat + chore 등)이 섞여 있음 | **반드시 분리** |
| 논리적으로 독립된 기능 2개 이상이 포함됨 | **반드시 분리** |
| 하나의 타입이지만 서로 관련 없는 파일 수정 | **분리 권장** |
| 하나의 논리적 변경사항 | 단일 커밋 유지 |

분리가 필요하다고 판단되면 **사용자에게 먼저 아래 형식으로 제안하고 확인을 받습니다**. 사용자가 명시적으로 거부한 경우에만 단일 커밋을 작성합니다.

```
⚠️ 변경사항에 여러 논리적 단위가 포함되어 있습니다. 아래와 같이 커밋을 분리하는 것을 강력히 권장합니다:

커밋 1: feat: [기능 A 설명]
  - 관련 파일: src/feature-a.ts

커밋 2: fix: [버그 B 설명]
  - 관련 파일: src/bug-b.ts

분리하여 커밋하시겠습니까?
```

---

### 3단계: 커밋 타입 결정

> 각 타입이 버전에 미치는 영향은 반드시 [`references/semver-guide.md`](references/semver-guide.md)를 확인하세요.

| 타입 | 사용 시점 | 버전 영향 |
|---|---|---|
| `feat` | 새로운 기능 추가 | **Minor** 증가 |
| `fix` | 버그 수정 | **Patch** 증가 |
| `perf` | 성능 개선 | **Patch** 증가 |
| `refactor` | 리팩토링 (기능 변경 없음) | default_bump |
| `docs` | 문서 수정 | default_bump |
| `style` | 코드 포맷팅 | default_bump |
| `test` | 테스트 코드 | default_bump |
| `chore` | 빌드, 의존성 관리 | default_bump |

---

### 4단계: Breaking Change 표기 (Major 버전 필요 시)

**방법 A — `!` 붙이기 (권장)**
```
feat!: 결제 API v2로 교체
```

**방법 B — 푸터에 `BREAKING CHANGE:` 명시**
```
feat: 결제 API v2로 교체

BREAKING CHANGE: /api/v1/payment 엔드포인트가 제거되었습니다.
```

두 방법 모두 `github-tag-action`이 **Major** 버전으로 인식합니다.

---

### 5단계: 메시지 작성

```
<타입>[!]: <제목>

<본문> (선택사항)

<푸터> (선택사항 — Fixes #123 또는 BREAKING CHANGE:)
```

- 제목: 50자 이내, 명령형, 마침표 없음
- 본문: 72자마다 줄바꿈
- 한국어로 작성

---

## 핵심 예시

### feat (Minor 버전 증가)
```
feat: 사용자 로그인 폼 컴포넌트 추가

- 이메일/비밀번호 입력 필드 구현
- 유효성 검사 로직 추가
```

### fix (Patch 버전 증가)
```
fix: 토큰 만료 시 무한 리다이렉트 버그 해결

토큰 갱신 로직에서 만료 시간 계산 오류 수정

Fixes #456
```

### Breaking Change (Major 버전 증가)
```
feat!: 인증 토큰 응답 구조 변경

기존 access_token → accessToken, expires_in → expiresIn
```

> 더 많은 예시(커밋 분리, refactor, chore 등)는 [`references/writing-tips.md`](references/writing-tips.md)를 참고하세요.
