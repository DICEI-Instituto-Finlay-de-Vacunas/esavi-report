# ESAVI Report Platform

Web platform developed for the Instituto Finlay de Vacunas for the reporting and management of Events Supposedly Attributable to Vaccination or Immunization (ESAVI). The project aims to digitize pharmacovigilance processes by allowing reporters to submit adverse events related to vaccination, tracking each case through its life cycle, and providing a structured review workflow for medical reviewers, section heads, and administrators.

This repository contains the full-stack solution for the project, including:

- a React + TypeScript frontend
- an ASP.NET Core 8 backend API
- MySQL as the main relational database
- RabbitMQ for asynchronous messaging and processing
- Docker and Docker Compose for local environment setup

---

## Project Overview

The platform is designed to support the reporting, triage, review, classification, and monitoring of ESAVI cases. It addresses the need for a secure, traceable, and organized digital workflow to document vaccine-related adverse events and support institutional decision-making.

The solution is intended to help the organization:

- capture ESAVI reports from external reporters or internal users
- assign cases to medical reviewers according to priority and workload
- monitor the status of each report through a notification or case number
- maintain historical traceability of actions and statuses
- centralize catalog data such as vaccines, symptoms, vaccination centers, and lots
- provide dashboards for operational oversight and decision support

---

## Current Situation and Project Status

The project is in an active development and functional validation stage. Core platform capabilities are already implemented or under integration, including:

- authentication and authorization with JWT and refresh tokens
- report creation and tracking
- role-based access control
- dashboards and summary panels
- vaccine, symptom, lot, and vaccination-center catalog management
- medical review assignment workflows
- duplicate-report detection logic
- email and messaging integrations
- Docker orchestration for local environments

At this stage, the solution is oriented toward internal institutional use and validation in a development environment rather than large-scale production deployment. The architecture is organized to support future expansion, regulatory requirements, and broader operational deployment.

---

## Main Functional Scope

The platform is structured around several user profiles and business flows:

### Reporter / External User
- submits a vaccination-related adverse event report
- receives confirmation of submission
- can check report status using a notification number

### Medical Reviewer
- receives assigned ESAVI reports
- evaluates the information and updates the report status
- validates severity, causality, and clinical findings

### Section Head / Vaccination Manager
- manages and assigns reports to available reviewers
- monitors review deadlines and workload
- supervises indicators by municipality, section, or operational unit

### Administrator
- manages core catalog data
- reviews all reports in the system
- monitors system performance and operational metrics
- manages access and configuration-related aspects

---

## Technical Architecture

The solution follows a layered architecture with a clear separation between presentation, application, domain, and infrastructure.

### Frontend
- React
- TypeScript
- Vite
- Material UI / component-driven interface

### Backend
- ASP.NET Core 8
- REST API architecture
- JWT-based authentication
- CORS configuration
- middleware for error handling and request limiting

### Data and integration layer
- MySQL as the relational database
- RabbitMQ for messaging and asynchronous operations
- logging infrastructure through Serilog
- email and messaging integrations for notifications

### Deployment model
- Docker and Docker Compose for local environment orchestration
- API exposed on port 5137
- MySQL on port 3306
- RabbitMQ management UI on port 15672

### Simple architecture diagram

```text
+----------------------+
|    Frontend (React)  |
|   Vite + TypeScript   |
+----------+-----------+
           |
           | HTTP / REST
           v
+----------------------+
| ASP.NET Core API     |
|   Controllers / App  |
|   JWT + business     |
+----+-----------+-----+
     |           |
     |           +------------------------+
     |                                    |
     |                                    v
     |                          +----------------------+
     |                          | RabbitMQ             |
     |                          | async jobs / events  |
     |                          +----------+-----------+
     |                                     |
     |                                     v
     |                           +----------------------+
     |                           | MySQL                |
     |                           | relational data      |
     |                           +----------------------+
     |
     +--------------------> +----------------------+
                          | External services    |
                          | - Captcha            |
                          | - Email / SMTP       |
                          | - WhatsApp API       |
                          +----------------------+
```

This is a simplified view of the runtime flow:

- the frontend sends requests to the API
- the API validates input, checks authentication, and persists data in MySQL
- RabbitMQ is used for asynchronous background tasks and event-driven processing
- external services such as captcha validation, email delivery, and WhatsApp notifications are called from the backend when required

---

## Repository Structure

```text
esavi-report/
├── README.md
├── backend/
│   ├── .env
│   ├── docker-compose.yml
│   ├── Dockerfile
│   ├── Finlay.PharmaVigilance.sln
│   ├── Finlay.PharmaVigilance.API/
│   ├── Finlay.PharmaVigilance.Application/
│   ├── Finlay.PharmaVigilance.Domain/
│   ├── Finlay.PharmaVigilance.Infrastructure/
│   └── Requests/
├── frontend/
│   ├── .env
│   ├── package.json
│   ├── Dockerfile
│   ├── vite.config.ts
│   └── src/
└── informe_tecnico.tex
```

---

## Required Dependencies

To run the project locally, the following tools are recommended or required:

### Required
- .NET SDK 8.0
- Node.js 18+ or newer
- npm
- Docker Desktop / Docker Engine
- Docker Compose
- MySQL client or database management tool (optional but useful for inspection)
- RabbitMQ management console (optional but useful for validation)

### Recommended
- Git
- Visual Studio 2022 or VS Code
- Postman or Insomnia for API testing
- DBeaver, MySQL Workbench, or similar DB client
- browser with dev tools

---

## Environment and Integration Dependencies

The application includes several external integrations and service dependencies configured in environment files and application settings:

- MySQL database
- RabbitMQ message broker
- SMTP-based email sending
- EmailJS / Resend integration
- Friendly Captcha / reCAPTCHA validation
- WhatsApp API integration for notifications or communication flow
- Firebase configuration for web push/notification support

Some of these values are stored in `.env` or in `appsettings.json`, so a valid local configuration is needed to fully run notifications and secure features.

> Important: some keys and credentials are currently embedded in the project configuration for local development. In a production or shared environment, these values should be moved to a secure secret-management strategy.

---

## Quick Start

### 1. Clone the repository

```bash
git clone <repository-url>
cd esavi-report
```

### 2. Start the backend and infrastructure services

From the `backend` folder:

```bash
cd backend
docker compose up -d
```

This starts:
- MySQL on port `3306`
- RabbitMQ on port `5672` and management UI on `15672`
- the API service on port `5137`

### 3. Initialize the database

The project includes a migration/start script pattern for database initialization. In the backend folder, run:

```bash
./start_migrations.sh
```

On Windows PowerShell, this may require using Git Bash or a compatible shell, depending on how the script is configured.

If you prefer a direct .NET approach, also possible commands are:

```bash
dotnet restore

dotnet build
```

and then run the API project:

```bash
dotnet run --project Finlay.PharmaVigilance.API/Finlay.PharmaVigilance.API.csproj
```

### 4. Start the frontend

From the `frontend` folder:

```bash
cd frontend
npm install
npm run dev
```

The frontend development server usually runs on the default Vite port, typically:

- `http://localhost:5173`

The backend API is typically available at:

- `http://localhost:5137/api`

---

## Docker-Based Setup

The repository contains Docker configurations for both the backend and frontend.

### Backend Docker Compose

File: `backend/docker-compose.yml`

It defines services for:
- MySQL
- RabbitMQ
- API

The default values are:

- database name: `finlay_dev`
- root password: `root_Jabel`
- RabbitMQ user: `guest`
- RabbitMQ password: `guest`

### Backend image build

```bash
cd backend
docker build -t finlay-api .
```

### Frontend image build

```bash
cd frontend
docker build -t finlay-frontend .
```

---

## Configuration Files

### Backend configuration
The backend reads configuration from:

- `backend/Finlay.PharmaVigilance.API/appsettings.json`
- `backend/Finlay.PharmaVigilance.API/appsettings.Development.json`
- `backend/.env`

Key settings include:
- database connection string
- JWT secret and lifecycle values
- email SMTP configuration
- Friendly Captcha / reCAPTCHA keys
- WhatsApp API integration values

### Frontend configuration
The frontend uses environment variables defined in:

- `frontend/.env`

Example values include:
- `VITE_API_BASE_URL`
- `VITE_RECAPTCHA_SITE_KEY`
- `VITE_FRIENDLYCAPTCHA_SITE_KEY`
- Firebase configuration keys

---

## How the System Works at a Basic Level

The basic workflow of the platform is as follows:

1. A reporter submits an ESAVI case through the web interface.
2. The system validates the data and stores the report in the database.
3. A duplicate detection mechanism can identify possible repeated records.
4. A section head or admin assigns the report to a medical reviewer.
5. The reviewer evaluates the clinical information, severity, and possible relationship to vaccination.
6. The system updates the report status as the case advances.
7. The reporter or internal users can consult the status using a notification/reference number.
8. Dashboard data aggregates metrics for operational monitoring and institutional analysis.

This creates a traceable, auditable, and systematic process for pharmacovigilance case management.

---

## Current Project Notes

This project is a strong foundation for an institutional ESAVI reporting system, but it still shows characteristics of an active development project:

- the environment is designed for development and validation more than for full production deployment
- several third-party integrations are present and require valid credentials/configuration
- some secrets are currently stored in configuration files
- the system is structured for future improvements and regulatory expansion

---

## Future Improvements and Planned Updates

The project is expected to continue evolving with improvements in the following areas:

- stronger role and permissions governance
- expanded audit trails and event logging
- additional export/reporting formats such as PDF and Excel
- better analytics and dashboards for institutional performance
- more robust duplicate-case management workflows
- improved notification channels and automation
- broader deployment support for production environments
- security hardening and secret management modernization
- API versioning and integration standardization
- mobile-friendly or responsive workflow enhancements
- improved test coverage, CI/CD, and quality gates

---

## Suggested Next Steps

For a production-quality deployment, the roadmap should include:

1. secure secret management using environment variables or vault solutions
2. database backup and recovery strategy
3. performance and concurrency validation against realistic usage
4. automated tests for API and frontend flows
5. CI/CD pipeline for build, test, and deployment
6. deployment configuration for staging and production
7. review of institutional compliance requirements and audit standards

---

## Summary

This project represents a digital pharmacovigilance platform for the public and institutional management of ESAVI reports. It combines modern web technologies, secure backend services, database persistence, messaging, and operational monitoring to support a structured reporting and review workflow.

The current repository already contains the core foundations for an institutional application and is positioned as a functional platform in active validation, with clear room for expansion toward a more robust, secure, and production-ready system.

---

## License and Project Context

This repository is intended for internal institutional use and project development. Any broader production or public deployment must be reviewed and aligned with the operational, legal, and cybersecurity requirements of the corresponding organization.