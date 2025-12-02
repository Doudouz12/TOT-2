# Build Roadmap – Digital Readiness Super AI Consultant

The following steps are designed for incremental commits and easy verification.

## Phase 0 – Repo Hygiene
1. Add `.env.example` with required variables and notes.
2. Initialize `backend/` and `frontend/` folders with placeholders and lint configs.
3. Add basic `package.json` (scripts: start, dev, lint, test) and `.gitignore`.

## Phase 1 – API & Validation Skeleton
1. Create Express server (`backend/server.js`) with health route and CORS.
2. Define JSON schemas for company profile and assessment answers under `backend/schemas/`.
3. Add validation middleware (e.g., `ajv`) and error handler.
4. Implement routes:
   - `POST /api/profile` (accepts company profile, returns normalized profile)
   - `POST /api/assess` (profile + answers → enqueues to coordinator)
   - `GET /api/results/:id` (retrieve last run, in-memory for now)
5. Add logging utility (console + request ID) to prepare for App Insights.

## Phase 2 – Azure AI Foundry Client & Agent Stubs
1. Implement `services/azureClient.js` using Azure OpenAI REST SDK with env-based config.
2. Create agent prompt templates under `backend/agents/prompts/` (markdown/JSON structure per agent).
3. Implement agent stubs (functions returning mock data) to unblock frontend during early dev.
4. Build `Coordinator` service to call agents sequentially, then merge outputs into a single `ResultEnvelope` JSON.
5. Add unit tests for coordinator shape (Jest) and validation of required fields.

## Phase 3 – Frontend Shell
1. Build `frontend/index.html` with Concentrix-aligned structure: profile form, assessment questions (10–20 across themes), submit button, and result panels.
2. Add `frontend/styles.css` with responsive layout and Concentrix-inspired palette.
3. Implement `frontend/app.js` for form state, input validation, API calls, and rendering results (opportunity cards, impact metrics, inefficiency list, use cases, video placeholders, executive summary).
4. Provide a “Copy Executive Summary” action (clipboard API).

## Phase 4 – Real Multi-Agent Integration
1. Replace agent stubs with Azure AI Foundry calls; include guardrails for required fields and deterministic formatting.
2. Inject benchmark data from `backend/data/benchmarks.json` and sample use cases.
3. Enhance coordinator to run selected agents in parallel (`Promise.all`) with reconciliation for consistency (e.g., every opportunity has impact + use cases + videos).
4. Add caching/trace IDs to correlate requests and agent outputs.

## Phase 5 – Impact & Benchmark Depth
1. Expand benchmark dataset by industry/region and map to assessment themes.
2. Add ROI range calculations (efficiency %, cost savings bands, CX uplift proxies).
3. Include “value leakage” categorization for CX, Ops, Workforce, Data/Analytics, Platform, Automation/AI.

## Phase 6 – Hardening & Observability
1. Add input rate limiting, request size limits, and security headers.
2. Integrate Application Insights (telemetry + custom events for agent calls).
3. Add OpenAPI spec for all endpoints and publish via `/api/docs` (Swagger UI).
4. Write integration tests (supertest) and lint checks (eslint/prettier).
5. Enable GitHub Actions CI (lint, test) and deployment to Azure App Service.

## Phase 7 – Deployment
1. Configure Azure App Service (Node 18/20), set Application Settings for secrets.
2. Wire GitHub Actions workflow using publish profile or OIDC for deployment.
3. Add deployment docs and runbook for rotating keys and updating models.

## Phase 8 – Enhancements (Post-MVP)
- PDF/email export of executive summary.
- Persistent storage (Cosmos DB) with assessment history per client.
- Authentication (Entra ID/B2C) and role-based access.
- RAG enrichment using Concentrix knowledge base.
- Multi-language support (English/French initially).
- Feature-flagged UI experiments and A/B testing for question flow.
