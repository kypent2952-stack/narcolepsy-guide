# NIGHT REPORT — 2026-11-08 · 기면증 가이드 디자인 공통 정비

- 브랜치: `claude/night-2026-11-08` (main 푸시 없음, PR 없음)
- 범위: 공통 디자인, 가독성, 모바일, 접근성, 인쇄, SEO 정비. **의학 내용은 바꾸지 않았습니다.**
  `<main>` 안의 보이는 글자를 원본과 자동 비교했고 완전히 같습니다(7,594자, 공백 정규화 기준).

---

## 1. 변경 전후 요약

| 항목 | 변경 전 | 변경 후 |
|---|---|---|
| 글꼴 | 시스템 글꼴(`-apple-system`, Segoe UI 등) | **Pretendard Variable v1.3.9**(jsDelivr dynamic subset), 폴백 `Pretendard, -apple-system, …` |
| 색상 | 하드코딩된 색 35종, 보라 그라데이션 배경 | `:root` 토큰 39개로 정리, 강조색 **#1F5FA8** |
| 다크 모드 | 클래스는 있었지만 켜는 UI가 없어 실제로는 동작하지 않음 | `prefers-color-scheme` 자동 적용 + 상단 수동 토글(`data-theme`, localStorage에 저장, 깜빡임 방지) |
| 본문 폭 | 최대 1200px(한 줄 약 70~80자 이상) | 본문 열 `max-width: 70ch`, 줄간격 1.8(모바일 1.75), `word-break: keep-all` |
| 목차 | 가로 메뉴 7개(모바일에서 세로로 쌓여 화면을 많이 차지) | 1080px 이상: 왼쪽 **고정 목차**와 현재 섹션 강조 / 그보다 좁으면: 상단 고정 **접이식 목차**(현재 섹션 이름 표시, 링크 누르면 자동으로 접힘). 빠져 있던 '맺음말' 추가 |
| 읽기 진행 바 | 있음(scroll마다 width 재계산) | 4px 강조색, `transform: scaleX`와 rAF로 부드럽게, `aria-hidden` |
| 맨 위로 버튼 | 없음 | 600px 이상 스크롤하면 나타남, 48×48 터치 영역, `aria-label` |
| 읽기 시간 | 영어 기준(단어 200개/분) | 한국어 기준(약 500자/분), 히어로에 전체 읽기 시간 표시 |
| 표 | `overflow-x` 래퍼만 있음 | 래퍼에 `role="region"`·고유 `aria-label`·`tabindex="0"`(키보드로 스크롤 가능), 모바일에서 "좌우로 밀기" 안내 |
| 모바일 375px | 가로 스크롤 없음 | 가로 스크롤 없음(검사 스크립트로 확인), 카드 그리드 `minmax(min(100%,260px),1fr)` |
| 접근성 | 건너뛰기 링크 없음, 랜드마크 부족 | 본문 바로가기 링크, `nav`/`main`/`footer` 랜드마크, `:focus-visible`, 헤딩 순서 보정(h4→h3 2곳, 글자는 그대로), **axe-core 위반 0건**(WCAG 2.1 A/AA + best-practice, 375/1280 × 라이트/다크) |
| 동작 | 카드 hover 시 JS로 들썩임, 헤더 등장 애니메이션 | 장식용 움직임은 없앰, `prefers-reduced-motion` 지원 |
| 인쇄 | 일부만 처리, JS로 글자 크기 강제 변경 | A4 전용 `@media print`: 흰 바탕/검은 글자, 목차·버튼·진행 바 숨김, 카드·표 행이 페이지 중간에서 잘리지 않게 함, 표 머리글 반복, 외부 링크 주소 표기 |
| SEO | title만 있음 | description, keywords, canonical, Open Graph, Twitter Card, theme-color, favicon(SVG), **MedicalWebPage JSON-LD**(about: MedicalCondition 기면증 / ICD-10 G47.4, audience: Patient·Clinician, BreadcrumbList), `og-image.png`(1200×630) |
| 신규 파일 | — | `sitemap.xml`, `robots.txt`, `README.md`, `og-image.png`, `docs/screenshots/*`, 이 보고서 |
| 허브 링크 | 없음 | 상단 바 "← 이비인후과 질환 가이드 허브", 하단 "다른 이비인후과 질환 가이드 보기" → `https://kypent2952-stack.github.io/ent-diseases-guide/` |
| 정리한 코드 | 쓰이지 않던 `.progress-bar`, `.loading`, 앵커 클릭 가로채기(주소창 hash가 바뀌지 않던 문제), `localStorage` 직접 접근(사생활 보호 모드에서 오류 가능) | 삭제하거나 `try/catch`로 감쌈. 앵커는 브라우저 기본 동작과 CSS `scroll-behavior`를 사용 |

**마크업만 바꾼 부분(글자는 그대로):** 인라인 `style` 4곳을 클래스(`.lead`, `.quote`, `.closing`)로 교체, 마지막 섹션에 `id="conclusion"` 추가, 참고문헌의 카페 주소를 클릭 가능한 링크로 변경, 상자 제목 2곳 h4→h3.

### 스크린샷 (`docs/screenshots/`)

| | 375px | 1280px |
|---|---|---|
| 변경 전 · 라이트 | `before-375-light-top.png` | `before-1280-light-top.png` |
| 변경 전 · 다크(시스템 다크여도 변화 없음) | `before-375-dark-top.png` | `before-1280-dark-top.png` |
| 변경 후 · 라이트 | `after-375-light-top.png`, `after-375-light-korea.png` | `after-1280-light-top.png` |
| 변경 후 · 다크 | `after-375-dark-top.png` | `after-1280-dark-top.png`, `after-1280-dark-korea.png` |
| 변경 후 · 기타 | `after-375-toc-open.png`(모바일 목차 펼침) | `after-print-page1.png`(인쇄 PDF 1쪽) |

---

## 2. 최신화 검토 필요 목록 (본문은 수정하지 않음)

> 줄 번호는 변경 후 `index.html` 기준입니다. 아래 "참고"는 작업자가 알고 있는 범위의 메모일 뿐 확인된 사실이 아니므로, **원장님께서 최신 자료로 직접 확인하신 뒤** 반영해 주세요.

### 높음 — 사실관계 오류 가능성
1. **L1072 · 약물명 표기** — "TAK-994 (오베포렉스톤)". 참고문헌 [2](Dauvilliers 2025 NEJM, *Oveporexton…*)를 보면 오베포렉스톤(oveporexton)은 **TAK-861**의 일반명으로 보입니다. TAK-994에 붙은 이름이 맞는지 확인이 필요합니다.
2. **L1037 · 저나트륨 옥시베이트 "1일 1회 서방형"** — 참고: 저나트륨 옥시베이트(Xywav)는 보통 밤에 두 번 나눠 먹고, 1일 1회 서방형은 옥시베이트 나트륨 서방형(Lumryz) 쪽으로 알고 있습니다. 두 약이 섞여 적혔을 수 있습니다.
3. **L1094 · "2024년 발표"** — TAK-861 3상(2건) 결과 발표 연도를 확인해 주세요(참고: 작업자는 2025년 발표로 기억합니다).
4. **L817, L1435 · "최종 업데이트: 2025년 1월"** — 참고문헌 [2]가 NEJM 2025;392(19)(2025년 5월호)라 날짜가 맞지 않습니다. 하단의 "© 2025"도 함께 확인해 주세요.

### 중간 — 시간이 지나 바뀌었을 가능성이 큰 내용
5. **L1079, L1393 · TAK-861 "Phase 3 진행 중", "FDA 신속(심사) 지정"** — 2026년 11월 현재 허가 신청이나 승인 여부 확인이 필요합니다. 받은 지정이 Breakthrough Therapy인지 Priority Review인지도 구분해 주세요.
6. **L1085 · TAK-360 "Phase 1"** — 2상 진입 여부와 대상 적응증(제2형 기면증, 특발성 과다수면증)을 확인해 주세요.
7. **L1408 · 맺음말 "임상시험의 마지막 단계를 통과하며 상용화를 눈앞에"** — 오렉신 수용체 작용제의 허가 현황에 맞게 표현을 갱신할지 검토가 필요합니다.
8. **L1029 · 솔리암페톨 "최근 승인된"** — 참고: 미국 FDA 승인은 2019년이라 "최근"이라는 표현을 다시 볼 필요가 있습니다.
9. **L1028 · 모다피닐/아모다피닐 "현재 1차 치료제"** — 최신 진료지침(AASM 2021 등)은 여러 약제를 함께 권고하므로 표현을 검토해 주세요.
10. **L930 · "ICSD-3"** — 현재 판은 ICSD-3-TR(2023)입니다. 진단 기준 표기를 갱신할지 검토해 주세요.
11. **L1056 · Science Media Centre, 2025 인용문** — 원문 출처와 번역이 맞는지 확인해 주세요.

### 국내 제도·급여 (변동이 잦음)
12. **L1247 · PSG·MSLT 건강보험 급여** — 현재 급여 범위와 조건(특히 MSLT)을 확인해 주세요.
13. **L1254–1257 · 국내 치료제 급여 현황** — 모다피닐, 메틸페니데이트, 옥시베이트 나트륨, 솔리암페톨, 피톨리산트 각각의 국내 허가·급여 상태를 확인해 주세요. 특히 옥시베이트의 국내 허가와 공급 형태를 확인할 필요가 있습니다.
14. **L1274, L1299 · 장애 등록** — 2021년 시행령 개정 내용과 장애 유형 분류, 그리고 "법 개정 후 3년간 33명 중 4명(약 12%)" 통계를 최신 수치로 바꿀지 검토해 주세요.
15. **L1263–1264 · 유병률·HLA 수치** — 출처 연도를 표기하거나 최신 연구로 바꿀지 검토해 주세요.
16. **L1310, L1424 · 한국기면병환우협회(2007년 설립, cafe.daum.net/narcolepsy)** — 단체 정보와 링크가 지금도 유효한지 확인해 주세요.

---

## 3. 검증 결과

| 항목 | 결과 |
|---|---|
| 로컬 서버 + Playwright(Chromium) 375px / 1280px × 라이트 / 다크 | 4가지 조합 모두 렌더링 확인, 전후 스크린샷 저장 |
| 콘솔 에러 | **0건**(4가지 조합 모두) |
| 375px 가로 스크롤 | 없음(`scrollWidth == clientWidth == 375`, 표 래퍼 밖으로 넘치는 요소 0개) |
| axe-core 4.10.2(WCAG 2.1 A/AA + best-practice) | **위반 0건**(4가지 조합 모두, 색 대비 포함) |
| Pretendard 적용 | `font-family` 계산값과 `document.fonts`의 Pretendard 로드 상태 확인 |
| 모바일 목차 | 처음엔 접힘 → 펼침 → 링크 클릭 시 접히고 해당 섹션으로 이동(고정 목차 아래에 정확히 위치), 주소창 hash 갱신, 현재 섹션 표시 |
| 맨 위로 / 진행 바 / 테마 토글 | 동작 확인(scrollY 0 복귀, scaleX 갱신, `data-theme`과 localStorage 저장) |
| 인쇄 | `emulateMedia('print')`로 A4 PDF를 만들어 1쪽 확인 |
| 본문 글자 무변경 | `<main>` 텍스트 원본과 동일 |

### 참고 / 한계
- **jsDelivr가 이 작업 환경의 네트워크에서 막혀 있어**, 검증할 때는 npm 레지스트리에서 받은 **같은 버전(pretendard@1.3.9)** 의 CSS와 woff2를 Playwright로 CDN 요청 자리에 넣어 렌더링했습니다. 실제 페이지는 지정된 CDN 링크를 그대로 씁니다.
- **허브 주소(`ent-diseases-guide`)와 이 사이트의 GitHub Pages 주소는 접속해 보지 못했습니다**(github.io 접속 차단). canonical, OG, sitemap은 `https://kypent2952-stack.github.io/narcolepsy-guide/`로 가정했습니다. Pages 배포 경로가 다르면 `index.html` head, `sitemap.xml`, `robots.txt`의 주소를 함께 바꿔 주세요.
- JSON-LD에는 `dateModified: 2026-11-08`(페이지 수정일)만 넣었습니다. 의학적 검토일(`lastReviewed`)은 실제 검토 후에 넣는 것이 맞아 비워 두었습니다.

---

## 4. 체크리스트

- [x] Pretendard Variable v1.3.9 CDN 링크와 지정 font-family
- [x] 색상 `:root` 토큰화, 강조색 #1F5FA8
- [x] 다크 모드(`prefers-color-scheme` + 수동 토글)
- [x] 본문 폭 70ch, 줄간격 1.8
- [x] 목차: 데스크톱 고정, 모바일 접이식
- [x] 맨 위로 버튼
- [x] 읽기 진행 바
- [x] 375px 가로 스크롤 없음, 표 래퍼(region/라벨/키보드 스크롤)
- [x] 접근성 AA(axe 0건, 건너뛰기 링크, 포커스 표시, 랜드마크, 동작 줄이기)
- [x] 인쇄용 CSS(A4)
- [x] meta description, Open Graph, Twitter Card, canonical
- [x] MedicalWebPage JSON-LD
- [x] sitemap.xml, robots.txt, README.md
- [x] 상단·하단 가이드 허브 링크(주소는 확인 못 함)
- [x] 최신화 검토 목록(본문 수정 없음)
- [x] 375/1280 × 라이트/다크 전후 스크린샷, 콘솔 에러 0
- [x] `claude/night-2026-11-08` 커밋·푸시(main 푸시 없음, PR 없음)
- [ ] (원장님) 최신화 목록 검토 후 본문 반영
- [ ] (원장님) GitHub Pages 배포 주소와 허브 주소 확인
