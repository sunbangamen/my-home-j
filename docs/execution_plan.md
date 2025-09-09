# Execution Plan — Phase-Based (Next.js + FastAPI + Docker)

본 문서는 Phase 단계별로 환경 세팅 → 개발 → 검증 → 릴리즈 사이클을 정의합니다. Phase 1은 환경/구조/도커 연동에 집중하고, 본격 개발은 Phase 2부터 시작합니다. 이후 모든 개발은 "이슈 생성 → 구현 → PR → 리뷰/머지" 사이클로 반복합니다.

## Phase 1 — 환경 세팅 & 스캐폴딩
- 목표: 공통 개발 환경 정렬, 깃 연동, 프로젝트 구조 확정, 프론트/백엔드 스캐폴드, Docker Compose로 API 구동
- 산출물: 초기 커밋, 디렉터리 구조, Next.js 앱, FastAPI 앱, Dockerfile/compose, 기본 헬스체크

### 1. Git 저장소 초기화/연동
- 로컬 저장소 생성 및 원격 연결
  - `git init`
  - `git add . && git commit -m "chore: initial commit (docs)"`
  - GitHub/GitLab 원격 생성 후: `git remote add origin <REMOTE_URL>`
  - `git push -u origin main` (또는 `master`)
- 권장 설정
  - 브랜치 전략: `main` 보호, 기능 브랜치 `feat/*`, 버그 `fix/*`
  - PR 템플릿/이슈 템플릿은 Phase 2에서 추가

### 2. 개발 도구 정렬
- Node.js: 18 LTS 이상 (권장 20)
- 패키지 매니저: npm(기본) 또는 pnpm(선호 시)
- Python: 로컬 설치는 필수 아님(백엔드는 Docker로 실행)
- Docker: 24+ / Docker Compose V2

### 3. 리포지토리 구조 생성
- 디렉터리
  - `apps/frontend` — Next.js 앱
  - `apps/api` — FastAPI 앱
  - `apps/frontend/public/**` — 프론트 정적 자산(아이콘/OG 등)
  - `content/posts/*.md` — 게시글(Frontmatter 포함)
  - `content/gallery/*.json` — 앨범 메타
  - `content/images/**` — API가 서빙하는 원본 이미지
  - `deploy/docker-compose.yml` — 런/배포용 Compose 파일(Phase 1은 api만)
- .gitignore(예시)
  - `node_modules/`, `.next/`, `dist/`, `.DS_Store`, `*.log`
  - `__pycache__/`, `*.pyc`, `.venv/`
  - `.env`, `.env.*`

### 4. 프론트엔드(Next.js + TS) 설치
- 앱 생성(폴더: `apps/frontend`)
  - `npx create-next-app@latest apps/frontend --ts --eslint`
  - 옵션: Tailwind 포함 여부는 선택(디자인 편의상 권장)
- 개발 실행(이후)
  - `cd apps/frontend && npm run dev`
  - 환경변수: `NEXT_PUBLIC_API_BASE_URL=http://localhost:8000`

### 5. 백엔드(FastAPI) 스캐폴딩
- 최소 파일(폴더: `apps/api`)
  - `main.py` — FastAPI 앱, `/healthz` 및 샘플 라우트
  - `requirements.txt` — fastapi, uvicorn, pydantic, python-frontmatter 등(Phase 2 확장)
  - `Dockerfile` — slim python 기반, `uvicorn apps.api.main:app --host 0.0.0.0 --port 8000`
  - 콘텐츠 마운트
  - `content/` 폴더를 컨테이너에 읽기 전용 볼륨으로 마운트하여 파일 기반 API 구현 준비
  - CORS: `ALLOWED_ORIGINS` 환경변수(예: `http://localhost:3000`)로 허용 도메인 설정

### 6. Docker Compose 연동(초안)
- 파일: `deploy/docker-compose.yml`
- 서비스
  - `api` — 빌드 `../apps/api`, 포트 `8000:8000`, 볼륨 `../content:/app/content:ro`, env `ALLOWED_ORIGINS`
  - Reverse proxy/HTTPS는 Phase 3에서 별도 추가 예정
- 실행
  - `cd deploy && docker compose up --build`
  - 확인: `curl http://localhost:8000/healthz` → `{ "status": "ok" }`

### 7. 연결/검증
- 프론트에서 API 호출 베이스 URL 설정: `.env.local` → `NEXT_PUBLIC_API_BASE_URL=http://localhost:8000`
- 브라우저에서 프론트 홈 접속, 네트워크 탭으로 `/healthz` 호출 확인
- 초기 커밋/푸시
  - `git add . && git commit -m "feat: scaffold(frontend/api/docker)"`
  - `git push`

---

## Phase 2 — 본격 개발(이슈 기반)
- 목표: MVP 기능 구현(소식/갤러리/연락), 파일기반 API, 기본 SEO/접근성
- 산출물: 동작하는 목록/상세 페이지, 갤러리, API 엔드포인트, 기본 스타일/레이아웃

### 주요 작업(이슈 생성 가이드)
- 프론트엔드
  - 레이아웃/네비/푸터 기본 컴포넌트
  - 페이지: 홈, 가족 소개, 소식(목록/상세), 갤러리, 연락
  - 기능: 이미지 lazy, 라이트박스(경량 라이브러리: PhotoSwipe 등), 메타/OG, `generateMetadata`, `app/sitemap.ts`, `app/robots.ts`
- 백엔드
  - `/api/v1/posts` 목록/상세(Frontmatter+Markdown 파싱)
  - `/api/v1/gallery` 목록/상세(JSON 파싱)
  - CORS 설정(프론트 오리진 허용), `/healthz`
- 콘텐츠
  - `content/posts`에 샘플 글 2–3개, `content/gallery`에 샘플 앨범 1개
  - 관리 스크립트: `scripts/create-post`(frontmatter 템플릿 자동 생성)
- 품질
  - ESLint/Prettier, 기본 e2e 스모크(선택: Playwright), Lighthouse 체크

- ### Definition of Done(예시)
- 목록/상세/갤러리가 프론트에서 정상 표시되고, API 200 응답
- Lighthouse Performance 90+ (홈), A11y 주요 이슈 없음
- CI(선택): lint/test 통과

---

## Phase 3 — 하드닝 & 운영 준비
- 도커 이미지 최적화, 멀티스테이지 빌드
- 리버스 프록시/HTTPS(선택: Caddy/Traefik) 구성 초안
- 로그 레벨/구조화, 기본 모니터링(헬스체크), 백업 전략(콘텐츠 폴더)
- 간단 보안 헤더, CORS 제한 강화

---

## Phase 4 — 배포
- 옵션 A: 단일 VM/서버에서 `docker compose -f deploy/docker-compose.yml up -d`
- 옵션 B(혼합): 프론트는 Vercel, API만 컨테이너 배포
- 도메인 연결, TLS 적용(프록시 사용 시 자동화), 최종 스모크 테스트

---

## 작업 사이클(반복)
1) 당신(관리자)이 구현계획과 깃 이슈들을 생성(Phase 2 항목 기준, 작은 단위 권장)
2) 제가 이슈를 확인하고 구현 → 기능 브랜치 푸시 → PR 생성
3) 리뷰 후 머지 → 다음 이슈로 반복

### 이슈 템플릿 제안
- 배경/목표
- 범위(페이지/엔드포인트/데이터)
- 수용 기준(결과·성능·가시성)
- 구현 체크리스트
- 테스트/검증 방법
 - 리스크/롤백 플랜

### 브랜치/PR 규칙(간단)
- 브랜치: `feat/<slug>`, `fix/<slug>`
- PR 제목: `feat: ...` / `fix: ...`
- 머지 정책: 스쿼시 머지, CI(있다면) 통과 필수

---

## 빠른 참조(명령어)
- 프론트 개발: `cd apps/frontend && npm run dev`
- API 도커: `cd deploy && docker compose up --build`
- 헬스체크: `curl http://localhost:8000/healthz`
- 환경변수(프론트): `.env.local` → `NEXT_PUBLIC_API_BASE_URL=http://localhost:8000`
 - 성능 측정 조건: Lighthouse 기본 시뮬레이션(데스크톱), 냉캐시/초기 접속 기준

본 계획은 MVP 기준으로 작성되었습니다. 변경이 필요하면 해당 Phase 섹션을 업데이트하고, 새 이슈로 트래킹합니다.
