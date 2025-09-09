# Project Overview

This project is a web application built with a modern technology stack. It consists of a Next.js frontend and a FastAPI backend.

## Key Technologies

*   **Frontend**: [Next.js](https://nextjs.org/) (with TypeScript)
*   **Backend**: [FastAPI](https://fastapi.tiangolo.com/) (with Python)
*   **Containerization**: [Docker](https://www.docker.com/)

## Project Structure

The project is organized into the following main directories:

*   `apps/frontend`: The Next.js frontend application.
*   `apps/api`: The FastAPI backend application.
*   `content/`: Contains content for the website, such as blog posts (in Markdown) and gallery information (in JSON).
*   `deploy/`: Docker configuration for deployment.
*   `docs/`: Project documentation.
*   `commands/`: Templates for common development tasks.

## Development

To run the project locally, you will need to start both the frontend and backend services.

*   **Frontend**:
    ```bash
    cd apps/frontend
    npm run dev
    ```

*   **Backend**:
    ```bash
    cd deploy
    docker compose up --build
    ```

## Testing

The project has a testing setup for both the frontend and backend.

*   **Frontend**: [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/) + [Vitest](https://vitest.dev/) (or Jest)
*   **Backend**: [pytest](https://docs.pytest.org/en/stable/)

## Contribution Guidelines

Please refer to `AGENTS.md` for detailed contribution guidelines, including coding style, branch naming conventions, and pull request procedures.
