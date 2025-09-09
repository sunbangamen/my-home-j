# Feature Breakdown: Phase 1 - Environment Setup & Scaffolding

---

## Problem Analysis

### 1. Problem Definition and Complexity Assessment
- **Problem**: Phase 1 — Environment Setup & Scaffolding
- **Complexity**: Medium
- **Estimated Time**: 2 hours
- **Key Challenges**:
    - Configuring the interaction between Next.js, FastAPI, and Docker.
    - Establishing the initial project structure correctly.
    - Ensuring a consistent development environment.

### 2. Scope and Constraints
- **Included Scope**:
    - Git repository initialization and remote connection.
    - Creation of core directory structure: `apps`, `content`, `deploy`.
    - Scaffolding the Next.js frontend app (`create-next-app`).
    - Scaffolding the FastAPI backend app (basic `main.py`, `Dockerfile`).
    - Running the backend API service using Docker Compose.
    - Verifying the basic `healthz` call between the frontend and backend.
- **Excluded Scope**:
    - Implementation of actual business logic (posts, gallery, etc.).
    - CI/CD pipeline setup.
    - Detailed UI/UX design and styling.
    - Database integration.
- **Constraints**:
    - The backend must run in a Docker container environment.
    - The frontend must call the API at `http://localhost:8000`.
- **Prerequisites**:
    - Git, Node.js (18+), and Docker (24+) must be installed on the local machine.

---

## Task Breakdown

### Step 1: Repository and Directory Structure Setup
**Goal**: Create the basic skeleton of the project and start version control.

| Task | Description | Definition of Done (DoD) | Priority |
|---|---|---|---|
| Git Repository Initialization | `git init` and connect to a remote repository. | `main` branch pushed to the remote repository. | High |
| Directory Structure Creation | Create folders like `apps/frontend`, `apps/api`, `content`, `deploy`. | The directory structure specified in `execution_plan.md` is complete. | High |
| `.gitignore` File Creation | Exclude unnecessary files like `node_modules`, `.next`, `.env`. | `.gitignore` file is committed. | High |

### Step 2: Frontend and Backend Scaffolding
**Goal**: Generate the basic code for each application and make them runnable.

| Task | Description | Definition of Done (DoD) | Dependencies |
|---|---|---|---|
| Next.js App Creation | Install the app in `apps/frontend` using the `npx create-next-app` command. | Successfully access the default Next.js page at `localhost:3000`. | Phase 1 Complete |
| FastAPI App Creation | Create `main.py`, `requirements.txt`, and `Dockerfile` in `apps/api`. | `/healthz` responds 200 OK when the API runs via Docker Compose. | Phase 1 Complete |
| API Dockerization | Configure the environment to build the FastAPI app using a `Dockerfile`. | API image builds successfully; runs with Compose and exposes port 8000. | FastAPI App Creation Complete |

Additional Configuration Artifacts (Recommended)
- `.env.local.example` (frontend): `NEXT_PUBLIC_API_BASE_URL=http://localhost:8000`
- `.env.example` (api): `ALLOWED_ORIGINS=http://localhost:3000`
- `.gitignore`: `node_modules/`, `.next/`, `.env*`, `__pycache__/`, `*.pyc`, etc.
- Versioning/Editor: `.nvmrc` (e.g., `20`), `.editorconfig` (2-space TS, 4-space Py)

### Step 3: Docker Compose Integration and Final Verification
**Goal**: Run the backend service with Docker Compose and verify the connection with the frontend.

| Task | Description | Definition of Done (DoD) | Risk |
|---|---|---|---|
| Docker Compose File Creation | Define the `api` service in `deploy/docker-compose.yml`. | `docker compose up --build` runs the `api` container; volume mounts `../content:/app/content:ro`; env `ALLOWED_ORIGINS`. | Medium |
| Frontend-Backend Integration | Set the `NEXT_PUBLIC_API_BASE_URL` environment variable in the frontend. | Frontend calls `/healthz` on `http://localhost:8000` and receives `{"status":"ok"}`. | Low |
| Final Commit and Push | Push the Phase 1 results to the remote repository. | The `feat: scaffold(frontend/api/docker)` commit is pushed. | Low |

Notes
- API prefix: use `/api/v1/*` for all business endpoints (Phase 2+). Health check remains `/healthz`.
- CORS: FastAPI reads allowed origins from `ALLOWED_ORIGINS` (e.g., `http://localhost:3000`).

Compose Snippet (example)
```yaml
services:
  api:
    build: ../apps/api
    ports:
      - "8000:8000"
    environment:
      - ALLOWED_ORIGINS=http://localhost:3000
    volumes:
      - ../content:/app/content:ro
```

---

## Execution Plan

I will now proceed with the actual development according to the plan above. I will report the results as each step is completed.

I will start with **Phase 1: Repository and Directory Structure Setup**.
