# Binary Garden theme — 개선 기록

현재 적용 대상: **`binary-garden-v4.xml`** (v3 는 `binary-garden-v3.xml` 로 남겨 둠)

## v4.0 "Runbook Console" (2026-11-13)
선택 과정과 방향 카드: `../.oil-ui/blog-redesign/design-notes.md`, 비교 페이지: `../.oil-ui/blog-redesign/explore/style-explorer.html`

- **첫 화면 = 글 목록.** 슬로건 히어로·토픽 타일·썸네일 카드 제거. 1440×900 에서 글 12개가 첫 화면에 보임(v3 는 0개).
- **명령행 검색(`$ grep -i`)이 주인공.** 입력 즉시 목록이 그 자리에서 검색 결과로 바뀌고 일치어 앰버 표시, `n matches`, ↑↓/Enter/Esc, `/`·Ctrl/⌘+K. 글 화면에서는 명령행 아래 결과 패널. JS 가 꺼져도 Enter 로 Blogger 기본 검색(`/search?q=`) 동작.
- **좌 INDEX 트리**(/web /was … 라벨 수 포함, 현재 라벨 강조), **우 상태 패널**(글 수·마지막 작성·최근 수정 글·whoami+SNS·툴박스 접힘).
- **글 화면**: 경로 `~/logs/Tomcat/`, `[TAG]`, 제목, 날짜·읽기 시간·수정일, `##`/`###` 표시 제목, 터미널 코드블록(앰버/녹색 문법 색), `[WARN]`/`[TIP]` 콜아웃, 우측 ON THIS PAGE(스크롤 위치 표시), 공유, whoami 카드, 이전/다음, `$ grep --related` 관련 글, `# comments`(Disqus/giscus 탭).
- **색**: 따뜻한 흑연 #141210 + 앰버 #f5a524 (다크 기본). 라이트 "종이 로그" #f7f4ee + #a86400. 보라·파랑 발광, 매트릭스 배경 제거.
- **모바일**: 메뉴 서랍(트리), 전폭 명령행(16px), 2줄 행(날짜·태그/제목), 상태 패널은 목록 아래.
- **기존 글 호환**: post-summary, callout(note/tip/warn/danger), steps, env-table, param-table, ts-step, ver, ref-links, post-changelog, 구형 메타 줄 숨김 모두 유지.
- **검증**: XML·JS 구문 OK, 1440/390 가로 넘침 없음, `preview/test-grep.mjs` 9개 기능 검사 통과. Blogger 실제 저장·실데이터 렌더링은 미확인.
- **Blogger 설정 권장**: 메인 페이지 게시물 수 15~20, 사이트 피드 "전체" 또는 "짧게".


적용 방법: **`APPLY-GUIDE.md`** 참고. 요약: `binary-garden-v4.xml` 을 (Blogger → 테마 → 백업/복원 → 업로드, 또는 HTML 편집에 통째로 붙여넣기)
**적용 전에 현재 테마를 꼭 백업(다운로드)하세요.**

## 진행 계획 (v4 "Runbook Console" 기준, 하루 2회)

| 라운드 | 내용 | 상태 |
|---|---|---|
| 1 | v3 골격 | 완료 (v3, 폐기 아님·롤백용) |
| 2 | 디자인 재설계: 방향 비교(A/B/C) → A 운영 로그 선택 → v4 구현 | 완료 (2026-11-13) |
| 3 | **엔지니어 도구 페이지** (`/p/tools.html`, 아래 "도구 페이지 사양") | 완료 (2026-11-13) |
| 4 | unslop 다듬기 + 접근성(키보드·대비·스크린리더) + 모바일 세부 (도구 페이지 포함) | 완료 (2026-11-14) |
| 5 | 검색 고도화(`label:tomcat` 같은 필터 문법, 최근 검색어, 결과 정렬) | 완료 (2026-11-14) |
| 6 | 성능(LCP/CLS·폰트 로딩)·SEO(구조화 데이터·OG)·기존 글 호환 점검 | 완료 (2026-11-15) |
| 7 | 최종 QA, 적용 가이드(Blogger 페이지 생성 방법 포함) 정리, 릴리스 | 완료 (2026-11-15) |

## 도구 페이지 사양 (라운드 3)

목표: 흩어진 위젯을 한 페이지에서 "골라서 실행"하는 엔지니어 도구 콘솔로 모은다. 디자인은 v4 Runbook Console 문법 그대로(흑연+앰버, 모노, 카드·그라데이션 없음).

- **Blogger 연결**: 도구 코드(CSS/JS)는 테마에 둔다. Blogger 관리자에서 만든 페이지(`/p/tools.html`) 본문에는 `<div id="toolbox-app"></div>` 한 줄만 둔다. 테마는 `#toolbox-app` 이 있을 때만 도구 화면을 그린다(다른 페이지 성능 영향 없음). 페이지 생성은 사용자가 직접 한다.
- **화면**: `$ ls ./toolbox` 제목. 좌측 도구 목록(이름 + 한 줄 설명), 우측 실행 패널. 데스크톱 2열, 모바일은 목록 → 선택 시 패널(뒤로 버튼). 선택 상태 = 앰버 글자 + 좌측 2px 막대.
- **조작**: 클릭/탭, ↑↓ 또는 숫자키로 선택, URL 해시(`#jvm`, `#cidr`)로 바로 열기·공유, 결과 옆 복사 버튼(명령어/값). 계산은 모두 브라우저 안에서만(외부 전송 없음).
- **도구 목록**:
  1. `bytes` 용량 단위 변환 (B/KB/MB/GB/TB, 1024 기준)
  2. `usage` 사용률 % + 남은 양, 70/90% 경고 색
  3. `time` 시간 단위 변환 (ms/s/m/h/d)
  4. `jvm` JVM 힙 계산 → `-Xms -Xmx -XX:MaxMetaspaceSize` 명령어 복사
  5. `ports` 포트 표 + 포트 번호/이름 필터
  6. `epoch` 유닉스 시간(초/밀리초) ↔ 날짜(KST/UTC) 양방향
  7. `cidr` CIDR/서브넷 계산 (네트워크·브로드캐스트·호스트 범위·개수, IPv4)
  8. `chmod` 권한 체크박스 ↔ 숫자(640) ↔ 기호(rw-r-----)
  9. `cron` 5필드 cron 표현식을 한국어로 풀이 + 다음 실행 5회
  10. `encode` URL·Base64 인코딩/디코딩 (UTF-8)
  11. `cert` 인증서 만료일 입력 → 남은 일수, 30일 이하 경고
  12. `cmd` keytool/openssl 명령어 만들기 (PKCS12 생성, 체인 확인, 만료일 조회, keystore 목록)
- **홈 우측 패널**: 접힌 위젯 5개 대신 `$ ./toolbox →` 바로가기(도구 이름 몇 개 미리보기). 상단 내비에도 `tools` 링크.
- **검증**: `build_preview.py` 에 `tools.html` 목업(본문 `#toolbox-app`) 추가, `shoot.mjs` 에 데스크톱/모바일 도구 화면 추가, `test-grep.mjs` 와 같은 방식의 `test-tools.mjs` 로 도구별 계산 결과 검사(예: 192.168.10.0/24 → 254 hosts, chmod 640 → rw-r-----, 8192MB 60% → -Xmx4915m).

## v3.0 에서 바뀐 것

1. **홈페이지형 레이아웃** — 히어로(검색·인기 라벨·SNS·통계), 토픽 타일(WEB/WAS/OS/SECURITY…), 첫 글 강조 카드 + 2열 그리드, 우측 엔지니어 툴박스.
2. **트렌드 반영** — 라이트/다크 전환(OS 설정 기본 아님, 저장값 우선·기본 다크), 커맨드 팔레트 검색(`/` 또는 `Ctrl/⌘+K`), 읽기 진행바, 데스크톱 우측 스크롤 스파이 목차, 관련 글, 공유 버튼, 브레드크럼, 썸네일 카드.
3. **SNS 댓글** — Disqus(Google·Facebook·X 로그인) + giscus(GitHub 로그인). 둘 다/하나만/없음 모두 지원. 스크롤 근처 도달 시 지연 로드.
4. **내 SNS 연동** — `BLOG_CONFIG.social` 에 URL 만 넣으면 헤더 드로어·홈 히어로·글 하단 작성자 카드·푸터에 아이콘 표시.
5. **미들웨어 정체성 유지** — 터미널 코드블록, 콜아웃/TL;DR/단계/트러블슈팅 컴포넌트, 바이트·%·시간·JVM·포트 툴박스, 매트릭스 배경(데스크톱 다크 전용, 옅게) 유지.
6. **모바일** — 햄버거 드로어(아코디언 카테고리), 전체화면 검색, 16px 입력(iOS 확대 방지), 48px 터치 영역, 코드/표 가로 스크롤, 접이식 목차, 맨 위로 버튼, 툴박스 기본 접힘.
7. **검색** — 블로그 JSON 피드를 1시간 캐시해 제목(가중치 높음)·라벨·요약을 AND 검색, 일치어 강조, 라벨 칩 필터, 방향키/Enter. 피드 실패 시 Blogger 기본 검색(`/search?q=`)으로 폴백.

## 설정 방법 (BLOG_CONFIG, 파일 하단 `<script>` 안)

- **SNS**: `social[].url` 입력. 비워두면 숨김. 이메일은 `mailto:...`.
- **Disqus 댓글**: disqus.com 에서 사이트 등록 → `disqusShortname` 입력. Disqus 관리자에서 Google/Facebook/X 로그인 허용.
- **giscus 댓글**: public GitHub 저장소 + Discussions 활성화 + giscus 앱 설치 → <https://giscus.app/ko> 에서 `repo / repoId / categoryId` 복사.
- **홈 문구**: `<h1 id='hero-title'>` 와 `hero-sub` 문단을 직접 수정.
- **작성자 소개**: `author.name`, `author.bio`.

## Blogger 설정에서 같이 확인할 것

- 설정 → 사이트 피드 → **전체** 또는 **짧게** (없음이면 검색 색인 불가, 기본 검색으로 폴백).
- 설정 → 게시물 표시 개수 → 홈 **10~12개** 권장 (2열 그리드).
- 썸네일은 글 첫 이미지가 자동 사용됩니다 (`data:post.featuredImage`).
- Blogger 기본 댓글은 v3 에서 사용하지 않습니다 (SNS 로그인 불가). 기존 Blogger 댓글은 삭제되지 않고 남아 있습니다.

## 1차 수정 (v3.0.1, 2026-11-13) — 로컬 미리보기 기반

- 미리보기 도구 추가: `preview/build_preview.py` (테마에서 CSS/JS/헤더/툴박스/푸터를 그대로 잘라 목업 페이지 생성), `preview/shoot.mjs` (헤드리스 Chrome 기기 에뮬레이션 캡처 → `preview/shots/`)
- 썸네일 없는 글: 빈 공간 대신 터미널 스타일 자동 썸네일(`$ tail -f <라벨>.log`) → 그리드 높이 균일
- 본문 인라인 코드가 단어 중간에서 끊기던 문제(`server.xm|l`) 수정
- 모바일 홈: 토픽 타일 7개가 세로로 쌓이던 것을 가로 스와이프 행으로 변경
- 확인 결과: 1440px / 390px 모두 가로 넘침 없음, 다크/라이트, 검색 팔레트, 모바일 드로어 정상

## 알려진 위험 / 검증 상태

- XML 구문, JS 구문은 로컬에서 검증 완료. **Blogger 서버 저장 검증과 실제 브라우저 렌더링은 아직 미확인** → 라운드 2 에서 확인.
- `data:post.featuredImage` 가 저장 시 오류를 내면 `post-card` 안의 `<b:if cond='data:post.featuredImage'>…</b:if>` 블록만 지우면 됩니다.

## 라운드 3 결과 (2026-11-13) — 엔지니어 도구 페이지

백업: `backup/2026-11-13_round3_binary-garden-v4.xml`

**바꾼 것**
- 테마가 `#toolbox-app` 요소를 찾았을 때만 도구 콘솔을 그림(`Toolbox.init`). 다른 페이지에서는 아무것도 하지 않음. 마운트되면 페이지 껍데기(경로·제목·날짜)와 목차를 숨기고 전폭 2열로 바꿈. CSS `:has()` 로 JS 실행 전에도 껍데기가 깜빡이지 않음.
- 화면: `$ ls ./toolbox` 제목, 좌 목록(번호·이름·한 줄 설명)·우 실행 패널(`$ ./jvm`, 제목, 용도 설명, 입력, 결과 행 + copy). 선택 = 앰버 글자 + 좌측 2px 막대. 모바일(≤900px)은 목록 → 탭하면 패널(‹ ls ./toolbox 뒤로).
- 조작: 클릭/탭, ↑↓, 숫자키(1–9, 두 자리 10–12), URL 해시(`#jvm` `#cidr` …)로 바로 열기, 결과 copy 버튼. 계산은 모두 브라우저 안.
- 도구 12개: bytes, usage(70/90% 경고), time(+읽기 쉬운 형태), jvm, ports(번호/이름 필터, 기존 표 + AJP·shutdown·DB 포트), epoch(초/밀리초 자동 판별, KST/UTC 양방향), cidr(`ip/prefix` 와 `ip netmask` 둘 다), chmod(체크박스↔숫자↔기호), cron(한국어 풀이 + 다음 5회, `@daily`·`MON-FRI` 지원), encode(URL·Base64, UTF-8), cert(날짜·`notAfter=`·keytool `Valid until` 줄 인식, 30일 이하 WARN), cmd(PKCS12 생성·체인 확인·만료일 조회·keystore 목록 + CSR·JKS 변환).
- 기존 바이트·사용률·시간·JVM 계산식과 표시 형식은 그대로 옮김(8192MB 60% → `-Xms2457m -Xmx4915m -XX:MaxMetaspaceSize=512m`).
- 홈 우측: 접힌 위젯 5개 삭제 → `$ ./toolbox →` 바로가기 + 도구 이름 5개(각각 `#해시` 링크). 상단 내비 `tools`(도구 페이지에서 앰버), 모바일 메뉴 서랍과 푸터에도 링크.
- preview: `build_preview.py` 에 `tools.html`(Blogger 정적 페이지 구조, 본문은 `#toolbox-app` 한 줄), `shoot.mjs` 에 도구 화면 6장, `test-tools.mjs` 신규(26개 검사).

**검증**
- expat XML well-formed OK, 인라인 스크립트 2개 `node --check` OK, 중복 id 없음(Blog1·Label1 의 includable `main` 은 원래 구조), b:skin 은 CSS 만.
- `test-grep.mjs` 9/9, `test-tools.mjs` 26/26 PASS (192.168.10.0/24 → 254 hosts, chmod 640 → rw-r-----, 8192MB 60% → -Xmx4915m, epoch 1700000000 → KST 2023-11-15 07:13:20, cron `0 2 * * 1-5` → 월–금요일 02:00, 해시·↑↓·숫자키·copy·모바일 뒤로 포함).
- `shoot.mjs` 15장 모두 가로 넘침 없음. 스크린샷 확인 후 고친 것: 패널 설명이 목록 설명과 중복 → 용도 문장으로 교체, 데스크톱 결과 행이 너무 넓음 → 패널 최대 880px, 모바일에서 `-XX:` 가 하이픈에서 줄바꿈 → 명령어 블록은 줄바꿈 없이 가로 스크롤, 모바일 목록에서 열지 않은 bytes 가 선택돼 보임 → 처음 연 뒤에만 표시, 홈 바로가기 `+6` 이 혼자 줄바꿈 → 이름 5개로 축소.

**미확인**
- Blogger 실제 저장과 실데이터 렌더링(정적 페이지가 Blog1 단일 항목 분기로 그려지는지 포함). 목업은 그 구조를 가정함.
- 독립 시각 평가는 하지 않음(자동 실행). 라운드 4(unslop·접근성)에서 도구 페이지를 같이 평가.
- 클립보드: https 에서는 `navigator.clipboard`, 아니면 `execCommand` 대체. 실제 기기(iOS Safari)에서 복사는 미확인.
- cron 다음 실행은 브라우저 시간대 기준. 일요일 `0`/`7`, 일·요일 동시 지정 시 OR 규칙(vixie cron)을 따름.

**사용자가 Blogger 에서 직접 할 일**
1. 테마 → 백업 후 `binary-garden-v4.xml` 업로드(또는 HTML 편집에 붙여넣기).
2. 페이지 → 새 페이지: 제목 `tools`, HTML 보기에서 본문을 `<div id="toolbox-app"></div>` 한 줄로 두고 게시 → 주소가 `/p/tools.html` 인지 확인(다르면 테마에서 `/p/tools.html` 을 모두 찾아 바꿔야 함).
3. 페이지 설정에서 댓글 허용 끔(권장).


## 라운드 4 결과 (2026-11-14) — unslop 다듬기 + 접근성

백업: `backup/2026-11-14_round4_binary-garden-v4.xml`. unslop 감사 결과 슬롭 신호는 적음(이미 디자인된 화면) → 폴리시 모드: 방향·팔레트·레이아웃은 유지하고 걸린 것만 수정.

**바꾼 것**
- 대비(WCAG AA 4.5:1): 다크 `--faint` #6f685d(3.4) → #8c8478(5.1). 라이트 `--dim` #756d61 → #5f584d, `--faint` #9a9184(2.8) → #6e665a, 라이트 앰버 #a86400(4.26) → #9a5a00(5.0).
- 굵기 700 → 600(제목·로고·통계·강조·결과 값), 표 머리글의 대문자+자간 제거. (태그 `[TOMCAT]` 대문자는 방향 카드 문법이라 유지)
- 도구 입력창: `outline:none` 제거 → 키보드 포커스 링 표시. 복사 버튼에 `aria-label`(예: "-Xmx 복사"), 모바일 복사 버튼 터치 높이 40px.
- 이미 갖춰져 있어 손대지 않음: 건너뛰기 링크, `:focus-visible`, `aria-current`, `<output>`(결과 읽어줌), 라벨, 모션 줄이기.

**검증**: XML well-formed, 인라인 스크립트 `node --check` OK, 중복 id 없음(`main` 은 includable 구조), `test-grep` 9/9, `test-tools` 26/26, `shoot` 15장 가로 넘침 없음, 도구 화면(라이트·모바일) 스크린샷 확인.

**미확인**: Blogger 실제 렌더링, 스크린 리더(VoiceOver/NVDA) 실사용 읽기, iOS 클립보드. 대비 값은 계산 기준이며 본문 코드 하이라이트 색은 재측정하지 않음.

**사용자 할 일**: 라운드 3 과 동일(테마 업로드, `/p/tools.html` 페이지 생성). 추가 없음.


## 라운드 5 결과 (2026-11-14) — 검색 고도화

백업: `backup/2026-11-14_round5_binary-garden-v4.xml`. oil-ui 지침의 "존재하는 화면에 기능 추가" 경로: 방향·색·컴포넌트 문법은 그대로 두고, 새 요소도 로그 행 문법(앰버 글자 + 좌측 2px 막대, 채움·그림자 없음)으로만 만듦.

**바꾼 것**
- 검색 문법: 단어(AND), `"문구"` 그대로, `label:tomcat`(`tag:` 같음, 앞부분 일치 — `label:jboss` → JBoss EAP7), `-단어`, `-label:x` 빼기, `sort:new|upd|rel`. 라벨 조건만 있으면 최신순, 단어가 있으면 관련도순이 기본.
- 결과 머리줄 `grep-bar`: 왼쪽에 적용된 필터(`label:tomcat -apache`), 오른쪽에 `sort relevance · newest · updated`(선택 = 앰버 + 좌측 막대, `aria-pressed`). 버튼을 누르면 입력의 `sort:` 는 지움. 단어가 없으면 relevance 는 숨김.
- 라벨 완성: `label:` 뒤를 입력하면 명령행 아래 `# labels` 목록(글 수 많은 순, 최대 8개). ↑↓, Tab/Enter 로 완성, 공백 있는 라벨은 `label:"jboss eap7"` 로 넣음.
- 최근 검색어: 빈 명령행에 포커스하면 `# recent`(최근 5개, 이 브라우저 localStorage `bg-recent-v1`, 최대 8개 저장) + `clear` + 문법 한 줄 안내. 결과를 열 때(Enter·클릭), 기본 검색으로 제출할 때, `/search?q=` 로 들어왔을 때 저장. ↑↓ + Enter 로 다시 실행.
- 일치어 표시는 단어에만(부분 라벨 `label:w` 가 제목의 모든 W 를 칠하던 문제 수정). 입력창에 `aria-controls`·`aria-expanded`·`aria-activedescendant`.
- 자리표시 문구: 데스크톱 `에러 메시지, 설정 키, label:tomcat`, 모바일 `키워드 또는 label:ssl`.
- preview: `test-grep.mjs` 에 13개 검사 추가(총 22개), `shoot.mjs` 에 6장 추가(라벨 필터·라벨 완성·최근 검색어 라이트/모바일·라벨 모바일·글 화면 패널, 총 21장).

**검증**
- expat XML well-formed OK, 인라인 스크립트 2개 `node --check` OK, 중복 id 없음(`main` 은 includable 구조), b:skin 은 CSS 만, 이름형 HTML 엔티티 없음.
- `test-grep.mjs` 22/22 (label:tomcat → Tomcat 글 3개 최신순, `label:tomcat ssl` → 1개, `ssl -label:troubleshoot` → 2개, `ssl -pkix` → 2개, `"pkix path"` → 1개, relevance→updated 순서 바뀜, `sort:new`, `label:we` → websphere·weblogic, Tab 완성, `label:"jboss eap7"`, Enter 로 연 검색어가 recent 에 남고 ↓+Enter 로 재실행, clear). `test-tools.mjs` 26/26.
- `shoot.mjs` 21장 모두 가로 넘침 없음. 스크린샷 확인 후 고친 것: 부분 라벨 일치어 과다 표시, 모바일 문법 안내가 `"문구"` / `그대로` 사이에서 줄바꿈 → 항목 단위로 줄바꿈.

**미확인**
- Blogger 실제 렌더링. JS 가 꺼졌거나 색인 실패 시 Enter → Blogger `/search?q=label:tomcat` 로 가는데, Blogger 기본 검색이 `label:` 연산자를 어떻게 처리하는지는 실제 블로그에서 확인 필요(라이브 목록은 테마가 직접 거르므로 영향 없음).
- 독립 시각 평가·UX 서브 에이전트 검토는 하지 않음(자동 실행). 자체 스크린샷 검토만.
- 정렬 `updated` 는 피드의 `updated` 시각 기준이라, 댓글·라벨만 바꾼 경우에도 수정으로 잡힐 수 있음.

**사용자가 Blogger 에서 직접 할 일**: 라운드 3 과 동일(테마 백업 후 `binary-garden-v4.xml` 업로드, `/p/tools.html` 페이지). 추가 설정 없음. 사이트 피드가 "없음"이면 라이브 검색이 동작하지 않으므로 "전체" 또는 "짧게" 확인.


## 라운드 6 결과 (2026-11-15) — 성능·SEO·기존 글 호환

백업: `backup/2026-11-15_round6_binary-garden-v4.xml`. 화면 설계를 바꾸는 라운드가 아니라 oil-ui/unslop 은 부르지 않음. 방향·색·레이아웃은 그대로.

**바꾼 것**
- 폰트 로딩: Pretendard·JetBrains Mono·Dancing Script CSS 를 첫 화면을 막지 않게 로드(`media=print` → `onload` 로 `all`, `<noscript>` 대체). JetBrains Mono 굵기 700 → 600(실제 쓰는 굵기). Dancing Script 는 로고 글자(`text=BinaryGarden`)만 받음. 글 화면에 cdnjs preconnect.
- CLS: 코드블록 머리줄(`$ terminal` + copy)과 표 감싸기를 window load 뒤가 아니라 본문 끝 스크립트 실행 시점에 만듦(이전에는 이미지까지 다 받은 뒤 코드블록마다 높이가 늘어 화면이 밀림). 문법 색은 DOMContentLoaded(highlight.js defer 실행 뒤)에 칠함.
- SEO: 홈에 `WebSite` JSON-LD, 라벨이 있는 글에 `BreadcrumbList` JSON-LD(글의 `BlogPosting` 은 Blogger `postMetadataJSON` 이 이미 출력). `og:locale=ko_KR`. 검색 결과(`?q=`)·보관함 페이지는 `noindex, follow`(라벨 페이지는 색인 유지). 라벨/검색/보관함/404 의 상태 줄(`$ ls ~/logs/Tomcat/`)을 `h1` 로 바꿔 모든 화면에 h1 이 하나씩(모양은 그대로). 도구 페이지는 숨긴 껍데기 h1 을 빼고 `$ ls ./toolbox` 만 h1.
- 기존 글 호환(Blogger 글쓰기 편집기로 쓴 글): 하드코딩된 검정/진회색 글자색 → 테마 글자색, 흰 배경 강조 → 투명, Arial 등 글꼴 지정 → Pretendard, Courier/monospace 지정 → JetBrains Mono, `x-small` → 14px, `<pre style="color:black">` 도 터미널 색 유지, `border="1"` 표 → 가로 선만.
- preview: `build_preview.py` 가 폰트 링크를 테마에서 그대로 잘라 씀, `label.html`(상태 줄 h1)·`legacy.html`(편집기식 옛 글) 목업 추가. `shoot.mjs` 4장 추가(총 25장). `test-perf.mjs` 신규 26개 검사.

**검증**
- expat XML well-formed OK, 인라인 스크립트 2개 `node --check` OK, JSON-LD 템플릿 2개 JSON 파싱 OK, 중복 id 없음(`main` 은 includable 구조), b:skin 은 CSS 만(CSS 주석 안 `<div>` 표기는 기존), 이름형 엔티티 없음, expr 안 `&&` 없음.
- `test-grep` 22/22, `test-tools` 26/26, `test-perf` 26/26: CLS 0(홈·글·옛 글·도구, 1440/390), 코드블록이 DOMContentLoaded 전에 완성, 문법 색 적용, 폰트 CSS 가 `all` 로 전환·폰트 사용 가능, 페이지마다 h1 1개, 옛 글 다크/라이트 대비 15:1 이상·흰 배경 제거·글꼴 매핑·이미지 16:9 유지·모바일 넘침 없음.
- `shoot` 25장 가로 넘침 없음. 스크린샷 확인 후 고친 것: 옛 글의 `border="1"` 표가 격자선으로 보임 → 가로 선만.

**미확인**
- Blogger 실제 저장·렌더링. 특히 `data:blog.canonicalHomepageUrl`·`data:post.labels.first.url.canonical`·`.jsonEscaped` 가 저장 시 오류를 내면 해당 `<script type='application/ld+json'>` 블록만 지우면 됨. Google 리치 결과 테스트로 구조화 데이터 확인 필요.
- 실제 LCP 수치(Lighthouse/PageSpeed)는 측정하지 않음(외부 전송 금지·로컬 목업). 로컬 CLS 만 측정.
- 비차단 폰트 로딩이라 첫 화면에서 대체 글꼴 → 웹폰트로 바뀌는 순간이 보일 수 있음(행 높이는 고정이라 CLS 는 0).
- 실제 옛 글의 인라인 스타일은 표본 목업 기준. 위에서 다루지 않은 색(예: 파랑·빨강 글자)은 그대로 둠.
- 글에 이미지가 없으면 og:image 가 없음(기본 공유 이미지 없음).

**사용자가 Blogger 에서 직접 할 일**
1. 라운드 3 과 동일(테마 백업 후 `binary-garden-v4.xml` 업로드, `/p/tools.html` 페이지).
2. 설정 → 메타 태그 → "검색 설명 사용" 켜고 블로그 설명 입력(글마다 검색 설명도 넣으면 좋음). 테마는 meta description 을 따로 만들지 않음(Blogger 와 중복 방지).
3. 업로드 뒤 글 1개를 Google 리치 결과 테스트와 PageSpeed Insights 로 확인.


## 라운드 7 결과 (2026-11-15) — 최종 QA·적용 가이드·릴리스

백업: `backup/2026-11-15_round7_binary-garden-v4.xml`. oil-ui 지침의 "존재하는 화면 검토" 경로로 25장 스크린샷 중 대표 화면(홈 데스크톱/모바일/라이트, 글 데스크톱/모바일, 도구 데스크톱/모바일, 메뉴 서랍)을 직접 확인. 방향·색·레이아웃 변경 없음.

**바꾼 것**
- 모바일 푸터: `tools` 만 저작권 줄에 붙고 `about privacy rss` 가 다음 줄로 떨어지던 어색한 줄바꿈 수정. 저작권 칸이 첫 줄을 채워 링크 4개가 둘째 줄에 함께 놓임(`.site-footer .copy`, 모바일만). 데스크톱은 그대로.
- `APPLY-GUIDE.md` 신규: 백업 → 업로드 → 저장 오류 시 지울 블록 → 페이지 3개(`tools`·`about`·`privacy`, 주소 고정 방법) → Blogger 설정 → 올린 뒤 10분 확인표 → 되돌리기.
- 릴리스: `binary-garden-v4.xml` 이 v4.0 릴리스 파일(SHA-1 `4beb560b892ce27219635a215eb6a09edb867704`, 149,124 bytes). 상단 "적용 방법"이 가이드를 가리키게 함.

**검증**
- expat XML well-formed OK, 인라인 스크립트 2개 `node --check` OK, 중복 id 없음(검사에 걸린 `toolbox-app`·`tb-` 는 CSS/JS 주석·JS 문자열, `main` 은 includable 구조), 이름형 엔티티 없음, expr 안 `&&` 없음, b:skin 은 CDATA CSS 만, `BLOG_CONFIG` 의 SNS·댓글 값은 빈 값.
- `test-grep` 22/22, `test-tools` 26/26, `test-perf` 26/26 PASS. `shoot` 25장 모두 가로 넘침 없음.
- 검토했지만 고치지 않은 것: 홈 데스크톱 태그 칸이 3개 이상이면 `[…` 로 잘림(의도된 말줄임, 제목 열 폭 유지), 모바일 JAVA_OPTS 명령어는 가로 스크롤(라운드 3 결정).

**미확인**
- Blogger 실제 저장·렌더링(이전 라운드와 동일). 정적 페이지가 Blog1 단일 항목 분기로 그려지는지, JSON-LD 표현식 저장 여부.
- 독립 시각 평가 서브 에이전트는 띄우지 않음(자동 실행, 자체 스크린샷 검토만).
- 실제 기기 클립보드(iOS Safari), 스크린 리더 실사용, 실측 LCP.

**사용자가 Blogger 에서 직접 할 일** (자세한 순서는 `APPLY-GUIDE.md`)
1. 현재 테마 백업 → `binary-garden-v4.xml` 업로드.
2. 페이지 3개 만들기: `tools`(본문 `<div id="toolbox-app"></div>` 한 줄, 댓글 끔), `about`, `privacy`. 주소가 `/p/tools.html` 등인지 확인.
3. 사이트 피드 전체/짧게, 홈 게시물 수 15~20, 검색 설명 켜기.
4. 필요하면 `BLOG_CONFIG` 에 SNS·Disqus·giscus 값 입력(파일에는 빈 값으로 둠).
5. 가이드의 10분 확인표대로 점검, 리치 결과 테스트·PageSpeed Insights 실행.


## v4.0.1 대비 보정 (2026-11-15)

백업: `backup/2026-11-15_contrast_binary-garden-v4.xml` (v4.0 릴리스 파일 그대로). 라운드 4 이후 남은 대비 미달만 고침. 레이아웃·글꼴·다른 색은 그대로.

**바꾼 것** (기준: 글자 4.5:1, 입력창 테두리 3:1, 호버 바탕 `--raise` 포함)
- 다크 `--faint` #8c8478 → #91897e (호버 바탕에서 4.34 → 4.64)
- 다크 `--red` #e5534b → #e65d55 (호버 바탕 4.33 → 4.63)
- 라이트 `--red` #c2413a → #b73d37, `--green` #2f7d3a → #2c7637 (호버 바탕 4.23 → 4.65 / 4.63)
- 코드 주석 `.hljs-comment` #7a7266 → #847b6e (4.07 → 4.63)
- 새 토큰 `--control`(다크 #6f685d, 라이트 #8f8679): 명령행(`.cmd`)과 도구 입력창(`.tbx-in/.tbx-sel/.tbx-ta`) 테두리에만 사용. 1.5:1 → 3.2~3.5:1. 패널·구분선·버튼 테두리는 `--line2` 그대로(버튼은 글자·아이콘으로 구분됨).

**검증**: expat XML OK, `test-grep` 22/22, `test-tools` 26/26, `test-perf` 26/26 PASS, 미리보기에서 다크·라이트 계산값 확인. 릴리스 파일 SHA-1 `c059cab7a22de7dfec629fc1fd7ec2f44fa042bd`, 149,168 bytes.

**미확인**: Blogger 실제 렌더링. 다크 `--faint`(5.41)와 `--dim`(5.69)의 차이가 줄어 단계 구분이 약간 약해짐.

**사용자 할 일**: v4.0 을 이미 올렸다면 이 파일로 다시 업로드.
