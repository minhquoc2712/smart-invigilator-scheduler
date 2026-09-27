# Smart Invigilator Scheduler

> An independent side project inspired by real exam-scheduling pain points at university — **not** affiliated with any coursework. Built to explore workforce-scheduling optimization, microservice architecture, and lightweight AI assistance.

## Motivation

Universities run hundreds of exam sessions per term and must assign invigilators (proctors) to each session while respecting hard rules (no double-booking, minimum headcount per session, availability) and soft preferences (location, fatigue, fairness). Done manually, this takes days and produces uneven workloads. This project builds a small tool that automates the assignment using real optimization techniques, wrapped in a usable web application — turning a classic operations-research problem into a working product.

## Demo

- **Live demo:** [add deployed link here]
- **Video walkthrough:** [add link here]

## Tech Stack

| Layer | Technology | Why |
|---|---|---|
| Frontend | React | Interactive schedule view, dashboards |
| Backend (API/BFF) | Node.js / Express | User/data management, orchestrates the solver call |
| Solver Service | Python + OR-Tools (or PuLP) | Proper optimization tooling for the assignment problem — the right tool for the job, not forced into JS |
| Database | MongoDB | Flexible schema for invigilators, sessions, constraints |
| Containerization | Docker, Docker Compose | Each service ships independently |
| CI/CD | GitHub Actions | Automated build/test/deploy |
| Cloud | [AWS / GCP / Azure — fill in] | Hosting |
| AI (stretch) | LLM API | Explains why a constraint was violated / suggests re-scheduling |

## Architecture

A **polyglot microservices** design: the optimization engine is deliberately a separate Python service rather than reimplementing solver logic in JavaScript, since mature optimization libraries (OR-Tools, PuLP) live in the Python ecosystem.

```
┌─────────────┐      ┌───────────────────┐      ┌──────────────────────┐
│  React SPA  │─────▶│  Node/Express API │─────▶│  Python Solver        │
│ (Frontend)  │◀─────│   (Backend/BFF)   │◀─────│  Service (OR-Tools)   │
└─────────────┘      └─────────┬─────────┘      └──────────────────────┘
                                │
                                ▼
                         ┌─────────────┐
                         │   MongoDB   │
                         └─────────────┘
```

- **Frontend**: upload invigilator/session data, configure constraints, view generated schedule, fairness dashboard
- **Backend**: auth, CRUD for invigilators/sessions/constraints, calls the solver service, persists results
- **Solver service**: exposes a REST endpoint that takes the problem instance and returns an optimal (or best-found) assignment

## Scope

### v1 (MVP)
- [ ] Import invigilators + exam sessions (CSV upload or manual form)
- [ ] Configure basic hard constraints (no double-booking, headcount per session, max sessions per person)
- [ ] Generate schedule via the solver service
- [ ] View schedule as a table/calendar
- [ ] Export schedule (CSV/PDF)

### v2 (stretch)
- [ ] Fairness dashboard (workload distribution chart)
- [ ] Soft constraints (location preference, fatigue penalty)
- [ ] Re-schedule flow when someone reports unavailability last-minute
- [ ] AI assistant: explain why a specific invigilator wasn't assigned to a session, or suggest a fix when the problem is infeasible

### Explicitly out of scope
- Multi-institution/multi-tenant support
- Payment/HR integration
- Formal proofs of constraint properties (this is a product, not a math paper)

## Getting Started

```bash
git clone https://github.com/minhquoc2712/smart-invigilator-scheduler.git
cd smart-invigilator-scheduler
cp .env.example .env
docker-compose up --build
```

- Frontend: `http://localhost:3000`
- Backend API: `http://localhost:5000`
- Solver service: `http://localhost:8000`

## API (representative)

| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/invigilators` | Add an invigilator |
| POST | `/api/sessions` | Add an exam session |
| POST | `/api/schedule/generate` | Run the solver on current data, return assignment |
| GET | `/api/schedule/:id` | Fetch a generated schedule |
| GET | `/api/schedule/:id/export` | Export schedule as CSV/PDF |

## DevOps

- Each service (frontend, backend, solver) is containerized independently
- `docker-compose.yml` orchestrates local multi-service development
- CI pipeline (`.github/workflows/ci.yml`) runs tests and builds images on every push
- [Deployment target and monitoring approach — fill in once deployed]

## Project Structure

```
.
├── frontend/              # React app
├── backend/               # Node/Express API
├── solver/                # Python optimization service (OR-Tools/PuLP)
├── docs/                  # Architecture notes, diagrams
├── docker-compose.yml
└── .github/workflows/
```

## Status

🚧 Planning stage — implementation starting soon.

## License

Personal project, MIT License (or adjust as preferred).