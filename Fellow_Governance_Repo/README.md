# Fellow Governance 🛡️🤖
> SaaS Platform for AI Governance, Geometric Interpretability, and Adversarial Defense  
> *Translating technical requirements into auditable legal evidence and executable policies*

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.104+-green.svg)](https://fastapi.tiangolo.com)
[![Next.js 14](https://img.shields.io/badge/Next.js-14-black.svg)](https://nextjs.org)
[![OPA](https://img.shields.io/badge/Policy-OPA/Rego-00ACD7.svg)](https://openpolicyagent.org)

---

## 🎯 Mission
Empower **AI governance leaders** and **legal professionals** to respond to AI framework violation notices (NIST AI RMF, EU AI Act, PL 2338/2023, LGPD), transforming complex technical requirements into:
- ✅ Auditable legal evidence
- ✅ Executable Rego policies via OPA
- ✅ Immutable audit trails (SHA-256 hash chain)
- ✅ Adversarial defense generation in ≤300 seconds

### 👥 Target Audience
| Profile | Primary Use Case |
|---------|------------------|
| ⚖️ Lawyer | Defense Mode: upload notice → 3-line thesis + forensic ZIP |
| 🛡️ AI Governance Lead | Risk dashboard, OPA blocking, MSB-21 tracking |
| 📋 Auditor | Immutable trails, forensic reports, evidence ↔ controls |
| 💻 Security/DevOps | Rego deployment, drift monitoring, CI/CD integration |

---

## ⚡ The Problem We Solve
| ❌ Without Fellow Governance | ✅ With Fellow Governance |
|------------------------------|---------------------------|
| Speed over compliance → Missing AIBOM/provenance → Regulatory fines | OPA blocks non-compliant deployments → Enforcement **before** violations |
| Black-box AI decisions → Unauditable risk | MIG relevance vectors prove decision drivers mathematically |
| Reactive defense after notices | Forensic ZIP + regressive Rego generated in ≤300s |

---

## 🏗️ System Architecture
```mermaid
flowchart TD
    Client["Frontend (Next.js 14)\nEvidenceGraph • PolicyViewer"]
    API["API Gateway / FastAPI"]
    SEK["System Execution Kernel\n(Orchestration + StateEngine)"]
    
    subgraph Core["Backend Logic"]
        Agents["22 Specialized Agents\n(A01-A18, D01-D04)"]
        OPA["Open Policy Agent\n(Rego / Gatekeeper)"]
        MIG["MIG Engine\n(pgvector / Geometry)"]
    end

    subgraph Infra["Infrastructure"]
        DB[(PostgreSQL + pgvector)]
        Redis[(Redis Cache)]
        Storage[(S3/GCS Evidence)]
    end

    Client <-->|HTTPS/WSS| API
    API --> SEK
    SEK -->|Dispatch| Agents
    SEK -->|Enforce| OPA
    OPA -.->|Eval| SEK
    Agents -->|Vectorize| MIG
    Agents -->|Write| DB
    SEK -->|Audit| DB
    
    classDef core fill:#2d3748,stroke:#4a5568,stroke-width:2px,color:#fff;
    classDef infra fill:#718096,stroke:#a0aec0,stroke-width:2px,color:#fff;
    class SEK,Agents,OPA,MIG core;
    class DB,Redis,Storage infra;
```

### 🔑 Critical Components
| Component | Purpose |
|-----------|---------|
| **SEK** | Deterministic agent orchestration; LLM proposes, Kernel executes |
| **MIG** | Geometric interpretability: relevance vectors, configurable dims (256–1024) |
| **PBSAI** | 21 MSB-21 controls mapped to 12 governance domains |
| **PCG** | 3-stage control plane: Admissibility → Binding Validation → Execution |
| **AEC** | Runtime governance: proposal/validation hashes chained in `audit_trails` |
| **Defense** | Pipeline ≤300s with per-tenant semaphore & degraded fallback |

---

## 📂 Repository Structure
```text
Fellow_Reg/
├── README.md              # Project entry point
├── docker-compose.yml     # Local infra (Postgres, Redis, OPA)
├── backend/               # FastAPI + SEK Core
│   ├── app/               # Source code (agents, api, core, db, services)
│   └── pyproject.toml     # Dependencies
├── frontend/              # Next.js 14 + React 18
├── infrastructure/        # IaC (Terraform, K8s manifests)
├── policies/              # OPA Rego bundles (MSB-21, NIST, PL 2338)
├── docs/                  # Technical Deep Dives
│   ├── architecture.md    # Full System Design Document (SDD)
│   ├── threat-model.md    # STRIDE analysis & mitigations
│   ├── perf-benchmarks.md # k6 scripts & SLO targets
│   └── runbooks/          # SRE procedures (RB-001 to RB-004)
├── planning/              # Roadmaps and specs
├── prompts/               # Agent prompt engineering
└── examples/              # Demo payloads
```

---

## 🚀 Quick Start
```bash
# 1. Infrastructure
cd infrastructure && docker-compose up -d postgres redis opa

# 2. Backend
cd backend && poetry install && cp .env.example .env
poetry run uvicorn app.main:app --reload

# 3. Frontend
cd frontend && pnpm install && pnpm dev  # Access: http://localhost:3000
```

---

## 📚 Documentation
| Document | Purpose |
|----------|---------|
| [🏛️ Architecture (SDD)](docs/architecture.md) | Complete system design, contracts, invariants & workflows |
| [🛡️ Threat Model](docs/threat-model.md) | STRIDE analysis, control coverage & review cadence |
| [📖 Runbooks](docs/runbooks/) | RB-001 to RB-004: OPA, hash chain, defense timeout & tenant DoS |
| [📊 Benchmarks](docs/perf-benchmarks.md) | k6 scripts, SLO thresholds (p95/p99) & bottleneck mitigation |

---

**Developed by Fabio Everton 🇧🇷** • [LinkedIn](https://www.linkedin.com/in/fabio-everton-3b62b1129/) • [GitHub](https://github.com/FABIOEVERTON/Fellow_Reg)