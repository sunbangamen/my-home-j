# MVP 스펙 — 우리집 홈페이지

본 문서는 PRD v0를 기반으로 즉시 구현 가능한 최소기능제품(MVP) 범위를 요약합니다.

## 1) 범위(Scope)
- 페이지
  - 홈: 대표 사진, 최근 소식 3개, 갤러리 하이라이트 6장
  - 가족 소개: 구성원 카드(이름/역할/소개/사진)
  - 소식: 목록 + 상세(Markdown 렌더)
  - 갤러리: 앨범 1개(썸네일 그리드 + 라이트박스)
  - 연락/방명록: 외부 폼 임베드(Tally/Formspree)
- 제외(나중): 로그인, 댓글, 검색, 다국어, 푸시알림, 복잡한 CMS

## 2) 성공 기준(간단)
- 성능: 데스크톱 초기 로드 < 1.5s, Lighthouse Performance 90+
- 콘텐츠: 1주 내 글 2개·사진 20장 업로드
- 참여: 첫달 가족 방문 5명+, 방명록 1건+

## 3) 기술 스택 / 아키텍처
- 프론트엔드: Next.js 14+(App Router), React, TypeScript, 스타일(Tailwind 또는 CSS Modules)
- 백엔드: FastAPI(Uvicorn), Python 3.11, 파일 시스템(초기) / SQLite(게스트북 선택)
- 런타임: Docker, Docker Compose(백엔드/스토리지), 프론트는 로컬에서 `npm run dev`
- 통신: 프론트 → `http://localhost:8000`(API), CORS는 `ALLOWED_ORIGINS` 환경변수 기반

## 4) 디렉터리 구조(초안)
- `apps/frontend` — Next.js 앱
- `apps/api` — FastAPI 앱
- `apps/frontend/public/**` — 프론트 정적 자산(아이콘/OG 등)
- `content/posts/*.md` — 게시글(Frontmatter: title, date, tags, cover, excerpt?)
- `content/gallery/*.json` — 앨범 메타
- `content/images/**` — 갤러리/게시글에서 참조하는 원본 이미지
- `deploy/docker-compose.yml` — Phase 1은 api만 포함(프록시/HTTPS는 Phase 3)

## 5) 데이터 모델(초안)
- Post: `id(slug)`, `title`, `date(ISO)`, `tags[]`, `cover`, `excerpt?`, `content(Markdown)`
- Album: `id`, `title`, `date`, `photos[{src, alt, width, height}]`
- Guestbook(선택): `id`, `name`, `message`, `createdAt`

## 6) API(초안)
- GET `/healthz` → `{ status: "ok" }`
- GET `/api/v1/posts` → `[{ id, title, date, tags, cover, excerpt }]`
- GET `/api/v1/posts/{id}` → `{ id, title, ..., content }`
- GET `/api/v1/gallery` → `[{ id, title, cover, count }]`
- GET `/api/v1/gallery/{id}` → `{ id, title, photos: [...] }`
- POST `/api/v1/guestbook`(선택) → 저장 후 `{ id, ... }` (간단 rate-limit/토큰)

## 7) 프론트 요구
- 반응형 레이아웃, 이미지 lazy-load, 라이트박스(경량 라이브러리: PhotoSwipe 등)
- SEO: App Router `generateMetadata`, `app/sitemap.ts`, `app/robots.ts`, 메타/OG
- 접근성: alt 텍스트, 포커스 스타일, 대비(AA)

## 8) 운영/보안(초안)
- Docker Compose 재시작 정책, 단일 노드 기준
- 민감 데이터 미수집, CORS 제한, 관리자 경로 비공개
- 로그 최소화, 추후 분석 도구(Plausible/Umami) 고려

- M1: 스캐폴딩
  - Next.js 기본 레이아웃/페이지(홈/소개/소식/갤러리/연락)
  - FastAPI `/healthz`, `/api/v1/posts`(파일 읽어 목록 반환)
  - Docker Compose로 API 구동, 프론트 로컬 연결
- M2: 콘텐츠 연결/갤러리
  - 마크다운 상세 렌더, 갤러리 그리드 + 라이트박스
  - SEO 기본, sitemap/robots
- M3: 배포/운영
  - 컨테이너 빌드/배포, 콘텐츠 폴더 백업 절차

- 프론트에서 `/api/v1/posts` 목록/상세 정상 표시(Markdown 렌더)
- 갤러리 썸네일/라이트박스 동작 확인
- 연락 페이지에서 외부 폼 노출 및 제출 동작
- 로컬 개발: `docker compose up` 후 프론트에서 API 호출 성공

## 11) 콘텐츠 가이드(초기)
- 글 2–3개(여행기/근황), 500–800자, 대표 이미지 1장
- 사진 20장 내외(<=2000px), WebP 우선, alt 텍스트 작성

---
본 문서는 MVP 구현의 단일 기준점입니다. 세부 구현 순서와 작업 배분은 `docs/execution_plan.md`에서 정의합니다.
성능 측정 조건: Lighthouse 기본 시뮬레이션(데스크톱), 냉캐시/초기 접속 기준.
