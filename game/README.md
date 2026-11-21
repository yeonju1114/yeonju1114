# Uptime Survivor (Binary Garden 블로그 게임)

| 항목 | 내용 |
|---|---|
| `uptime-survivor.html` | Blogger 페이지 본문(HTML 보기)에 **통째로 붙여넣는** 파일 (div + style + script 한 덩어리) |
| `uptime-survivor-preview.html` | 블로그 테마 CSS 안에서 실행해 보는 미리보기용 |

- 스크립트에는 `<`, `&` 문자를 쓰지 않았습니다(Blogger 에디터가 이스케이프하는 것을 피하기 위함).
- 점수는 방문자 브라우저 localStorage(`bgUptimeBest`)에만 저장됩니다. 외부 전송 없음.
- 페이지 제목 `game`, 댓글 끔. 테마 변경 불필요.
