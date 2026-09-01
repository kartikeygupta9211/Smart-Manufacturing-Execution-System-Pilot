# Smart MES Pilot — Project Folder Structure Guide

## 1. Purpose of This Structure

The `project-setup` branch contains the common structure of the Smart Manufacturing Execution System (MES) project. It provides a place for the frontend, documentation, Docker configuration, scripts, and GitHub workflows.

The actual NestJS backend application is being developed separately in the `backend` branch and will be integrated with the main project when the backend setup is ready.

Our project follows a local-first development approach with cloud deployment. The FSD defines the Smart MES Pilot as a digital shop-floor operations platform for a simulated fan-assembly manufacturing line. It covers production execution, materials, quality, downtime, maintenance, genealogy, performance reporting, and AI-assisted capabilities.

---

# 2. Overall Project Structure

```text
smart-mes-pilot/
│
├── frontend/
│
├── docs/
│   ├── architecture/
│   ├── api/
│   ├── database/
│   ├── setup/
│   ├── decisions/
│   └── progress/
│
├── docker/
│
├── scripts/
│
├── .github/
│   └── workflows/
│
├── docker-compose.yml
├── .gitignore
└── README.md
```

> Note: The `backend/` folder is intentionally not shown here because the backend is currently maintained in the separate `backend` branch.

---

# 3. `frontend/`

### Purpose

The `frontend` folder contains the Angular application that users will interact with.

Our FSD specifies Angular, TypeScript, Angular Material, and RxJS for the frontend. The frontend is responsible for operator workbenches, dashboards, administration, and user interaction.

```text
frontend/
└── angular-app/
```

Later, the Angular application can contain areas such as:

```text
Dashboard
Production
Materials
Quality
Maintenance
Genealogy
OEE
AI Assistant
Administration
```

### Example

A production supervisor may open the dashboard and see:

```text
Production Status
-------------------------
Planned:       100
Produced:       85
Good:           80
Rejected:        5
Downtime:       25 min
OEE:             78%
```

The frontend does not directly access the database. It communicates with the backend APIs.

```text
Angular Frontend
       ↓
NestJS API
       ↓
Database
```

### Main owner

**Archana — Frontend / Angular / UI/UX**

Other team members should still understand how the frontend connects to the backend.

---

# 4. `docs/`

### Purpose

The `docs` folder contains project documentation.

Documentation is not something we write only at the end. It should be updated as the project changes.

The FSD expects architecture diagrams, ERDs, API/event documentation, deployment information, testing information, operational guides, and final project documents.

```text
docs/
│
├── architecture/
├── api/
├── database/
├── setup/
├── decisions/
└── progress/
```

---

# 5. `docs/architecture/`

### Purpose

This folder explains **how the complete system is designed**.

Possible documents:

```text
architecture.md
c4-diagram.md
isa95-model.md
sequence-diagrams.md
deployment-architecture.md
```

Important concepts include:

### ISA-95 hierarchy

The FSD uses the manufacturing hierarchy:

```text
Enterprise
   ↓
Site
   ↓
Area
   ↓
Line
   ↓
Work Cell
   ↓
Equipment
```

The MES operates primarily at the manufacturing-operations level, while simulated equipment represents lower-level machine activity.

The project uses one simulated fan assembly line with five workstations.

### Example

```text
Fan Assembly Line
│
├── WS-01 Motor Assembly
├── WS-02 Blade Assembly
├── WS-03 Final Assembly
├── WS-04 Electrical Testing
└── WS-05 Packaging
```

The architecture documentation should explain how these parts communicate.

---

# 6. `docs/api/`

### Purpose

This folder documents how the frontend and other systems communicate with the backend.

For example:

```text
GET /api/v1/production-orders
POST /api/v1/production-orders
GET /api/v1/quality-inspections
GET /api/v1/downtime
```

The FSD requires an API-first approach using versioned OpenAPI contracts.

This means that before frontend and backend developers make assumptions about an API, the team should agree on:

* endpoint
* HTTP method
* request data
* response data
* validation
* error responses

### Example

```text
GET /api/v1/production-orders/PO-001
```

Possible response:

```json
{
  "id": "PO-001",
  "product": "FAN-001",
  "plannedQuantity": 100,
  "producedQuantity": 80,
  "status": "IN_PROGRESS"
}
```

Archana can then build the Angular screen based on the agreed API contract.

Nikitha and Javesh implement the backend according to the same contract.

---

# 7. `docs/database/`

### Purpose

This folder explains the project's data model.

The FSD specifies PostgreSQL with pgvector for transactional data, reporting views, and RAG vectors, while MongoDB is intended for machine/event documents, conversations, and AI traces.

The database documentation should explain:

* entities
* tables/collections
* relationships
* important fields
* identifiers
* constraints
* indexes
* data ownership

### Example MES data

A simplified relationship could look like:

```text
Product
   ↓
BOM
   ↓
Production Order
   ↓
Production Execution
   ↓
Quality Result
   ↓
Finished Product
```

Material lots and serial numbers should also be connected so that the team can trace which component lots were used for a finished product.

---

# 8. `docs/setup/`

### Purpose

This folder explains **how a new team member can run the project**.

This is especially important because all four team members should be able to work with the same project.

Example:

```text
Setup Guide

1. Install Node.js
2. Install Git
3. Install Docker
4. Clone repository
5. Install frontend dependencies
6. Start required services
7. Start Angular
8. Start backend
9. Verify application
```

It should also explain environment variables and common errors.

The goal is:

> A team member should be able to read this document and set up the project without asking another member to manually configure everything.

---

# 9. `docs/decisions/`

### Purpose

This folder stores important **Architecture Decision Records (ADRs)**.

An ADR records a significant technical decision and explains why the team made it.

For example:

```text
Decision:
Use PostgreSQL for transactional MES data.

Reason:
The FSD specifies PostgreSQL and the project requires
structured relationships between production orders,
materials, quality, genealogy and other manufacturing data.
```

Another example:

```text
Decision:
Use MQTT for simulated machine telemetry.

Reason:
The FSD specifies Eclipse Mosquitto MQTT for machine
simulator and telemetry events.
```

This prevents the team from repeatedly asking:

> "Why did we choose this?"

---

# 10. `docs/progress/`

### Purpose

This folder records project progress.

Since we have an eight-week development plan, we can maintain:

```text
weekly-progress.md
```

Example:

```text
Week 1
------
GitHub setup             DONE
Angular setup            DONE
Backend setup            IN PROGRESS
Database design          IN PROGRESS
Architecture             DONE

Week 2
------
Authentication
Angular shell
OpenAPI
Docker
...
```

This helps the entire team understand:

* what is completed
* what is currently being developed
* what is blocked
* who owns each task
* what needs to happen next

---

# 11. `docker/`

### Purpose

This folder contains Docker-related configuration that helps us run project services consistently.

The FSD specifies Docker and Docker Compose for creating a consistent local environment.

Eventually, the project may use containers for services such as:

```text
PostgreSQL
MongoDB
Keycloak
RabbitMQ
Mosquitto
Ollama
Grafana
```

We should not try to configure every service immediately.

We will add them as they become necessary.

---

# 12. `docker-compose.yml`

### Purpose

This file can be used to define the services required for local development.

For example, instead of every team member manually installing and configuring PostgreSQL, Docker Compose can provide a consistent PostgreSQL environment.

Eventually:

```text
docker-compose.yml
       │
       ├── PostgreSQL
       ├── MongoDB
       ├── RabbitMQ
       ├── Mosquitto
       ├── Keycloak
       └── other services
```

The FSD's local development topology specifies services such as PostgreSQL, MongoDB, RabbitMQ, Mosquitto, Keycloak, Ollama, and Grafana with defined local endpoints.

We will add these progressively rather than creating the entire environment at once.

---

# 13. `scripts/`

### Purpose

This folder contains helper scripts used to make repetitive development tasks easier.

Examples that may be added later:

```text
scripts/
├── seed-data
├── setup
├── reset-database
└── test
```

For example, a seed-data script could create synthetic MES data such as:

```text
FAN-001
Motor lots
Blade lots
Production orders
Workstations
Quality results
```

The FSD requires synthetic data rather than confidential production, supplier, customer, or employee data.

---

# 14. `.github/`

### Purpose

The `.github` folder contains GitHub-specific project configuration.

We can use it for:

```text
.github/
├── workflows/
├── ISSUE_TEMPLATE/
└── PULL_REQUEST_TEMPLATE/
```

For now, we mainly created:

```text
.github/workflows/
```

---

# 15. `.github/workflows/`

### Purpose

This folder will eventually contain our CI/CD workflows using GitHub Actions.

A workflow can automatically:

```text
Developer pushes code
        ↓
GitHub Actions starts
        ↓
Install dependencies
        ↓
Run tests
        ↓
Build application
        ↓
Security/dependency checks
        ↓
Package application
        ↓
Deploy
```

The FSD specifies GitHub Actions or GitLab CI for building, testing, scanning, packaging, and deployment.

We don't need to build the complete pipeline immediately.

---

# 16. `.gitignore`

### Purpose

`.gitignore` tells Git which files should **not** be uploaded to GitHub.

Examples include:

```text
node_modules/
.env
dist/
coverage/
temporary files
IDE-specific files
```

This is especially important for protecting environment configuration and preventing unnecessary files from entering the repository.

Never commit passwords, API keys, tokens, or other secrets.

---

# 17. `README.md`

### Purpose

The README is the **first document a developer should read after opening the repository**.

It should eventually explain:

```text
What is Smart MES?
What are we building?
Technology stack
Project structure
How to install
How to run
Branch strategy
Team roles
Important links
Documentation
```

A new team member should be able to understand the project at a high level from the README.

---

# 18. How Everything Connects

The complete system will eventually look approximately like this:

```text
                         USERS
                           │
                           ↓
                  Angular Frontend
                           │
                           │ REST API
                           ↓
                    NestJS Backend
                           │
             ┌─────────────┼─────────────┐
             ↓             ↓             ↓
        PostgreSQL      MongoDB       AI/Ollama
             │             │             │
             │             │             │
             └─────────────┼─────────────┘
                           │
                    Manufacturing
                       Processes
                           │
                     MQTT Simulator
```

Other infrastructure such as Keycloak, RabbitMQ, observability tools, and Docker will be added as the corresponding project features are developed.

---

# 19. How Our Four Team Members Use This Structure

## Archana — Frontend

Main area:

```text
frontend/
```

Also contributes to:

```text
docs/api/
docs/architecture/
```

because the frontend depends on agreed API contracts and overall system design.

---

## Nikitha — Backend

Main development area:

```text
backend/
```

The backend is currently maintained in the separate `backend` branch.

Nikitha also contributes to:

```text
docs/api/
docs/database/
docs/architecture/
```

because backend changes affect APIs, data and architecture.

---

## Javesh — Backend + Integration + Documentation

Main development area:

```text
backend/
```

Documentation responsibility:

```text
docs/api/
docs/database/
docs/architecture/
docs/progress/
```

Javesh also supports API testing and integration.

---

## Kartikey — Project Management + Cloud/DevOps + Documentation

Main areas:

```text
docker/
.github/
scripts/
docs/setup/
docs/decisions/
docs/progress/
```

Kartikey coordinates GitHub, Docker, CI/CD, deployment planning, project tracking and the final demonstration.

---

# 20. Important Team Rule

These folders are **not four separate projects**.

They are parts of one Smart MES application.

For example:

```text
Archana
   ↓
Angular screen
   ↓
API contract
   ↓
Nikitha/Javesh
   ↓
NestJS
   ↓
Database
   ↓
Production information
   ↓
Angular dashboard
```

If one person changes something that affects another person's work, they should communicate the change before implementing it.

---

# 21. Development Flow

For every major feature, follow:

```text
Requirement
     ↓
Understand the business process
     ↓
Design/API decision
     ↓
Create task
     ↓
Create feature branch
     ↓
Develop
     ↓
Test
     ↓
Pull Request
     ↓
Code Review
     ↓
Integration
     ↓
Update Documentation
     ↓
Done
```

This prevents one member from building something that cannot connect to the rest of the system.

---

# 22. What We Have Now vs Later

### Currently

```text
project-setup
│
├── frontend/
├── docs/
├── docker/
├── scripts/
├── .github/
├── docker-compose.yml
├── .gitignore
└── README.md
```

### Backend is separate

```text
backend branch
│
└── backend/
    └── NestJS application
```

### Later

The stable parts will be integrated into the main project:

```text
main
│
├── frontend/
├── backend/
├── docs/
├── docker/
├── scripts/
├── .github/
├── docker-compose.yml
├── .gitignore
└── README.md
```

The goal is to eventually have **one complete Smart MES system**, not permanently separated frontend and backend projects.
