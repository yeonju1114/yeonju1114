# 몽실이 서바이벌 (Binary Garden 블로그 게임)

| 파일 | 용도 |
|---|---|
| `mongsil-survivor.html` | Blogger 페이지 본문(HTML 보기)에 **통째로 붙여넣는** 파일 (div + style + script 한 덩어리) |
| `mongsil-preview.html` | 블로그 테마 CSS 안에서 실행해 보는 미리보기용 |

- 3분 버티기 서바이벌. 이동만 직접 하고 공격은 자동(별 슈터, 하트 오브, 뽀글 폭탄).
- 스크립트에는 `<`, `&` 문자를 쓰지 않았습니다(Blogger 에디터의 이스케이프 방지).
- 최고 기록은 방문자 브라우저 localStorage(`bgMongsilBest`)에만 저장. 외부 전송 없음.
- 글꼴(Jua)은 Google Fonts에서 불러옵니다. 실패하면 시스템 글꼴로 대체됩니다.
- 페이지 제목 `game`, 댓글 끔. 테마 변경 불필요.
