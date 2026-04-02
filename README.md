# Oracle Database@Azure — AI Adoption Playbook

**Version:** 2.1 · **Date:** April 2026

**Audience:** Field sellers, CSA/SSP, Solution architects

**Purpose:** A concrete, end-to-end guide to building AI agents,RAG solutions and agentic AI solutions on Oracle Database@Azure using every available integration path in the Microsoft AI ecosystem.

---

## Repository Structure

This playbook is organized into modular documents under the [`docs/`](docs/) folder for easier navigation and maintenance.

## Table of Contents

| # | Section | File | Audience |
|---|---------|------|----------|
| **PART I** | **FIELD PLAYBOOK** | | |
| 1 | How to Use This Playbook | [docs/01-field-playbook.md](docs/01-field-playbook.md) | All |
| 2 | Customer Discovery Framework | [docs/01-field-playbook.md](docs/01-field-playbook.md) | Field / Sales |
| 3 | Core Positioning Message | [docs/01-field-playbook.md](docs/01-field-playbook.md) | Field / Sales |
| 4 | AI Patterns on Oracle Database@Azure — Overview | [docs/01-field-playbook.md](docs/01-field-playbook.md) | Field / Sales |
| 5 | Decision Matrix | [docs/01-field-playbook.md](docs/01-field-playbook.md) | All |
| 6 | Objection Handling | [docs/01-field-playbook.md](docs/01-field-playbook.md) | Field / Sales |
| 7 | Field Motion & Engagement Model | [docs/01-field-playbook.md](docs/01-field-playbook.md) | Field / Sales |
| **PART II** | **ARCHITECTURE & IMPLEMENTATION** | | |
| 8 | Reference Architecture Patterns | [docs/02-reference-architectures.md](docs/02-reference-architectures.md) | Architects |
| 9 | Path 1 — Copilot Studio + Oracle | [docs/03-path1-copilot-studio.md](docs/03-path1-copilot-studio.md) | Architects / Devs |
| 10 | Path 2 — Microsoft Foundry Agents | [docs/04-path2-foundry-agents.md](docs/04-path2-foundry-agents.md) | Architects / Devs |
| 11 | Path 3 — Oracle MCP Server | [docs/05-path3-oracle-mcp.md](docs/05-path3-oracle-mcp.md) | Architects / Devs |
| 12 | Path 4 — Microsoft Fabric + Data Agents | [docs/06-path4-fabric-data-agents.md](docs/06-path4-fabric-data-agents.md) | Architects / Devs |
| 13 | Path 5 — Power Apps + Power Automate AI | [docs/07-path5-power-apps.md](docs/07-path5-power-apps.md) | Architects / Devs |
| 14 | Path 6 — Oracle 23ai Vector Search + RAG | [docs/08-path6-vector-search-rag.md](docs/08-path6-vector-search-rag.md) | Architects / Devs |
| 15 | Combined Patterns — Multi-Path | [docs/09-combined-patterns.md](docs/09-combined-patterns.md) | Architects |
| 16 | Security & Governance Guardrails | [docs/10-security-governance.md](docs/10-security-governance.md) | All |
| 17 | Step-by-Step Implementation Guides | [docs/11-implementation-guides.md](docs/11-implementation-guides.md) | Devs / Partners |
| 18 | Oracle ORDS as an AI Tool Layer | [docs/12-ords-ai-tool-layer.md](docs/12-ords-ai-tool-layer.md) | Architects / Devs |
| 19 | Monitoring, Observability & Cost | [docs/13-monitoring-cost.md](docs/13-monitoring-cost.md) | Architects / Ops |
| 20 | Appendix — Resources & References | [docs/14-appendix.md](docs/14-appendix.md) | All |

---

## Quick Start

| You need to… | Go to… |
|---------------|--------|
| Position AI on OD@A in 5 minutes | [Field Playbook — Sections 3 & 4](docs/01-field-playbook.md#3-core-positioning-message) |
| Help a customer decide which path | [Field Playbook — Decision Matrix](docs/01-field-playbook.md#5-decision-matrix) |
| Design an architecture | [Reference Architecture Patterns](docs/02-reference-architectures.md) |
| Build something today | [Step-by-Step Implementation Guides](docs/11-implementation-guides.md) |
| Implement RAG on Oracle 23ai vectors | [Path 6 — Vector Search + RAG](docs/08-path6-vector-search-rag.md) |
| Wire up MCP tools for agents | [Path 3 — Oracle MCP Server](docs/05-path3-oracle-mcp.md) |
| Review security guardrails | [Security & Governance](docs/10-security-governance.md) |

---

## 13 Patterns across 3 Categories

### Category 1: Live Oracle Data (No Migration)

| Pattern | AI Platform | Surfaces | Value Proposition |
|---------|------------|----------|-------------------|
| **1A** | [Copilot Studio](docs/03-path1-copilot-studio.md) | Teams, Web, M365 | • Fastest time-to-value (hours)<br/>• No-code builder<br/>• Business users self-serve answers<br/>• Zero data movement |
| **1B** | [MS Foundry + MCP](docs/04-path2-foundry-agents.md) | API, M365 Copilot, Agent Store | • NL → SQL via MCP<br/>• Entra ID SSO/MFA + RBAC<br/>• Private Endpoints end-to-end<br/>• Simplest Foundry pattern |
| **1B-2** | [MS Foundry + ORDS](docs/04-path2-foundry-agents.md) | API, M365 Copilot, Agent Store | • Governed REST APIs, no raw SQL<br/>• Oracle 23ai RAG / vector search<br/>• APIM enforces Entra ID OAuth2<br/>• All traffic private |
| **1B-3** | [MS Foundry + MCP + ORDS + Foundry IQ](docs/04-path2-foundry-agents.md) | API, M365 Copilot, Agent Store | • Complete: structured + unstructured + RAG<br/>• RBAC at every layer<br/>• Separate DB users per tool<br/>• Maximum AI value |
| **1C** | [Oracle MCP](docs/05-path3-oracle-mcp.md) | VS Code, Foundry, Copilot Studio | • Natural language → SQL in minutes<br/>• Zero infrastructure to start<br/>• Schema discovery on demand<br/>• DBA task automation |
| **1D** | [Power Apps](docs/07-path5-power-apps.md) | Power Platform | • Modernize workflows without rebuilding<br/>• AI Builder for forms & predictions<br/>• Citizen developer friendly<br/>• Incremental AI adoption |
| **1E** | Logic Apps | Workflow orchestration | • Event-driven automation<br/>• 400+ enterprise connectors<br/>• No custom code needed<br/>• Orchestrate Oracle + SaaS + Azure |

### Category 2: Mirrored / Analytics Data

| Pattern | AI Platform | Surfaces | Value Proposition |
|---------|------------|----------|-------------------|
| **2A** | [Mirrored Database + Data Agents](docs/06-path4-fabric-data-agents.md) | Teams, Copilot Studio, Foundry, MCP clients | • NL analytics on mirrored Oracle data<br/>• Data Agent as MCP server<br/>• Publish to Teams / Copilot Studio / Foundry<br/>• Entra ID + private networking |
| **2B** | [Fabric + Data Agents + Foundry](docs/06-path4-fabric-data-agents.md) | API, M365 Copilot, Agent Store | • Data Agent as Foundry tool<br/>• Combine mirrored + live Oracle<br/>• Entra ID RBAC at every layer |

### Category 3: IQ — Intelligent Data Processing

| Pattern | AI Platform | Surfaces | Value Proposition |
|---------|------------|----------|-------------------|
| **3A** | Fabric IQ | Fabric, Data Agents | • Automated insight discovery<br/>• AI finds patterns humans miss<br/>• Multi-source data intelligence |
| **3B** | Foundry IQ | Foundry, M365 Copilot | • Unlock PDFs, docs, emails<br/>• Combine unstructured + structured Oracle data<br/>• Enterprise-grade grounding |
| **3C** | Work IQ | M365, Copilot | • Bridge work signals + business data<br/>• Meeting, email, doc intelligence<br/>• Connected to Oracle context |
| **3D** | Unified IQ | Fabric, Foundry, M365 Copilot | • Complete organizational intelligence<br/>• Structured + unstructured + work signals<br/>• One agent, all context |

---

## Core Positioning

> **Oracle Database@Azure allows customers to build secure, enterprise-grade Agentic AI and RAG solutions on Oracle data using Microsoft AI — without forcing a single architecture or data movement pattern.**

---

## Version History

| Version | Date | Change |
|---------|------|--------|
| 1.0 | 2025 | Initial field playbook (4 paths) |
| 2.0 | February 2026 | Expanded to 6 paths; added Oracle 23ai vector search; added step-by-step implementation guides; added ORDS as AI tool layer; added monitoring and cost sections; added security checklist |
| 2.1 | April 2026 | Reorganized into 3 categories (Live Data, Mirrored, IQ) with 11 patterns; added Logic Apps, Foundry IQ, Fabric IQ, Work IQ, Unified IQ; split into modular `docs/` structure |

---

*This playbook is maintained by the OD@A AI Solutions team. For contributions, corrections, or customer-specific architecture reviews, contact the authors.*
