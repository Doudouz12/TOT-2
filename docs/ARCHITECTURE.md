# Digital Readiness Super AI Consultant – Architecture

## High-Level Text Diagram
```
[Browser UI]
  |-- collects company profile + assessment responses
  v
[Frontend (Vanilla JS / React-ready)]
  |-- Validation, local state, API calls
  v
[Express API Gateway]
  |-- Routes: /api/profile, /api/assess, /api/results
  |-- Auth hook (future), rate limits, logging
  v
[Coordinator Agent Service]
  |-- Orchestrates specialized agents via Azure AI Foundry
  |-- Request context: profile, answers, benchmarks, static datasets
  |-- Output envelope: scores, benchmarks, opportunities, ROI, inefficiencies, use cases, executive summary
  |--> [Assessment & Scoring Agent]
  |--> [Benchmarking Agent]
  |--> [Opportunity Mapping Agent]
  |--> [ROI & Impact Agent]
  |--> [CX/Ops Inefficiency Agent]
  |--> [Use Case & Video Curator Agent]
  |<-- Aggregates responses + consistency checks
  v
[Response Composer]
  |-- Shapes JSON for dashboard + export
  v
[Frontend Dashboard]
  |-- Scorecards, opportunity list, impact tiles, inefficiency heatmap, use-case cards, video placeholders

[Data Layer]
  |- ./backend/data/benchmarks.json (initial static set)
  |- ./backend/data/usecase-library.json (extensible)
  |- Future: Azure Storage / Cosmos DB / RAG index

[Deployment]
  |- Azure App Service (Node.js)
  |- Azure Application Settings for secrets/env vars
  |- GitHub Actions CI/CD (build, lint, deploy)
```

## Component Responsibilities
- **Frontend**: lightweight SPA with forms (profile + 10–20 question assessment), dashboard rendering, copy/export action. No secret handling in browser.
- **API Layer (Express)**: JSON endpoints, input validation, schema typing, logging, error handling, CORS. Acts as the single entry point for AI orchestration.
- **Coordinator Agent**: Receives normalized payload; calls specialized agents sequentially or in parallel; performs consistency checks (e.g., all opportunities include impact, reasoning, tech enablers, outcomes); merges into a single result document.
- **Specialized Agents**:
  - *Assessment & Scoring*: Computes readiness score by theme; generates narrative snapshot.
  - *Benchmarking*: Compares to market/peer baselines; references static benchmark data.
  - *Opportunity Mapping*: Produces unlimited ranked opportunities with description, impact, reasoning, tech enablers, expected outcomes.
  - *ROI & Impact*: Estimates value unlocked (efficiency, savings, CX, productivity) and creates impact ranges.
  - *CX/Ops Inefficiency*: Detects operational pain points and value leakage across CX, Ops, Workforce, Data/Analytics, Platform, Automation/AI.
  - *Use Case & Video Curator*: Maps opportunities to realistic use cases and placeholder video references.
- **Response Composer**: Shapes the final payload for the dashboard and export-ready summary.

## Folder Structure (initial)
```
root/
├── backend/
│   ├── server.js               # Express bootstrap (to be added)
│   ├── routes/                 # Route definitions
│   ├── controllers/            # Request handlers
│   ├── agents/                 # Agent prompt + call logic
│   ├── services/               # Azure AI Foundry client + coordinator
│   ├── data/                   # Static benchmark/use-case seeds
│   ├── schemas/                # JSON schemas for validation
│   └── utils/                  # Helpers (logging, formatting)
├── frontend/
│   ├── index.html              # Dashboard shell
│   ├── styles.css              # Styling
│   └── app.js                  # Front-end logic
├── docs/
│   ├── ARCHITECTURE.md         # This file
│   └── ROADMAP.md              # Build sequence
├── .env.example                # Environment variables (to be added)
└── README.md                   # Project overview
```

## Environment & Secrets
- Define the following in `.env` (and Azure App Service Application Settings):
  - `PORT` – server port (default 3000)
  - `AZURE_OPENAI_ENDPOINT` – Azure AI Foundry/OpenAI endpoint
  - `AZURE_OPENAI_KEY` – primary/secondary key
  - `AZURE_OPENAI_DEPLOYMENT` – deployment name
  - `AZURE_OPENAI_MODEL` – model identifier
- Never commit `.env`. Use `.env.example` for placeholders.
- In Azure App Service, set these via *Configuration → Application settings*.

## Deployment Flow (GitHub → Azure App Service)
1. Push to `main` or a release branch.
2. GitHub Actions builds/tests the Node app, runs lint/unit tests, then deploys to Azure App Service using a publish profile or OpenID Connect.
3. App Service serves both API and static frontend (via `express.static` or App Service static site), with CORS configured for any additional origins.

## Extensibility Notes
- RAG-ready: add a retrieval layer (Azure AI Search) under `services/` and feed citations to agents.
- Multi-agent coordination can be sequential (deterministic) or parallel (Promise.all) with a reconciliation step to keep outputs aligned.
- Data layer can evolve to Cosmos DB for persisted assessments; keep schemas in `schemas/` for validation/migration.
- Add observability (App Insights) via middleware hooks and trace IDs passed into agent prompts.
```
