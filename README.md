# Claude Code Guide

Claude Code 워크플로우를 다루는 한국어 단일 페이지 핸드북.

**Live:** https://claude-guide-eosin.vercel.app/

## 구조

- `index.html` — 본문 전체. 빌드 단계 없음, 프레임워크 없음.
- `DESIGN.md` — *Midnight Command Center* 다크 테마 디자인 시스템 레퍼런스.
- `vercel.json` — 정적 호스팅 캐시 헤더만 설정.

## 디자인

*Midnight Command Center* — 어두운 방에서 은은하게 빛나는 하이테크 대시보드를 모티브로 한 다크 테마.

- **팔레트**: `Midnight Abyss #05060f` 배경 위에 `Comet #d8ecf8` 본문, `Celestial Light #b6d9fc` 강조, `Neon Violet #663af3` CTA 전용.
- **타이포그래피**: 디스플레이 제목은 Space Grotesk, 본문은 Inter (`-0.01em` 트래킹), 코드는 IBM Plex Mono.
- **표면 처리**: 12–16px 라운드 카드 + 인셋 섀도우의 frosted glass 질감, 999px pill 라디우스 배지.
- 자세한 토큰·컴포넌트 사양은 `DESIGN.md` 참조.
