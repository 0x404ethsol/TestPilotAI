# TestPilot AI — Autonomous Mobile App QA Platform

> **AI-powered mobile app testing for modern teams.** TestPilot AI uses a multi-agent architecture to autonomously plan, execute, and report on Android app test scenarios described in plain English.

---

## Table of Contents

- [Overview](#overview)
- [Key Use Cases](#key-use-cases)
- [Architecture](#architecture)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Project Structure](#project-structure)
- [AI Agent System](#ai-agent-system)
- [CI/CD Integration](#cicd-integration)
- [Fintech Compliance Auditing](#fintech-compliance-auditing)
- [Security](#security)
- [API Reference](#api-reference)
- [Roadmap](#roadmap)

---

## Overview

TestPilot AI is an enterprise-grade autonomous QA platform that eliminates the need for manual mobile test scripting. Instead of writing brittle Appium or Espresso scripts, teams simply describe what they want tested in plain language:

> *"Open the banking app, log in with valid credentials, transfer $50 to savings, and verify the balance updated correctly."*

Three specialized AI agents — **Planner**, **Executor**, and **Reporter** — coordinate to turn that sentence into a fully executed, documented test run with screenshots, step-by-step logs, and a pass/fail verdict.

---

## Key Use Cases

### 1. CI/CD Pipeline Integration
Trigger automated regression test suites on every pull request or merge. TestPilot AI connects directly to GitHub Actions, GitLab CI, Jenkins, and Bitrise via webhook endpoints. When your pipeline fires, the agents spin up, execute your scenarios against real Android devices, and report results back — all before code reaches production.

### 2. Fintech Compliance Testing
Built-in audit templates for **PCI-DSS**, **SOC 2**, and **GDPR** ensure your mobile banking, payments, or trading app meets regulatory requirements. Each compliance template defines the exact test scenarios mandated by the standard, runs them automatically, and generates a structured audit report you can share with auditors.

### 3. Device Fleet QA
Manage a fleet of Android devices and assign test runs to specific devices or device profiles. Track the health and availability of each device from the dashboard.

---

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                   React Frontend                    │
│  Landing · Dashboard · Runs · CI/CD · Compliance   │
└──────────────────────┬──────────────────────────────┘
                       │ REST + WebSocket
┌──────────────────────▼──────────────────────────────┐
│              Node.js / Express Backend              │
│   Rate Limiting · Helmet CSP · JWT Auth            │
│   REST API · WebSocket Broadcast · Drizzle ORM     │
└──────────┬──────────────────────┬───────────────────┘
           │                      │
           │ Internal HTTP        │ PostgreSQL
┌──────────▼──────────┐  ┌────────▼───────────────────┐
│  Python Agent API   │  │      PostgreSQL DB          │
│  (CrewAI / port 8080│  │  test_runs · test_steps    │
│                     │  │  cicd_configs · compliance │
│  ┌───────────────┐  │  └────────────────────────────┘
│  │ PlannerAgent  │  │
│  │ ExecutorAgent │  │
│  │ ReporterAgent │  │
│  └───────┬───────┘  │
└──────────┼──────────┘
           │ ADB
┌──────────▼──────────┐
│   Android Device    │
│  (Open-AutoGLM)     │
└─────────────────────┘
```

The system has **graceful fallback**: if the Python agent server isn't running, the Node.js backend automatically falls back to AI-simulated execution using Claude, GPT-4o, or Gemini — so the dashboard always works even without a connected Android device.

---

## Features

| Feature | Description |
|---|---|
| **Natural Language Tests** | Describe test scenarios in plain English — no code required |
| **Multi-Agent Execution** | Planner decomposes, Executor runs, Reporter summarizes |
| **Real-time Dashboard** | Live WebSocket updates as agents execute each step |
| **CI/CD Webhooks** | Trigger tests from any CI pipeline via HTTP webhook |
| **Compliance Templates** | PCI-DSS, SOC 2, GDPR audit suites built in |
| **Device Fleet** | Track and manage Android device inventory |
| **Multi-Model AI** | Automatic fallback across Claude, GPT-4o, and Gemini |
| **Web Search** | Agents can research app behavior using Serper API |
| **Screenshot Capture** | Each test step captures device screenshots |
| **Audit Reports** | Structured compliance reports with pass/fail breakdown |

---

## Tech Stack

### Frontend
- **React 18** + TypeScript
- **Wouter** — client-side routing
- **TanStack React Query** — server state and caching
- **shadcn/ui** + Radix UI — component library
- **Tailwind CSS** — dark cyber-tech theme
- **Framer Motion** — animations
- **WebSocket** — real-time test progress

### Backend (Node.js)
- **Express.js** + TypeScript
- **Drizzle ORM** + PostgreSQL
- **Helmet** — security headers + CSP
- **express-rate-limit** — abuse prevention
- **bcryptjs** — password hashing
- **jsonwebtoken** — API authentication
- **WebSocket (ws)** — live broadcast

### AI Providers
- **Anthropic Claude** (claude-opus-4-5) — primary
- **OpenAI GPT-4o** — secondary fallback
- **Google Gemini 2.5 Pro** — tertiary fallback
- **Serper API** — web search for app research

### Agent Backend (Python)
- **CrewAI** — multi-agent orchestration
- **Open-AutoGLM** — vision-language model for Android UI interaction
- **ADB** — Android Debug Bridge device control

---

## Getting Started

### Prerequisites
- Node.js 20+
- Python 3.11+
- PostgreSQL database
- Android device with USB debugging enabled (optional — works in simulation mode without one)

### 1. Clone the Repository
```bash
git clone https://github.com/0x404ethsol/TestPilotAI.git
cd TestPilotAI
```

### 2. Install Node.js Dependencies
```bash
npm install
```

### 3. Install Python Dependencies
```bash
pip install crewai crewai-tools flask
```

### 4. Configure Environment Variables
Copy the example env file and fill in your API keys:
```bash
cp .env.example .env
```
See the [Environment Variables](#environment-variables) section for details.

### 5. Initialize the Database
```bash
npm run db:push
```

### 6. Start the Web Dashboard
```bash
npm run dev
```
The dashboard will be available at `http://localhost:5000`.

### 7. (Optional) Start the Python Agent Server
```bash
python agents/api_server.py
```
This runs on port 8080 and enables real Android device testing. Without it, the platform runs in AI simulation mode.

---

## Environment Variables

Create a `.env` file in the project root. **Never commit this file — it is gitignored.**

```env
# Database
DATABASE_URL=postgresql://user:password@localhost:5432/testpilot

# AI Providers (at least one required)
ANTHROPIC_API_KEY=your_claude_api_key
OPENAI_API_KEY=your_openai_api_key
GEMINI_API_KEY=your_gemini_api_key

# CrewAI (for Python agent backend)
CREWAI_API_KEY=your_crewai_api_key

# Web Search
SERPER_API_KEY=your_serper_api_key

# Security (generate a strong random string)
JWT_SECRET=your_long_random_jwt_secret_here

# Agent Backend (optional)
AUTOGLM_BASE_URL=http://localhost:8000/v1
AUTOGLM_MODEL=autoglm-phone-9b
AGENT_API_PORT=8080
AGENT_API_URL=http://localhost:8080
```

---

## Project Structure

```
TestPilotAI/
├── client/                    # React frontend
│   └── src/
│       ├── pages/
│       │   ├── landing.tsx        # Marketing landing page
│       │   ├── dashboard.tsx      # Main dashboard
│       │   ├── test-runs.tsx      # All test runs
│       │   ├── run-details.tsx    # Individual run view
│       │   ├── cicd-pipelines.tsx # CI/CD configuration
│       │   ├── compliance.tsx     # Compliance auditing
│       │   └── device-fleet.tsx   # Device management
│       └── components/
├── server/                    # Node.js backend
│   ├── index.ts               # Express app + security middleware
│   ├── routes.ts              # API route handlers
│   ├── auth.ts                # JWT + bcrypt authentication
│   ├── storage.ts             # Drizzle ORM database layer
│   ├── ai.ts                  # Multi-provider AI integration
│   └── search.ts              # Serper web search
├── agents/                    # Python CrewAI backend
│   ├── mobile_qa_crew.py      # Main crew orchestrator
│   ├── planner_agent.py       # Test plan decomposition
│   ├── executor_agent.py      # Android UI interaction
│   ├── reporter_agent.py      # Results compilation
│   ├── api_server.py          # HTTP API for Node.js integration
│   └── tools/                 # Agent tools (automation, screenshots)
├── shared/
│   └── schema.ts              # Drizzle schema + Zod types
└── README.md
```

---

## AI Agent System

TestPilot AI uses three specialized agents that collaborate on every test run:

### Planner Agent
Receives the natural language test scenario and decomposes it into ordered, actionable steps. It understands the difference between navigation actions, input actions, assertion actions, and wait conditions. Output is a structured test plan handed to the Executor.

### Executor Agent
Takes the plan and executes it step-by-step on the connected Android device using Open-AutoGLM — a vision-language model that understands mobile UI screenshots and issues precise tap, swipe, and type commands via ADB. Each step is logged with a screenshot for traceability.

### Reporter Agent
After execution completes, the Reporter compiles a structured report: overall pass/fail, step-by-step results with evidence, detected issues, and recommendations. For compliance runs, it maps results to specific regulatory requirements.

---

## CI/CD Integration

### Setting Up a Pipeline

1. Navigate to **CI/CD Pipelines** in the dashboard
2. Click **New Pipeline** and fill in:
   - Repository URL
   - Branch to watch
   - Trigger event (pull request / push / schedule)
   - Test scenarios to run
3. Copy the generated **Webhook URL**
4. Add it to your CI system (GitHub Actions, GitLab CI, Jenkins, etc.)

### GitHub Actions Example
```yaml
name: Mobile QA
on: [pull_request]
jobs:
  mobile-qa:
    runs-on: ubuntu-latest
    steps:
      - name: Trigger TestPilot AI
        run: |
          curl -X POST https://your-testpilot-domain.com/api/webhooks/cicd/${{ secrets.TESTPILOT_PIPELINE_ID }} \
            -H "Content-Type: application/json" \
            -H "X-Webhook-Secret: ${{ secrets.TESTPILOT_WEBHOOK_SECRET }}" \
            -d '{"ref": "${{ github.ref }}", "commit": "${{ github.sha }}"}'
```

---

## Fintech Compliance Auditing

TestPilot AI includes built-in compliance templates for financial applications:

| Standard | Focus Areas |
|---|---|
| **PCI-DSS** | Payment data handling, encryption, session security, access controls |
| **SOC 2** | Availability, confidentiality, processing integrity, security |
| **GDPR** | Data minimization, consent flows, right to deletion, data portability |

### Running a Compliance Audit

1. Go to **Compliance** in the dashboard
2. Select a template (or create a custom one)
3. Click **Run Audit**
4. Monitor real-time execution as each requirement is verified
5. Download the structured audit report when complete

---

## Security

TestPilot AI implements several layers of security:

- **Helmet.js** — sets strict HTTP security headers including Content Security Policy
- **Rate Limiting** — 100 requests/15 min on all API routes; 10 requests/15 min on auth endpoints
- **bcrypt** — all passwords are hashed with bcrypt before storage (cost factor 12)
- **JWT Authentication** — stateless token-based auth protects all write operations
- **HMAC Webhook Validation** — webhook endpoints verify request signatures using the configured webhook secret
- **Zod Input Validation** — all API inputs are validated against strict schemas before processing
- **Request Body Limits** — 10MB max on all requests
- **No Secrets in Code** — all API keys and credentials are loaded from environment variables only

---

## API Reference

### Authentication
| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/auth/register` | Create a new user account |
| `POST` | `/api/auth/login` | Log in and receive a JWT token |

### Test Runs
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/runs` | List all test runs |
| `POST` | `/api/runs` | Create a new test run |
| `GET` | `/api/runs/:id` | Get run details with steps and logs |
| `PATCH` | `/api/runs/:id/status` | Update run status |
| `POST` | `/api/runs/:id/execute` | Start execution |
| `POST` | `/api/runs/:id/steps` | Add a step to a run |
| `PATCH` | `/api/steps/:id/status` | Update step status |
| `POST` | `/api/steps/:id/logs` | Add a log entry to a step |

### CI/CD Pipelines
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/cicd-configs` | List all pipeline configurations |
| `POST` | `/api/cicd-configs` | Create a new pipeline |
| `PATCH` | `/api/cicd-configs/:id` | Update a pipeline |
| `DELETE` | `/api/cicd-configs/:id` | Delete a pipeline |
| `POST` | `/api/webhooks/cicd/:id` | Webhook trigger (HMAC verified) |

### Compliance
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/compliance-templates` | List compliance templates |
| `POST` | `/api/compliance-templates` | Create a custom template |
| `GET` | `/api/compliance-runs` | List all audit runs |
| `POST` | `/api/compliance-runs` | Start a compliance audit |

### System
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/capabilities` | AI providers and feature status |
| `GET` | `/api/agents/health` | Python agent API health check |
| `POST` | `/api/search` | Web search via Serper |
| `POST` | `/api/demo-requests` | Submit a demo request |

---

## Roadmap

- [ ] iOS device support (XCUITest integration)
- [ ] Visual regression testing with screenshot diffing
- [ ] Slack / Teams test result notifications
- [ ] Parallel test execution across device fleet
- [ ] Custom AI model fine-tuning on your app's UI
- [ ] Self-hosted deployment with Docker Compose
- [ ] Test scenario library and marketplace

---

## License

This project is proprietary software. All rights reserved.

---

*Built with CrewAI, React, and a lot of coffee.*
