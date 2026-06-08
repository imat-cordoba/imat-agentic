# Code Conventions (iMat)

## General

- Code language: **English** (variables, functions, classes, comments).
- Commit format: **Conventional Commits** — `tipo(scope): descripción` (imperative, lowercase).
  - Types: `feat`, `fix`, `docs`, `chore`, `refactor`, `test`, `ci`.
  - Examples: `feat(web): add appointment booking page`, `docs(negocio): add resumen ejecutivo`.
- No decorative comments; only comments that explain *why*, not *what*.

## Backend (Spring Boot / Java 21)

- Build: **Gradle** (Kotlin DSL where possible).
- Format: **Spotless** (Google Java Format). Run `gradlew spotlessApply` to auto-fix.
- Static analysis: **Checkstyle**.
- Tests: **JUnit 5 + Mockito**; minimum coverage on service layer.
- Package structure: `com.imat.<module>.(controller|service|domain|repository|dto|config)`.
- DTOs as records when immutable.
- Error handling: centralized `@ControllerAdvice`; never expose stack traces to the client.
- DB migrations: **Flyway**, versioned `V<n>__descripcion.sql`.

## Frontend (Next.js 15 / TypeScript)

- App Router + RSC by default; `"use client"` only when strictly necessary.
- Styling: **Tailwind CSS** (utility classes); no CSS-in-JS.
- UI components: **shadcn/ui**. Icons: **Lucide React**.
- Lint: **ESLint** (Next.js config) + **Prettier**.
- Types: do not use `any`; prefer `unknown` when necessary.
- Data fetching: native `fetch` in Server Components; no additional HTTP client for SSR.
- Internationalization: Spanish by default; structure prepared for `next-intl` if it expands.

## Documentation

- Markdown: **markdownlint** (`.markdownlint.json` at repo root).
- ADRs: format `docs/adr/<NNNN>-title-in-kebab-case.md`, template at `references/templates/adr.md`.
- `README.md` always includes: purpose, stack, how to run locally, how to contribute.

## Infra / DevOps

- Docker Compose for local development (PostgreSQL).
- Environment variables: never hardcode; use `.env.local` (not committed) + `.env.example` (committed).
- CI: GitHub Actions; minimum pipeline: lint → typecheck → test → build.
