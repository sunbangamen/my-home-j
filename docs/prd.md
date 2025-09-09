# PRD v0 — 우리집 홈페이지 (MVP)

## 1) 개요
- 목적: 가족 소식과 사진을 빠르고 예쁘게 공유하는 소규모 웹사이트를 최소 기능으로 출시하고, 점진적으로 고도화한다.
- 전략: 프론트엔드는 Next.js(React+TypeScript)로 로컬 개발, 백엔드(FastAPI)·DB 등은 Docker로 구동. 운영 중 축적된 요구를 바탕으로 인증/비공개, 댓글, 검색 등 고도화.
- 타깃: 친척·지인(공개 열람), 가까운 가족(향후 비공개 섹션)

## 2) 목표/비목표
- 목표
  - 홈·가족 소개·소식(게시글)·사진 갤러리·연락/방명록의 기본 흐름 제공
  - 코딩 초보도 콘텐츠를 쉽게 추가/수정 가능(초기에는 Markdown/JSON 기반)
  - 로컬 개발은 프론트 단독 실행, 나머지는 Docker로 단순화
  - 배포는 Docker Compose(또는 단일 서버) 기준으로 단순하게 시작
- 비목표(차후)
  - 로그인/권한관리(NextAuth/Clerk 등), 댓글, 검색, 푸시알림, 다국어, 복잡한 CMS

## 3) 사용자 시나리오(핵심)
- 방문자: 메인에서 최근 소식과 사진을 보고, 갤러리/게시글 상세 열람, 연락 폼 제출
- 관리자(가족): Git/폴더에 Markdown(또는 간단 JSON) 추가 → API가 목록/상세 제공 → 프론트가 렌더링

## 4) 정보구조/페이지
- 홈: 히어로(가족 사진/한줄 소개), 최근 소식 3개, 갤러리 하이라이트 6장
- 가족 소개: 구성원 카드(이름·역할·짧은 소개·사진)
- 소식(블로그)
  - 목록: 카드형, 태그(여행/행사/일상) 필터(선택)
  - 상세: 제목, 날짜, 본문(Markdown 렌더), 대표 이미지/OG
- 갤러리: 앨범 1개부터 시작(여행/행사), 썸네일 그리드 + 라이트박스
- 연락/방명록: 외부 폼 임베드(Tally/Formspree) 또는 간단 API POST(스팸 방지 최소한)
- 푸터: 저작권/이메일/SNS(선택)

## 5) 기능 요구사항
- 콘텐츠
  - 게시글: Markdown 파일로 저장(필수 메타: `title`, `date`, `tags`, `cover`), API에서 목록/상세 제공
  - 갤러리: 이미지 메타(JSON) + 원본 파일, API에서 앨범/사진 목록 제공
- 프론트엔드
  - Next.js + TypeScript, 반응형 레이아웃, 이미지 지연로딩, 라이트박스(경량 라이브러리: PhotoSwipe 등)
  - SEO: App Router `generateMetadata`, `app/sitemap.ts`, `app/robots.ts`, 기본 메타/OG
  - 접근성: 대체텍스트, 키보드 포커스, 색 대비(AA)
- 백엔드(API)
  - FastAPI로 정적 콘텐츠 인덱싱(파일 시스템 기반) → `/api/v1/posts`, `/api/v1/posts/{slug}`, `/api/v1/gallery`
  - 헬스체크 `/healthz`
  - CORS: `ALLOWED_ORIGINS` 환경변수 기반(프론트 로컬 도메인 허용)
- 폼/방명록
  - 1차: Tally/Formspree 임베드
  - 2차(선택): `/api/v1/guestbook`로 제출 저장(SQLite) + Rate limit
- 이미지 처리
  - 업로드/보관은 우선 파일 시스템(볼륨) 사용, WebP 변환은 추후

## 6) 비기능 요구사항
- 성능: 초기 로드 < 1.5s(데스크톱), Lighthouse 90+
- 안정성: 단일 노드 Docker Compose에서 무중단에 준하는 재시작 정책
- 보안/프라이버시: 관리자용 경로 비공개, 민감 데이터 미수집, CORS 제한
- 관측: 간단 로그, 추후 프라이버시 친화형 분석(Plausible/Umami) 고려

## 7) 기술 스택/아키텍처
- 프론트엔드: Next.js 14+ (App Router), React, TypeScript, 스타일(Tailwind 또는 CSS Modules)
- 백엔드: FastAPI, Uvicorn, Python 3.11, 데이터 저장은 초기 파일 시스템/SQLite
- 배포/런타임: Docker, Docker Compose
- 개발 흐름
  - 로컬: 프론트(`npm run dev`)는 호스트에서 실행, API/DB/스토리지는 `docker compose up`
  - 통신: 프론트 → `http://localhost:8000`(API)로 호출, CORS 허용

### 7.1 디렉터리(초안)
- `apps/frontend` — Next.js 앱
- `apps/api` — FastAPI 앱(파일 인덱싱, REST)
- `apps/frontend/public/**` — 프론트 정적 자산(아이콘/OG 등)
- `content/posts/*.md`, `content/gallery/*.json`, `content/images/**` — API가 읽는 콘텐츠/이미지
- `deploy/docker-compose.yml` — Phase 1: api만 포함(Reverse proxy는 Phase 3에서 추가 가능)

## 8) 데이터 모델(초안)
- Post
  - id(slug), title, date(ISO), tags[string[]], cover(string URL), excerpt(선택), content(Markdown)
- Album
  - id(slug), title, date, photos[{src, alt, width, height}]
- Guestbook(선택)
  - id, name, message, createdAt

## 9) API 설계(초안)
- GET `/healthz` → `{status:"ok"}`
- GET `/api/v1/posts` → `[{id, title, date, tags, cover, excerpt}]`
- GET `/api/v1/posts/{id}` → `{...post, content}`
- GET `/api/v1/gallery` → `[{id, title, cover, count}]`
- GET `/api/v1/gallery/{id}` → `{id, title, photos:[...]}`
- POST `/api/v1/guestbook`(선택) → `{id, ...}` (스팸 최소 방지: 간단 토큰/헤더)

## 10) 콘텐츠 가이드(MVP)
- 게시글: 2–3개(여행기1, 근황1), 500–800자, 대표 이미지 1장
- 사진: 20장 내외, 2000px 이하, WebP 우선, alt 텍스트
- 문구 톤: 따뜻하고 짧게, 가족 애칭 일관

## 11) 성공 지표
- 성능: 홈 페이지 Lighthouse 90+ (Performance)
  - 측정 조건: Lighthouse 기본 시뮬레이션(데스크톱), 냉캐시/초기 접속 기준
- 초기 콘텐츠: 1주 내 글 2개·사진 20장 업로드
- 참여: 첫달 방문 가족 5명+, 방명록 1건+

## 12) 마일스톤/범위
- M1: 프로젝트 스캐폴드 & 기본 페이지(홈/소개/소식/갤러리/연락)
  - Next.js 앱 기본 라우트/레이아웃
  - FastAPI `/healthz`, `/api/v1/posts`(목록, 로컬 파일 읽기)
  - Docker Compose로 API 구동, 프론트 로컬 연결
- M2: 콘텐츠 연결 & 갤러리
  - Markdown 파싱(목록/상세), 갤러리 JSON/이미지 그리드 + 라이트박스
  - SEO 메타/OG, sitemap/robots
- M3: 배포/운영
  - Docker 이미지 빌드(프론트 정적 에셋, API) & Compose 배포
  - 간단 로그/모니터링, 기본 백업 전략(콘텐츠 폴더)

- 프론트에서 `/api/v1/posts` 목록이 보이고, 상세 페이지에서 Markdown이 렌더링된다.
- 갤러리 페이지에서 썸네일 그리드가 보이고, 클릭 시 라이트박스가 동작한다.
- 연락/방명록 페이지에서 외부 폼 임베드가 노출된다.
- 로컬 개발: `docker compose up`으로 API 정상 동작, 프론트 로컬에서 호출 성공.

## 14) 리스크/대응
- 이미지 용량 증가 → WebP 변환/지연로딩/썸네일 파이프라인 도입
- 초보 난이도 → 파일 기반 콘텐츠로 시작, 관리 스크립트/문서화 강화
- 인증 필요성 증가 → 차후 NextAuth+Supabase 등 도입 계획 수립

## 15) 운영 정책(초안)
- 개인정보 최소 수집, 쿠키/트래킹 최소화
- 비공개 자료는 초기에는 비공개 저장소/링크 비공개로 운영, 본격 권한관리는 추후

---

부가 자료(향후 추가)
- 와이어프레임: 홈/목록/상세/갤러리/연락
- 콘텐츠 템플릿: 게시글 마크다운 frontmatter 예시, 갤러리 JSON 스키마
- 개발 가이드: 로컬 실행/배포/백업 절차
- 관리 스크립트: `scripts/create-post`(frontmatter 템플릿 자동 생성) 등
