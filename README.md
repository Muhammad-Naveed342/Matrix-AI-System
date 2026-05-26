# Matrix Integration AI Foundations

[![Project Setup](https://img.shields.io/badge/Setup_Guide-Docs-blue)](./setup_guide.md)
[![Python Version](https://img.shields.io/badge/python-3.11%2B-blue)](https://www.python.org/downloads/)
[![Node Version](https://img.shields.io/badge/node-18%2B-green)](https://nodejs.org/)
[![License](https://img.shields.io/badge/License-Proprietary-red.svg)](#)

Enterprise-grade AI infrastructure and applications designed to accelerate IT ticket resolution, enhance organizational visibility, and seamlessly manage AI models and agents. This project establishes the foundation for AI capability management, integrating securely with existing enterprise systems like ConnectWise, Confluence, SharePoint, and Microsoft Entra ID.

---

## 🌟 Key Capabilities & Modules

The project is composed of several critical systems working in tandem to deliver the core functionality:

### 1. Ticket Orchestrator

A full-stack web application designed for Level 1 and Level 2 support ticket assignment assistance.

* **Frontend (`ticket-orchestrator/frontend`)**: Built with React, providing an intuitive interface for managing ticket flows and AI-assisted resolutions.
* **Backend (`ticket-orchestrator/backend`)**: A robust FastAPI service that powers the orchestrator, interfaces with external APIs, and manages the business logic for ticket assignments.

### 2. Enterprise Data Collector (EDC)

A dedicated data gathering and processing engine (`edc/`) that synchronizes domain knowledge.

* Connects to **ConnectWise**, **Confluence**, and **SharePoint**.
* Maintains active and historical ticket mirrors.
* Generates and stores **vector embeddings** for similarity search and AI context augmentation.
* Processes the organization's **Skill Matrix** for intelligent ticket routing.

### 3. Telemetry & Observability

A comprehensive AI activity logging and monitoring pipeline using OpenTelemetry.

* **OTLP Receiver (`otlp_receiver/`)**: Custom OpenTelemetry collector configured to securely ingest telemetry data from all microservices.
* **Database (`matrixsql`)**: Utilizes Microsoft SQL Server to store deep metrics, application logs, and AI request traces.

### 4. Shared SDK & Utilities

* **MCP SDK (`mcp_sdk/`)**: Reusable Python code and foundations for Model Context Protocol (MCP) servers.
* **TotoDev Core (`totodev/`)**: Specialized utility libraries for centralized configuration, secret management, and cross-application infrastructure.

---

## 🏗️ Architecture & Environments

The platform is designed to run securely across Development, Test (TEST01), and Production (PROD01) environments, with isolated access to client systems based on the deployment tier.

```mermaid
flowchart LR
  %% ==== Shared Services (defined first to influence placement) ====
  subgraph SHARED_ENV["Shared for TEST & PROD"]
    direction TB
    SP["SharePoint<br>(single instance)"]:::shared
    CONF["Confluence<br>(single instance)"]:::shared
    ENTRA["Microsoft Entra<br>(SSO)"]:::shared
  end

  %% ==== Environments & App Versions ====
  subgraph DEV_ENV["DEV (temporary)"]
    direction TB
    APP_DEV["App<br>DEV"]:::app

    subgraph DEV_STUBS["Stubs"]
      direction TB
      STUB_DATA["Stub Data"]:::stub
    end

    APP_DEV --> STUB_DATA

    DEV_NOTE(["DEV uses stubs by default;<br>may sometimes attach to TEST servers"]):::note
  end

  subgraph TEST_ENV["TEST"]
    direction TB
    APP_TEST["App<br>TEST"]:::app
    CW_TEST["ConnectWise<br>(CWWebApp_training)"]:::server_test
  end

  subgraph PROD_ENV["PROD"]
    direction TB
    APP_PROD["App<br>PROD"]:::app
    CW_PROD["ConnectWise<br>(PROD)"]:::server_prod
  end

  %% ==== Primary connections ====
  APP_TEST --> CW_TEST
  APP_PROD --> CW_PROD

  APP_TEST --> SP
  APP_TEST --> CONF
  APP_PROD --> SP
  APP_PROD --> CONF

  %% ==== SSO Authentication flows ====
  APP_TEST -.SSO.-> ENTRA
  APP_PROD -.SSO.-> ENTRA

  %% ==== DEV occasional links to TEST ====
  APP_DEV -.temporary connection.-> CW_TEST
  APP_DEV -.temporary.-> SP
  APP_DEV -.temporary.-> CONF
  APP_DEV -.temporary SSO.-> ENTRA

  %% ==== Styling ====
  classDef app fill:#d1e9ff,stroke:#388bff,stroke-width:2px,color:#0b3268;
  classDef server_test fill:#e7f5ff,stroke:#74c0fc,stroke-width:1.5px,color:#0b3268;
  classDef server_prod fill:#e6fcf5,stroke:#69db7c,stroke-width:1.5px,color:#0b3268;
  classDef shared fill:#fff4e6,stroke:#ffa94d,stroke-width:1.5px,color:#7a4b00;
  classDef stub fill:#f3f0ff,stroke:#9775fa,stroke-width:1.5px,stroke-dasharray:4 2,color:#3f1f7a;
  classDef note fill:#f8f9fa,stroke:#adb5bd,stroke-dasharray:3 3,color:#495057;
```

---

## 🚀 Getting Started

The platform runs as a constellation of microservices managed primarily by Docker Compose.

### Prerequisites

* Docker & Docker Compose
* Python 3.11+
* Node.js 18+

### Setup Guide

For comprehensive, step-by-step instructions covering configuration, database initialization, and running services locally, please refer to the **[Setup Guide](setup_guide.md)**.

### Quick Start (Docker)

1. Clone the repository.
2. Create your environment configuration file: `config._THIS_IS_<YOURNAME>_ENV_.sh`
3. Load the configuration: `source config._THIS_IS_<YOURNAME>_ENV_.sh`
4. Spin up the infrastructure:
   ```bash
   docker compose up -d
   ```

---

## 🛠️ Technology Stack

| Component               | Technologies                                                         |
| ----------------------- | -------------------------------------------------------------------- |
| **Frontend**      | React, Webpack                                                       |
| **Backend API**   | Python 3.11+, FastAPI, Uvicorn, Pydantic                             |
| **AI / LLM**      | LangChain, OpenAI (GPT-4o, GPT-4o-mini)                              |
| **Database**      | Microsoft SQL Server (2022), SQLite                                  |
| **Observability** | OpenTelemetry (OTLP), FastAPI Instrumentation                        |
| **Integrations**  | ConnectWise API, Microsoft Entra SSO, SharePoint API, Confluence API |
| **DevOps**        | Docker, Docker Compose, GitHub Actions                               |

---

## 🔐 Security & Configuration

Security and environment segregation are handled securely through dynamic configurations:

* **`config.py`**: Centralizes global defaults and logic for DEV, STAGE, and PROD.
* **Environment Scripts**: Secrets and sensitive endpoint URLs are loaded dynamically via `config._THIS_IS_<ENV>_ENV_.sh` files which are properly `.gitignore`d to prevent leakage.
* **SSO**: Application authentication leverages robust Microsoft Entra ID integration.

---

*Developed by  WPBrigade for Matrix Integration.*
