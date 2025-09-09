네, 이슈 #1에 대한 분석 및 해결 계획을 `commands/resolve-issue.md` 템플릿에 맞춰 작성했습니다.

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
- Git 레포지토리 초기화 및 원격 연결
- `apps`, `content`, `deploy` 등 핵심 디렉토리 구조 생성
- Next.js 프론트엔드 앱 스캐폴딩
- FastAPI 백엔드 앱 스캐폴딩
- Docker Compose를 사용한 백엔드 API 서비스 실행
- 프론트엔드와 백엔드 간의 기본 `healthz` 호출 확인

**기술적 제약사항**:
- 백엔드는 반드시 Docker 컨테이너 환경에서 실행되어야 합니다.
- 프론트엔드는 `http://localhost:8000` 주소로 API를 호출해야 합니다.
- 로컬 머신에 Git, Node.js (18+), Docker (24+)가 설치되어 있어야 합니다.

### Environment Variables & CORS
- 프론트엔드 환경 변수: `NEXT_PUBLIC_API_BASE_URL=http://localhost:8000`
  - 개발 환경 예시(`apps/frontend/.env.local`):
    - `NEXT_PUBLIC_API_BASE_URL=http://localhost:8000`
- FastAPI CORS 설정: 개발 단계에서 `http://localhost:3000` 오리진을 허용합니다.
  - 허용 항목: origins, methods(`GET,POST,OPTIONS`), headers(`*`), credentials(필요 시)

---

## Step 2: Technical Investigation

### Code Analysis Required
- 이번 이슈는 새로운 프로젝트 환경을 구성하는 것이므로, 분석할 기존 코드는 없습니다. 프로젝트 구조 정의는 [docs/execution_plan.md](../execution_plan.md)와 [GEMINI.md](../../GEMINI.md)를 따릅니다.

**영향 범위 분석**:
- **Frontend**: 신규 Next.js 애플리케이션 생성
- **Backend**: 신규 FastAPI 애플리케이션 생성
- **Database**: 이번 단계에서는 해당 없음
- **Infrastructure**: 신규 Docker Compose 설정 구성

### Dependency Check
- **Depends on**: 없음
- **Blocks**: 모든 후속 개발 단계
- **Related to**: 없음

---

## Step 3: Solution Strategy

### Recommended Approach
**선택한 접근법**: 이슈 내용에 명시된 계획
**선택 이유**: Next.js, FastAPI, Docker를 사용하는 모노레포(monorepo) 구성의 표준적인 접근법이며, 프로젝트의 초기 요구사항을 충실히 만족시킵니다. 다른 대안을 고려할 필요 없이 명확하고 효율적입니다.

---

## Step 4: Detailed Implementation Plan

### Phase 1: 준비 및 설계 (Day 1)
**목표**: 버전 관리 시작 및 프로젝트 골격 생성

| Task | Description | Owner | DoD | Risk |
|---|---|---|---|---|
| Git 레포지토리 초기화 | `git init` 및 원격 레포지토리 연결 | [담당자] | `main` 브랜치가 원격에 푸시됨 | Low |
| 디렉토리 구조 생성 | `apps/frontend`, `apps/api`, `content`, `deploy` 등 생성 | `execution_plan.md`에 명시된 구조 완성 | Low |
| `.gitignore` 파일 생성 | `node_modules`, `.next`, `.env` 등 불필요 파일 제외 | `.gitignore` 파일 커밋 완료 | Low |

### Phase 2: 핵심 구현 (Day 1)
**목표**: 각 애플리케이션의 기본 코드 생성 및 실행 가능한 상태로 만들기

| Task | Description | Owner | DoD | Risk |
|---|---|---|---|---|
| Next.js 앱 생성 | `npx create-next-app`으로 `apps/frontend`에 앱 설치 | `localhost:3000`에서 기본 페이지 확인 | Low |
| FastAPI 앱 생성 | `apps/api`에 `main.py`, `requirements.txt`, `Dockerfile` 생성 | Docker Compose 실행 시 `/healthz`가 200 OK 응답 | Medium |
| API Dockerization | `Dockerfile`을 사용하여 FastAPI 앱 빌드 환경 구성 | API 이미지가 성공적으로 빌드되고 Compose로 실행됨 | Medium |

### Phase 3: 테스트 및 통합 (Day 1)
**목표**: 백엔드 서비스를 Docker Compose로 실행하고 프론트엔드와 연결 확인

| Task | Description | Owner | DoD | Risk |
|---|---|---|---|---|
| Docker Compose 파일 생성 | `deploy/docker-compose.yml`에 `api` 서비스 정의 | `docker compose up` 실행 시 `api` 컨테이너 정상 동작 | Medium |
| FE-BE 연동 | 프론트엔드에 `NEXT_PUBLIC_API_BASE_URL` 환경 변수 설정 | FE가 `http://localhost:8000/healthz` 호출 성공 | Low |
| 최종 커밋 및 푸시 | 1단계 결과물을 원격 레포지토리에 푸시 | `feat: scaffold(frontend/api/docker)` 커밋 푸시 완료 | Low |

### Compose 예시 스니펫
```yaml
version: "3.9"
services:
  api:
    build:
      context: ./apps/api
      dockerfile: Dockerfile
    ports:
      - "8000:8000"
    command: uvicorn main:app --host 0.0.0.0 --port 8000 --reload
    restart: unless-stopped
    environment:
      - PYTHONUNBUFFERED=1
```

개발/운영 분리를 위해 `docker-compose.dev.yml`를 추가하고 개발 옵션(예: `--reload`)을 분리하는 것을 권장합니다.

### FastAPI `/healthz` 라우트 예시
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.get("/healthz")
def healthz():
    return {"status": "ok", "version": "0.1.0"}
```

### Next.js API 호출 예시
```ts
// apps/frontend/lib/api.ts
const BASE = process.env.NEXT_PUBLIC_API_BASE_URL ?? "http://localhost:8000";

export async function getHealth() {
  const res = await fetch(`${BASE}/healthz`, { cache: "no-store" });
  if (!res.ok) throw new Error(`Health check failed: ${res.status}`);
  return res.json();
}
```

### Phase 4: 배포 및 검증 (해당 없음)
- 이번 이슈는 로컬 개발 환경 구성에 중점을 두므로, 별도의 스테이징/운영 배포 단계는 없습니다.

---

## Step 5: Risk Assessment & Mitigation

### High Risk Items
| Risk | Impact | Probability | Mitigation Strategy |
|---|---|---|---|
| Docker 환경 설정 오류 | Medium | Medium | 공식 Docker 문서를 참고하고, 단계별로 설정을 검증하며 진행합니다. |
| 의존성 버전 충돌 | Low | Medium | `package.json`과 `requirements.txt`에 명시된 버전을 사용하여 환경을 통일합니다. |
| CORS 오구성으로 FE-BE 통신 실패 | Medium | Medium | FastAPI `CORSMiddleware`에 `http://localhost:3000`을 명시하고 E2E로 확인합니다. |
| 포트 충돌(3000/8000 사용 중) | Low | Medium | 포트를 가변(env)로 정의하고 충돌 시 다른 포트로 재할당합니다. |
| Docker 빌드 캐시/권한 문제 | Medium | Low | `--no-cache` 빌드, 파일 권한 점검, 단계별 빌드 로그 확인으로 진단합니다. |

---

## Step 6: Resource Requirements

### Human Resources
- **개발자**: 1명 (Next.js, FastAPI, Docker 경험자)
- **리뷰어**: 1명

### Technical Resources
- **개발 도구**: Git, Node.js (18+), Docker (24+), VSCode (권장)
- **테스트 환경**: 로컬 머신

### Time Estimation
- **총 예상 시간**: 3–4시간 (스캐폴딩, Compose 통합, CORS 포함)
- **버퍼 시간**: 1시간
- **완료 목표일**: 당일

---

## Step 7: Quality Assurance Plan

### Test Strategy
- **Unit Tests**: 각 앱의 스캐폴딩 이후 기본 테스트가 통과하는지 확인합니다.
- **Integration Tests**: 프론트엔드가 Docker 컨테이너로 실행 중인 백엔드 API (`/healthz`)를 성공적으로 호출하는지 확인합니다.

### Minimal Tests & CI 제안
- FastAPI pytest 예시:
```python
# apps/api/test_health.py
from fastapi.testclient import TestClient
from main import app

def test_healthz():
    c = TestClient(app)
    r = c.get("/healthz")
    assert r.status_code == 200
    assert r.json().get("status") == "ok"
```

- CI 워크플로우 초안(후속 이슈에서 추가 권장):
```yaml
name: ci
on: [push, pull_request]
jobs:
  api-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.11'
      - run: pip install -r apps/api/requirements.txt pytest
      - run: pytest -q apps/api
```

### Test Cases
```gherkin
Feature: Environment Setup Verification
  
  Scenario: Backend Health Check
    When the backend service is started with "docker compose up"
    Then a GET request to "http://localhost:8000/healthz" should return a 200 status code
    And the response body should be "{"status":"ok"}"

  Scenario: Frontend Default Page
    When the frontend service is started with "npm run dev"
    Then I should be able to access "http://localhost:3000" in a browser
    And see the default Next.js welcome page
```

---

## Step 8: Communication Plan

- **이슈 댓글 업데이트**: 각 `Phase` 완료 시마다 GitHub 이슈에 진행 상황을 댓글로 업데이트하겠습니다.
- **슬랙/팀즈 채널**: 특이사항 발생 시 실시간으로 공유하겠습니다.

---

## 📋 User Review Checklist

**다음 항목들을 검토해주세요:**

### Planning Review
- [ ] **이슈 분석이 정확한가요?**
- [ ] **선택한 해결 방안이 적절한가요?**
- [ ] **구현 계획이 현실적인가요?**

### Resource Review
- [ ] **시간 추정이 합리적인가요?**
- [ ] **필요한 리소스가 확보 가능한가요?**

### Risk Review
- [ ] **위험 요소가 충분히 식별되었나요?**
- [ ] **롤백 계획이 현실적인가요?** (이번 이슈는 초기 설정이므로 롤백은 `git reset`으로 간단히 처리 가능)

### Quality Review
- [ ] **테스트 전략이 충분한가요?**

---

## 🚀 Next Steps

**검토 완료 후 진행할 작업:**

1.  **Plan Approval**: 위 검토를 통과하면 계획을 승인합니다.
2.  **Issue Update**: GitHub 이슈에 이 계획을 댓글로 추가합니다.
3.  **Start Implementation**: `Phase 1`부터 구현을 시작합니다.

### Git Workflow 참고
- 브랜치 네이밍: `feat/boot-scaffold`, `chore/docker-compose` 등 컨벤션 통일
- 커밋 메시지: Conventional Commits(`feat:`, `chore:`, `docs:`) 사용 권장
- 워크트리 사용 시: 메인 저장소에서 `git worktree add ../worktree/issue-1 <branch>` → 완료 후 `git worktree remove ../worktree/issue-1`

**피드백 요청**
이 계획에 대해 어떻게 생각하시나요? 검토 후 진행 여부를 알려주시면 감사하겠습니다. 수정 또는 보완이 필요한 부분이 있다면 알려주세요.

---

### Compose Dev/Prod 분리 및 실행 경로

다음은 `deploy/` 폴더에 Compose 파일을 두고 실행하는 기준입니다.

```yaml
# deploy/docker-compose.yml (prod 기본값)
version: "3.9"
services:
  api:
    build:
      context: ../apps/api
      dockerfile: Dockerfile
    ports:
      - "8000:8000"
    command: uvicorn main:app --host 0.0.0.0 --port 8000
    restart: unless-stopped
    environment:
      - PYTHONUNBUFFERED=1
```

```yaml
# deploy/docker-compose.dev.yml (개발용 오버레이)
version: "3.9"
services:
  api:
    command: uvicorn main:app --host 0.0.0.0 --port 8000 --reload
    volumes:
      - ../apps/api:/app
    environment:
      - PYTHONUNBUFFERED=1
```

실행 예시:

```bash
cd deploy
# 개발 모드
docker compose -f docker-compose.yml -f docker-compose.dev.yml up --build

# 기본(운영 기본값에 가까운) 모드
docker compose up --build -d
```

참고: Compose 파일을 리포지토리 루트에 둘 경우, `build.context`를 `./apps/api`로 변경하고 루트에서 `docker compose ...`를 실행하세요.

### FastAPI requirements.txt & Dockerfile 템플릿

```text
# apps/api/requirements.txt
fastapi==0.111.0
uvicorn[standard]==0.30.0
pydantic==2.8.2
pytest==8.3.2
httpx==0.27.0
```

```dockerfile
# apps/api/Dockerfile
FROM python:3.11-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

RUN pip install --no-cache-dir --upgrade pip
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

```gitignore
# apps/api/.dockerignore (권장)
__pycache__/
*.pyc
.pytest_cache/
.git
```

### 환경 변수 템플릿

```bash
# apps/frontend/.env.local
NEXT_PUBLIC_API_BASE_URL=http://localhost:8000
```

```bash
# apps/api/.env (선택) — CORS 오리진을 환경 변수로 관리하고 싶을 때
ALLOWED_ORIGINS=http://localhost:3000
```

FastAPI에서 `.env`를 활용하려면 `python-dotenv`를 추가하고, 앱 기동 시 읽어 `CORSMiddleware`의 `allow_origins`에 반영하세요.
