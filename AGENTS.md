# Repository Guidelines

This repository hosts a Next.js (frontend) + FastAPI (backend) project with a Docker-based backend and file-driven content. Follow these conventions to keep contributions consistent and easy to review.

## Project Structure & Module Organization
- apps/frontend — Next.js (TypeScript) app; static assets in `apps/frontend/public/**`.
- apps/api — FastAPI app; serves `/api/v1/*`.
- content/posts/*.md — Markdown posts (frontmatter: title, date, tags, cover, excerpt?).
- content/gallery/*.json — Album metadata; images in `content/images/**`.
- deploy/docker-compose.yml — Local runtime for API; frontend runs locally.

## Build, Test, and Development Commands
- Frontend dev: `cd apps/frontend && npm run dev` (env: `NEXT_PUBLIC_API_BASE_URL=http://localhost:8000`).
- API (Docker): `cd deploy && docker compose up --build`.
- Health check: `curl http://localhost:8000/healthz` → `{"status":"ok"}`.
- Frontend build: `cd apps/frontend && npm run build` (optional during CI/preview).

## Coding Style & Naming Conventions
- TypeScript: 2-space indent, ESLint/Prettier; React components in PascalCase (e.g., `FamilyCard.tsx`).
- Python: PEP 8, 4-space indent; module names snake_case.
- Content slugs: kebab-case (e.g., `first-trip-to-jeju.md`).
- Branches: `feat/<slug>`, `fix/<slug>`, `chore/<slug>`.

## Testing Guidelines
- Frontend unit/integration: React Testing Library + Vitest (or Jest) in `apps/frontend` (e.g., `__tests__/*.test.tsx`).
- E2E (optional): Playwright in `apps/frontend`.
- Backend: pytest in `apps/api` (run via container: `docker compose exec api pytest`).
- Aim for practical coverage on core flows (posts list/detail, gallery load, healthz).

## Commit & Pull Request Guidelines
- Conventional Commits: `feat: ...`, `fix: ...`, `chore: ...`, `docs: ...`.
- PRs: link issues, describe changes and scope, include screenshots for UI, note test impact and risks/rollback.
- Keep PRs small and focused; add migration notes if touching content structure.

## Security & Configuration Tips
- Do not commit secrets; use `.env.local` (frontend) and environment variables for the API (`ALLOWED_ORIGINS`).
- Default API base: `http://localhost:8000`; all endpoints under `/api/v1`.
