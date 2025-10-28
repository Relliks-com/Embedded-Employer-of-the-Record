# Remote EOR Embedded Integration
Comprehensive technical documentation for the **Embedded Employer of Record (EOR) Integration** between **Acme Platform** and **Remote**, detailing architecture, design patterns, data flows, and operational strategies.

Presentation: https://docs.google.com/presentation/d/1UPYHBcPsrQIKu783rg9zeXUfeHO-d3QWJHylpHtFMJk/edit?usp=sharing
---

## 🗺️ Architecture Overview (C4 Summary Map)
```text
┌────────────────────────────────────────────────────────────────────────────┐
│                         REMOTE EOR EMBEDDED INTEGRATION                    │
└────────────────────────────────────────────────────────────────────────────┘
        │
        │  C4 Level 1 - System Context
        ▼
┌─────────────────────────────┐
│  ACME PLATFORM              │
│  (Customer Portal)          │
│   ↔ Remote EOR Platform     │
└─────────────────────────────┘
        │
        │  C4 Level 2 - Container Diagram
        ▼
┌────────────────────────────────────────────────────────────────┐
│  Web App │ API Gateway │ Integration Service │ DB │ Cache │ MQ  │
└────────────────────────────────────────────────────────────────┘
        │
        │  C4 Level 3 - Component Diagram
        ▼
┌────────────────────────────────────────────┐
│  Controllers │ Orchestrator │ Remote Client │
│  Repositories │ Cache │ Event Processor    │
└────────────────────────────────────────────┘
        │
        │  C4 Level 4 - Code Diagram
        ▼
┌──────────────────────────────┐
│  SagaOrchestrator.execute()  │
│  RemoteClient.create()       │
│  Repository.update()         │
└──────────────────────────────┘
        │
        │  Operational Views
        ▼
┌──────────────────────────────────────────────┐
│ Sequence | Data Flow | State Machine | Tests │
└──────────────────────────────────────────────┘
```

# Remote EOR Embedded Integration
Comprehensive technical documentation for the **Embedded Employer of Record (EOR) Integration** between **Acme Platform** and **Remote**, detailing architecture, design patterns, data flows, and operational strategies.

---

## 📘 Table of Contents

### 1. Introduction & Overview
- [Executive Summary](./01_Executive_Summary.md)
- [System Context (C4 Level 1)](./02_System_Context.md)

### 2. Architecture Design
- [Container Diagram (C4 Level 2)](./03_Container_Diagram.md)
- [Component Diagram (C4 Level 3)](./04_Component_Diagram.md)
- [Code Diagram (C4 Level 4)](./05_Code_Diagram.md)

### 3. System Behavior
- [Sequence Diagrams](./06_Sequence_Diagrams.md)
- [Data Flow Diagrams](./07_Data_Flow_Diagrams.md)
- [State Machine Diagrams](./08_State_Machine_Diagrams.md)

### 4. Infrastructure
- [Deployment Architecture](./09_Deployment_Architecture.md)
- [Security Architecture](./10_Security_Architecture.md)

### 5. Operations & Quality
- [Monitoring & Observability](./11_Monitoring_Observability.md)
- [Testing Strategy](./12_Testing_Strategy.md)

### 6. Reference
- [Appendix / Glossary](./13_Appendix.md)

---

## 🧩 System Summary
- **Primary Goal:** Embed global hiring via Remote EOR directly within Acme’s user experience  
- **Core Patterns:** Event-driven architecture, Saga orchestration, and bi-directional synchronization  
- **Key Technologies:**  
  - Backend: Node.js / FastAPI / .NET  
  - Infra: AWS ECS Fargate, SQS, RDS PostgreSQL, Redis  
  - Observability: DataDog, CloudWatch, Grafana  
  - Security: OAuth2, HMAC webhooks, IAM, KMS encryption

---

## 🛠️ Contributing
To update this documentation:
1. Clone the repository or download docs.
2. Edit relevant `.md` files.
3. Validate Markdown rendering (e.g., in VS Code or GitHub preview).
4. Commit with a descriptive message (e.g., `docs: update sequence diagrams`).

---

## 📄 License
© 2025 Remote Integration Architecture — Internal Use Only.

---
