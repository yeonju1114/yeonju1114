# 몽실이 서바이벌 v3 (도트 · 스토리 스테이지 · 보스 · 진화/합성)

| 파일 | 용도 |
|---|---|
| `mongsil-survivor.html` | Blogger 페이지 본문(HTML 보기)에 **통째로 붙여넣는** 파일 (div + style + script 한 덩어리, 약 66KB) |
| `mongsil-preview.html` | 블로그 테마 CSS 안에서 실행해 보는 미리보기용 |
| `DESIGN.md` | 설계 문서 (v0.1 ~ v0.4, 말미에 구현 현황) |
| `ui-directions.html` | UI 방향 비교 목업 (4안) |

- 야외 → 병원 → 집, 3개 스테이지를 한 판 안에서 이어서 진행(보스를 쓰러뜨리고 상자를 열면 컷신 후 다음 스테이지). 최종 보스 처치 시 엔딩.
- 이동만 직접 하고 공격은 자동.
- 외부 이미지·소리 없음. 스프라이트는 코드 안 문자열/도형. 글꼴(Press Start 2P)은 Google Fonts에서 불러오며 실패 시 monospace로 대체.
- 스크립트에 `<`, `&` 문자를 쓰지 않았음(Blogger 에디터의 이스케이프 방지).
- 브라우저 localStorage(`bgMongsil2Surv`, `bgMongsil2Win`, `bgMongsil2Seen`)에만 최고 기록과 본 컷신을 저장. 외부 전송 없음.
- 디버그 API는 `window.BG_DEBUG`가 참일 때만 노출(일반 방문자에게는 없음).
- 페이지 제목 `game`, 댓글 끔. 테마 변경 불필요.
