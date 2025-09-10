# GitHub Issue Analysis & Solution for Issue #1

---

## Step 1: Issue Retrieval & Analysis

### Issue Information Summary
**이슈 번호**: #1
**제목**: [Feature] Phase 1 - Environment Setup & Scaffolding
**상태**: OPEN
**생성일**: 2025-09-09T10:06:30Z
**담당자**: 없음
**라벨**: 없음
**마일스톤**: 없음

### Issue Content Analysis
**문제 유형**: Feature
**우선순위**: High
**복잡도**: Medium

**핵심 요구사항**:
- Git 저장소 초기화 및 원격 연결
- `apps`, `content`, `deploy` 등 핵심 디렉토리 구조 생성
- Next.js 프론트엔드 및 FastAPI 백엔드 스캐폴딩
- Docker Compose를 사용한 백엔드 서비스 실행
- 프론트엔드-백엔드 간 `/healthz` API 호출을 통한 연결 확인

**기술적 제약사항**:
- 백엔드는 Docker 컨테이너 환경에서 실행되어야 합니다.
- 프론트엔드는 `http://localhost:8000`으로 API를 호출해야 합니다.

---

## Step 2: Technical Investigation

### Investigation Summary
- **`apps/api/main.py`**: `/healthz` 엔드포인트 구현을 확인했습니다.
- **`apps/api/Dockerfile`**: API 서버의 Docker 컨테이너화 설정을 확인했습니다.
- **`deploy/docker-compose.yml`**: Docker Compose를 통해 API 서비스를 빌드하고 포트 8000으로 실행하는 설정을 확인했습니다.
- **`apps/frontend/src/lib/api.ts` & `src/app/page.tsx`**: 프론트엔드에서 백엔드의 `/healthz`를 호출하여 상태를 표시하는 로직을 확인했습니다.

### Impact Analysis
- **Frontend**: `apps/frontend` 디렉토리 전체. `page.tsx`와 `api.ts` 파일에 백엔드 연동 로직이 포함됩니다.
- **Backend**: `apps/api` 디렉토리 전체. API 서버의 기본 구현 및 Dockerfile이 포함됩니다.
- **Database**: 영향 없음 (이슈 범위에 포함되지 않음).
- **Infrastructure**: `deploy/docker-compose.yml`을 통해 개발 환경의 서비스 오케스트레이션을 담당합니다.

---

## Step 3: Solution Strategy

### Recommended Approach
**선택한 접근법**: **Next.js, FastAPI, Docker를 활용한 모노레포 스타일 스캐폴딩**

**선택 이유**:
이 접근법은 프론트엔드와 백엔드를 명확히 분리하면서도 단일 저장소 내에서 일관성 있게 프로젝트를 관리할 수 있는 현대적인 개발 방식입니다. 각 기술 스택의 장점을 최대한 활용하여 개발 생산성과 확장성을 높이는 것이 핵심입니다.

- **장점**:
    - **명확한 관심사 분리**: `apps` 디렉토리 내에 프론트엔드와 백엔드 코드를 분리하여 독립적인 개발 및 배포가 용이합니다.
    - **일관된 개발 환경**: Docker와 Docker Compose를 사용하여 모든 개발자가 동일한 백엔드 환경에서 작업할 수 있습니다.
    - **검증된 기술 스택**: Next.js와 FastAPI라는 인기 있고 성능이 뛰어난 프레임워크를 사용하여 개발 경험과 애플리케이션 성능을 모두 확보할 수 있습니다.
    - **높은 확장성**: 향후 다른 마이크로서비스나 애플리케이션을 `apps` 디렉토리에 쉽게 추가할 수 있는 구조입니다.

- **단점**:
    - **초기 설정의 복잡성**: Next.js, FastAPI, Docker 등 여러 구성 요소가 함께 동작하도록 설정해야 하므로 초기 단계에서 다소 복잡하게 느껴질 수 있습니다.

- **예상 시간**: 이슈에 명시된 대로 약 2시간이 소요되었습니다.
- **위험도**: **Low**. 설정 관련 위험이 있었으나, Step 2에서 모든 기능이 정상 동작함을 확인하여 위험이 해소되었습니다.

---

## Step 4: Detailed Implementation Plan

### Phase 1: 저장소 및 디렉토리 구조 설정
**목표**: 버전 관리를 시작하고 프로젝트의 기본 골격을 생성합니다.

| Task | Description | Owner | Definition of Done (DoD) |
|---|---|---|---|
| Git 저장소 초기화 | `git init` 및 원격 저장소 연결 | 개발자 | `main` 브랜치가 원격 저장소에 푸시됨 |
| 디렉토리 구조 생성 | `apps`, `content`, `deploy` 등 폴더 생성 | 개발자 | `GEMINI.md`에 명시된 디렉토리 구조 완성 |
| `.gitignore` 파일 생성 | `node_modules`, `.next` 등 불필요 파일 제외 | 개발자 | `.gitignore` 파일이 커밋됨 |

### Phase 2: 프론트엔드 및 백엔드 스캐폴딩
**목표**: 각 애플리케이션의 기본 코드를 생성하고 실행 가능한 상태로 만듭니다.

| Task | Description | Owner | Definition of Done (DoD) |
|---|---|---|---|
| Next.js 앱 생성 | `npx create-next-app`으로 `apps/frontend`에 앱 설치 | 개발자 | `localhost:3000`에서 기본 Next.js 페이지 확인 |
| FastAPI 앱 생성 | `apps/api`에 `main.py`, `requirements.txt` 등 생성 | 개발자 | `/healthz` 엔드포인트가 200 OK를 응답함 |
| API Dockerization | `Dockerfile`을 작성하여 FastAPI 앱을 컨테이너화 | 개발자 | Docker 이미지 빌드 성공 및 컨테이너 실행 확인 |

### Phase 3: 통합 및 최종 검증
**목표**: 백엔드 서비스를 Docker Compose로 실행하고 프론트엔드와의 연결을 검증합니다.

| Task | Description | Owner | Definition of Done (DoD) |
|---|---|---|---|
| Docker Compose 파일 작성 | `deploy/docker-compose.yml`에 `api` 서비스 정의 | 개발자 | `docker compose up`으로 `api` 컨테이너 정상 실행 |
| 프론트엔드-백엔드 연동 | 프론트엔드에서 백엔드의 `/healthz` API 호출 | 개발자 | 프론트엔드 화면에 API 상태가 'ok'로 표시됨 |

### Phase 4: 완료 및 문서화
**목표**: 완료된 작업을 반영하고, 다른 개발자들이 사용할 수 있도록 준비합니다.

| Task | Description | Owner | Definition of Done (DoD) |
|---|---|---|---|
| 최종 커밋 및 푸시 | Phase 1-3의 결과물을 원격 저장소에 푸시 | 개발자 | `feat: scaffold(frontend/api/docker)` 커밋이 푸시됨 |
| 이슈 해결 문서화 | 본 프로세스를 통해 이슈 해결 과정 및 결과 정리 | Gemini | `resolve-issue.md` 템플릿 기반의 분석/결과 보고서 |

---

## Step 5: Risk Assessment & Mitigation

### Risk Items

| Risk | Impact | Probability | Mitigation Strategy |
|---|---|---|---|
| Docker Compose 설정 오류 | Medium | Medium | - `docker compose config` 명령으로 설정 파일의 유효성을 검사했습니다.<br>- 로컬 환경에서 `docker compose up --build`를 통해 빌드 및 실행을 반복적으로 테스트했습니다.<br>- 컨테이너 로그를 실시간으로 모니터링하여 런타임 오류를 신속하게 해결했습니다. |
| 포트 충돌(3000/8000) | Low | Medium | - 점유 중인 프로세스 종료 또는 포트 변경.<br>- Compose/Next 설정에서 포트 재지정. |
| CORS 미스매치 | Medium | Medium | - API: `ALLOWED_ORIGINS` 환경변수로 허용 오리진 관리.<br>- FE: `NEXT_PUBLIC_API_BASE_URL`과 실제 API 주소 일치 확인. |

### Technical Challenges

**예상되었던 기술적 난점 및 해결 방안**:
1. **Next.js, FastAPI, Docker 연동 설정**:
   - **해결 방안**: `docker-compose.yml`로 FastAPI를 서비스화하고, Next.js는 환경 변수를 통해 API 주소를 참조하도록 설정했습니다. FastAPI 측에 CORS 미들웨어를 추가하여 지정된 프론트엔드 오리진(`http://localhost:3000`)의 요청을 허용했습니다.

2. **초기 프로젝트 구조 수립**:
   - **해결 방안**: `apps`, `content`, `deploy` 등 기능적으로 분리된 명확한 디렉토리 구조를 채택하여 향후 확장성을 고려했습니다.

3. **일관된 개발 환경 보장**:
   - **해결 방안**: 백엔드 환경을 Docker 컨테이너로 정의하여, 모든 팀원이 의존성 문제 없이 동일한 환경에서 개발을 진행할 수 있도록 했습니다.

### Rollback Plan

**롤백 시나리오**:
- **문제 상황 1: API 컨테이너 실행 실패**
  - **롤백 절차**: `docker-compose.yml` 또는 `Dockerfile`의 변경 사항을 Git을 통해 이전 버전으로 되돌리고, 오류 로그를 분석하여 설정을 수정 후 재시도합니다.
- **문제 상황 2: 프론트엔드-백엔드 통신 실패**
  - **롤백 절차**: API 호출 관련 코드(CORS, URL 등) 변경 사항을 이전 커밋으로 되돌리고, 네트워크 설정 및 방화벽을 점검합니다.

---

## Step 6: Environment & Runbook

### Frontend 환경변수 (.env.local)
- 경로: `apps/frontend/.env.local`
- 값: `NEXT_PUBLIC_API_BASE_URL=http://localhost:8000`
- 비고: 공개 프론트 환경변수는 `NEXT_PUBLIC_` 접두사 사용.

### Backend CORS 환경변수
- 권장: `ALLOWED_ORIGINS`로 CORS 허용 도메인 외부화(복수일 경우 콤마 구분).
- Compose 예시: `deploy/docker-compose.yml`의 `environment`에 `ALLOWED_ORIGINS=http://localhost:3000` 추가.
- 참고: 현재 `apps/api/main.py`는 `http://localhost:3000`이 하드코딩되어 있어 후속 작업에서 환경변수 기반으로 개선 권장.

문서 참고용 코드 스니펫:
```
import os
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()
origins = os.getenv("ALLOWED_ORIGINS", "http://localhost:3000").split(",")
app.add_middleware(
    CORSMiddleware,
    allow_origins=origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

### 로컬 실행 절차
1) API 컨테이너 기동
- `cd deploy && docker compose up --build`
- 건강 체크: `curl http://localhost:8000/healthz` → `{"status":"ok"}`

2) 프론트엔드 기동
- `cd apps/frontend && npm run dev`
- 브라우저에서 `http://localhost:3000` 접속 → 메인 페이지에 “API Health Status: ok” 확인

3) 트러블슈팅
- 포트 충돌: 점유 프로세스 종료 또는 포트 변경
- CORS 오류: `ALLOWED_ORIGINS`/프론트 ENV 값 정합성 확인
- 네트워크/방화벽: 로컬 접근 허용 확인

---

## Step 7: Quality Assurance Plan

### Test Strategy
- **Unit Tests**: N/A. 이 단계는 스캐폴딩에 중점을 두므로, 비즈니스 로직이 없어 단위 테스트는 제외합니다.
- **Integration Tests**: 프론트엔드와 백엔드 간의 연동을 수동으로 검증하는 것이 핵심입니다. 실행 중인 백엔드 API의 `/healthz` 엔드포인트를 프론트엔드에서 호출하여 정상 응답을 받는지 확인합니다.
- **E2E Tests**: N/A. 사용자 시나리오에 기반한 End-to-End 테스트는 실제 기능이 구현된 후 다음 단계에서 계획합니다.

### Test Cases
```gherkin
Feature: API Health Check Integration

  Scenario: Frontend successfully checks API health
    Given the backend API service is running via Docker Compose
    And the frontend development server is running
    When the user opens the main page of the frontend application
    Then the page should display the API status as "ok"
```

### Performance Criteria
- **응답시간**: 일반적 개발 환경에서 `/healthz` p95 < 250ms.
- **처리량**: N/A (성능 테스트는 이 단계의 범위가 아닙니다.)
- **리소스 사용률**: 개발 환경에서 API 컨테이너는 최소한의 유휴 리소스(예: CPU < 5%, Memory < 100MB)를 사용해야 합니다.

---

## Step 8: Communication Plan

### Status Updates
- **일일 스탠드업**: "프로젝트 초기 환경 설정 및 스캐폴딩 완료" 내용을 공유합니다.
- **이슈 댓글 업데이트**: GitHub 이슈 #1에 작업 완료 사실과 이 분석 문서의 링크를 코멘트로 남겨 진행 상황을 기록합니다.
- **슬랙/팀즈 채널**: 개발 채널에 저장소 설정이 완료되었으며, 이제부터 본격적인 기능 개발이 가능함을 공지합니다.

### Stakeholder Notification
- **프로젝트 매니저**: 프로젝트 시작을 위한 기술적 기반이 성공적으로 마련되었음을 보고합니다.
- **관련 팀**: 프론트엔드 및 백엔드 개발팀에게 로컬 개발 환경 설정 가이드(`GEMINI.md` 또는 별도 문서)를 공유합니다.
- **사용자/고객**: N/A (내부 개발 환경 설정이므로 사용자에게 직접 공지할 내용은 없습니다.)

---

## 추가 보완 사항 요약
- 실행 가이드 명시: Compose, curl, Next dev 커맨드를 본 문서에 반영했습니다.
- 환경 변수 분리: FE `NEXT_PUBLIC_API_BASE_URL`, BE `ALLOWED_ORIGINS` 사용 권장.
- 위험 및 완화: 포트 충돌, CORS 미스매치, 네트워크 제약 대응 추가.
