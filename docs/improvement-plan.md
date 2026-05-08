# claude-guide.html — Final Improvement Plan

> Codex `/codex:adversarial-review` ↔ Claude 검증·반박 끝장토론 결과.
> 작성: 2026-05-08

## 1. Context

`claude-guide.html`은 Claude Code 입문 워크플로우 가이드를 단일 HTML 페이지로 제공한다. 직전 두 차례 작업으로 ① ERP 도메인 콘텐츠를 일반 백엔드 예시(TypeScript / Drizzle / PostgreSQL) 로 치환했고, ② Light beige paper 테마를 DESIGN.md의 "Midnight Command Center" 다크 테마로 전환했다.

다크 테마 전환 직후 Codex adversarial-review에서 두 건의 잔여 이슈를 지적했다 — verdict: `needs-attention`. 본 문서는 그 두 건에 대해 Codex 주장을 검증·반박한 뒤 채택한 최종 개선안과 적용 결과를 기록한다.

## 2. Codex 지적 ↔ Claude 검증

### 2.1 SVG 다이어그램 색상 — Codex severity: high

**Codex 주장**

> SVG 내부 `<style>` 블록이 라이트 테마 hex(`#1a1816`, `#6a665f`, `#2a3d66` 등)를 그대로 박아둬서 다크 배경에서 라벨/화살표가 저-콘트라스트가 된다. 이건 cosmetic이 아니라 본문 설명을 동반하는 다이어그램이라 가독성 자체가 떨어진다.

**Claude 반박 (부분 인정)**

런타임 contrast는 이미 해결돼 있었다. `claude-guide.html` 외부 CSS 블록에 `figure svg .stage1 { ... !important; }` 식의 오버라이드 24줄을 미리 주입해뒀기 때문이다. Codex는 외부 CSS를 보지 않고 SVG 내부 `<style>`만 보고 과대평가했다.

다만 다음 세 가지는 Codex가 옳다:

1. 내부 `<style>`이 dead-code로 남아 유지보수성 저하
2. `font-family: 'Inter Tight'`는 더 이상 로드되지 않는 폰트 → fallback에 의존
3. `!important` 오버라이드는 브리틀 — 누군가 SVG `<style>`을 수정해도 의도대로 적용 안 됨

→ **결정: severity high → medium 으로 격하한 뒤 정리.**

### 2.2 부록 일관성 — Codex severity: medium

**Codex 주장**

> 부록 제목이 "템플릿 & 참고"인데 실제 템플릿 챕터(A.1 ERP CLAUDE.md 템플릿)는 ERP 제거 작업으로 삭제됐다. 게다가 남아있는 슬래시 커맨드 본문에는 `pnpm db:migrate -- --validate-only` 같은 스택 특정 명령이 그대로 박혀 있다 — "즉시 사용"을 약속하는 챕터가 실제로는 Node + pnpm 환경 가정이라 다른 스택 사용자는 복붙시 실패한다.

**Claude 검증 (전면 인정)**

`claude-guide.html:2070` h1 = "템플릿 & 참고", part-summary는 템플릿을 약속. 그러나 본문에 템플릿 챕터 없음. `claude-guide.html:2130`의 `pnpm db:migrate -- --validate-only`도 일반 가이드의 톤과 충돌.

→ **결정: 부록을 "참고 모음" 방향으로 재정의. 템플릿 복원은 Ch9에 이미 풀 예시가 있어 중복 위험 → 채택하지 않음.**

## 3. 채택한 개선 방안

### A. SVG 다이어그램 정리

| 항목 | 변경 내용 |
| --- | --- |
| 색상 hex | `claude-guide.html` 전체에서 12개 legacy hex를 다크 테마 등가물로 일괄 치환 (PowerShell `Replace`) |
| 폰트 | `'Inter Tight'` → `'Space Grotesk'`, `'Crimson Pro'` (`.num` 클래스) → `'Inter'` |
| 외부 오버라이드 | `figure svg ...!important` 블록(38줄) 삭제 → 한 줄짜리 fallback만 유지 |

색상 매핑표 (light → dark):

| Legacy | Replacement | Role |
| --- | --- | --- |
| `#1a1816` | `#ffffff` | 라벨 헤딩 (ink → ghost-white) |
| `#6a665f` | `rgba(212,228,250,0.5)` | 서브 라벨/화살표 (ink-mute → muted blue-white) |
| `#2a3d66` | `#b6d9fc` | accent stroke/fill (navy → celestial-light) |
| `#e8ecf4` | `rgba(182,217,252,0.08)` | accent-soft fill |
| `#fff4dc` | `rgba(102,58,243,0.10)` | yellow-soft → violet tint |
| `#c4923a` | `#663af3` | gold highlight → neon-violet |
| `#ecf3e6` | `rgba(140,214,163,0.08)` | green-soft fill |
| `#4a6b3a` | `#8cd6a3` | good (saturated green → light good) |
| `#faf7f2` | `rgba(186,214,247,0.04)` | paper bg → glass tint |
| `#b8c4dd` | `#d1e4fa` | light blue text → arctic-mist |
| `#d9d2c4` | `rgba(186,215,247,0.20)` | rule line → border-subtle |
| `#f3ede3` | `rgba(186,214,247,0.04)` | paper-2 → glass tint |

### B. 부록 일관성 — Option B1 채택

| 위치 | 변경 |
| --- | --- |
| `claude-guide.html:2070` | h1 "템플릿 & 참고" → **"참고 모음"** |
| `claude-guide.html:2071` | part-summary 템플릿 약속 제거 → "자주 쓰는 슬래시 커맨드 예시와 외부 참고 링크" |
| `claude-guide.html:2072` | part-scope "참고 — 즉시 사용" → "참고 — Quick Reference" |
| `claude-guide.html:2077` | A.1 h2 "슬래시 커맨드 모음 — 즉시 사용" → **"슬래시 커맨드 예시"** |
| `claude-guide.html:2081-2083` | A.1 도입부에 callout-note 면책 추가: "예시 명령은 참고용. 빌드·테스트·마이그레이션은 프로젝트 스택에 맞춰 교체" |
| `claude-guide.html:2129-2133` | `pnpm db:migrate -- --validate-only` → `{프로젝트의 마이그레이션 검증 명령}` + 3-stack 예시 (pnpm / dotnet ef / alembic) |

### C. 본 문서

- `docs/improvement-plan.md` 신규 생성
- 향후 작업자가 디자인 의도와 결정 이력을 추적 가능하도록 Codex ↔ Claude 토론 결과를 보존

## 4. 변경 파일

- `claude-guide.html` — SVG 색상/폰트 12+2개 일괄 치환, 외부 SVG override 38줄 삭제, 부록 6곳 수정
- `docs/improvement-plan.md` — 신규

## 5. Verification

| 단계 | 방법 | 통과 기준 |
| --- | --- | --- |
| 1. Legacy 색상 잔존 0 | `grep -E "#6a665f|#1a1816|#2a3d66|..." claude-guide.html \| wc -l` | `0` |
| 2. SVG 시각 검증 | Chrome에서 8개 figure (Ch1, Ch2, Ch5, Ch6×2, Ch6 pillar, Ch11 Operator, Ch11 Q&A) 라벨/화살표/배경이 dark bg 위에서 구분 가능 | 모든 라벨 가독, 화살표 식별 가능 |
| 3. 인쇄 검증 | Chrome `Ctrl+P` 미리보기 | SVG·코드·callout이 흰 배경에서도 읽힘 |
| 4. 부록 일관성 | 부록 h1 / summary / A.1 h2 / 도입 callout / migration placeholder | 동일한 메시지 ("예시이며 적용 전 수정 필요") |
| 5. 회귀 점검 | 외부 `figure svg ...!important` 제거 후 모든 다이어그램 정상 렌더 | SVG 깨짐 없음 |

## 6. Out of Scope

- Ch9 / Ch10 본문 코드 예시(TypeScript/Drizzle/PostgreSQL)는 이미 일반화됐고 Codex도 이슈 제기하지 않음 → 추가 placeholder화 안 함
- 모바일 사이드바 햄버거 메뉴, 다크/라이트 토글, 서치 기능 같은 신규 UX는 별도 트랙
- SVG → React 컴포넌트 전환 같은 구조 변경은 본 가이드의 "단일 HTML 파일" 컨셉과 어긋나므로 검토 대상 아님

## 7. Status

| Item | Status |
| --- | --- |
| A. SVG 정리 | ✅ 적용 |
| B. 부록 일관성 | ✅ 적용 |
| C. docs 산출 | ✅ 본 문서 |
| 시각 회귀 검증 | ⏳ 사용자 브라우저 확인 대기 |
