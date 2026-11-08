# 기면증의 모든 것: 최신 연구부터 환자 관리까지

기면증(Narcolepsy)의 병태생리, 진단 기준, 약물·비약물 치료, 일상생활 관리, 국내 제도·지원을 한 페이지로 정리한 정적 가이드입니다. 전문가와 환자·가족을 함께 독자로 합니다.

- 배포 주소(예정): <https://kypent2952-stack.github.io/narcolepsy-guide/>
- 가이드 허브: <https://kypent2952-stack.github.io/ent-diseases-guide/>

> 이 문서는 교육 목적으로 제작되었으며, 의학적 조언을 대체할 수 없습니다.

## 구성

| 파일 | 설명 |
|---|---|
| `index.html` | 본문 전체 (HTML·CSS·JS 단일 파일, 빌드 과정 없음) |
| `og-image.png` | 링크 공유 미리보기 이미지 (1200×630) |
| `sitemap.xml`, `robots.txt` | 검색엔진용 |
| `docs/screenshots/` | 디자인 정비 전후 스크린샷 |
| `NIGHT_REPORT.md` | 2026-11-08 디자인 정비 작업 보고서 |

## 디자인 규격 (가이드 사이트 공통)

- 글꼴: [Pretendard Variable](https://github.com/orioncactus/pretendard) v1.3.9 (jsDelivr dynamic subset)
- 강조색: `#1F5FA8` — 모든 색은 `index.html` 상단 `:root` 토큰으로 관리
- 다크 모드: 시스템 설정(`prefers-color-scheme`)을 따르며, 상단 버튼으로 수동 전환 가능(브라우저에 저장)
- 본문 폭 약 70자(`70ch`), 줄간격 1.8, 데스크톱 고정 목차 / 모바일 접이식 목차, 읽기 진행 바, 맨 위로 버튼
- 인쇄 시 A4 흑백 친화 레이아웃(목차·버튼 숨김, 외부 링크 주소 표기)

## 로컬에서 보기

```bash
python3 -m http.server 8000
# http://localhost:8000 접속
```

## 내용 수정 시

- 본문은 `<main id="main">` 안의 `<section id="...">` 단위로 나뉘어 있습니다. 섹션을 추가하면 상단 `.toc` 목차에도 링크를 추가하세요.
- 날짜가 바뀌면 `index.html`의 JSON-LD `dateModified`와 `sitemap.xml`의 `lastmod`도 함께 갱신하세요.
- 시점에 민감한 내용(약물 승인·임상 단계, 건강보험 급여, 장애 등록 제도)은 `NIGHT_REPORT.md`의 "최신화 검토 필요" 목록을 참고하세요.
