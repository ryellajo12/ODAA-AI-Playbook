# PART I — FIELD PLAYBOOK

---

## 1. How to Use This Playbook

This playbook has three layers:

| Layer | Sections | Use When |
|-------|----------|----------|
| **Field** | 1 – 7 | Customer meetings, executive briefings, partner enablement |
| **Architecture** | 8 – 16 | Solution architects are in the room; designing credible solutions |
| **Implementation** | 17 – 20 | Hands-on build phase; developer and partner workshops |


**Quick-start for common scenarios:**

| You need to… | Go to… |
|---------------|--------|
| Position AI on Oracle Database@Azure in 5 minutes | Section 3 + Section 4 |
| Help a customer decide which path | Section 5 (Decision Matrix) |
| Design an architecture | [Reference Patterns](02-reference-architectures.md) |
| Build something today | [Step-by-Step Guides](11-implementation-guides.md) |
| Implement RAG on Oracle 26ai vectors | [Vector Search (Pattern 2 — Foundry Agents)](04-path2-foundry-agents.md#2-ms-foundry--ords-endpoints-rag--vector-search) |
| Wire up MCP tools for agents | [Path 3 — Oracle MCP](05-path3-oracle-mcp.md) |

---

## 2. Customer Discovery Framework

> **Rule #1:** Open with discovery, not slides.

### 2.1 Discovery Questions

| # | Question | Why It Matters |
|---|----------|----------------|
| 1 | What problem are you solving with AI? (Q&A, automation, insights, apps) | Maps to the right path |
| 2 | Who is the primary user? (business, ops, developers, DBAs) | Determines Persona and toolchain |
| 3 | Do you require live Oracle data or can you work with analytical copies? | Governs data movement and Fabric scope |
| 4 | What Oracle database version and edition are you running on Oracle Database@Azure ? | Oracle 26ai enables native vector search and the common guidance is to use this version for AI capabilities |
| 5 | Is there an existing Microsoft 365 / Power Platform footprint? | Opens Copilot Studio and Power Apps paths |
| 6 | Do you have Microsoft Foundry or Azure OpenAI provisioned? | Qualification for Microsoft Foundry agent path |
| 7 | Are you building for a single use case or a platform play? | Pilot vs platform architecture |
| 8 | What are your data residency and compliance requirements? | Drives network topology and data flow |

### 2.2 Signal Interpretation

| Signal | What It Tells You | Lead With |
|--------|-------------------|-----------|
| "No data movement allowed" | Trust, governance, speed are critical | Copilot Studio gateway / MCP / ORDS with Microsoft Foundry|
| "Business users need answers" | Low-code copilots | Copilot Studio |
| "We want insights and trends" | Analytics + AI | Microsoft Fabric |
| "We're building an app / product" | Pro-dev, APIs, orchestration | Microsoft Foundry |
| "DBAs need automation" | Schema exploration, SQL generation | Oracle MCP Server |
| "We have Oracle 26ai" | Native vector search | Oracle 26ai + Azure OpenAI RAG |
| "We want semantic search" | RAG / vector similarity | Oracle 26ai + Azure OpenAI RAG for vector search |
| "We need multi-step workflows" | Agentic AI with tool orchestration | Microsoft Foundry + MCP |

---

## 3. Core Positioning Message

> **Oracle Database@Azure allows customers to build secure, enterprise-grade Agentic AI and RAG solutions on Oracle data using Microsoft AI — without forcing a single architecture or data movement pattern.**

### Key Selling Points

1. **Start with live Oracle data** — no ETL prerequisite
2. **Choose your toolchain** — low-code (Copilot Studio, Power Apps) or pro-code (Microsoft Foundry, MCP, SDKs)
3. **Evolve incrementally** — from Q&A → Agents → Analytics → AI Applications
4. **Enterprise-grade security** — Oracle Database@Azure network isolation + Entra ID + Oracle DB security
5. **11 proven patterns across 3 categories** — Live Data, Mirrored Data, and IQ layers; customers choose based on need
6. **Oracle 26ai native vectors** — RAG without a separate vector database

### Elevator Pitch (30 seconds)

*"Oracle Database@Azure gives your Oracle data a direct line into Microsoft's AI ecosystem. Customers can build copilots, agents, and RAG applications on live/unified Oracle data directly using Copilot Studio, Microsoft Foundry, Oracle MCP tools, Microsoft Fabric, Power Apps, and Oracle 26ai vector search — all without compromising security"*

---

## 4. AI Patterns on Oracle Database@Azure — Overview

All patterns fall into three categories based on how Oracle data flows into the AI ecosystem.

### Zero Data Movement � Live Oracle Data

Agents query Oracle Database@Azure directly at runtime. No data leaves Oracle.

| Pattern | AI Platform | How It Connects | Surfaces | Value Proposition |
|---------|------------|-----------------|----------|-------------------|
| **1** | **Copilot Studio** | Gateway / Oracle as Knowledge / Oracle as Tool | Teams, Web, M365 | • Fastest time-to-value (hours)<br/>• No-code builder<br/>• Business users self-serve answers<br/>• Zero data movement |
| **2** | **MS Foundry + MCP** | Oracle MCP Server on Functions / Container Apps (VNET-integrated) | API, M365 Copilot, Agent Store | • NL → SQL via MCP<br/>• Entra ID SSO/MFA + RBAC<br/>• Private Endpoints end-to-end<br/>• Simplest Foundry pattern |
| **3** | **MS Foundry + ORDS** | ORDS REST endpoints + Oracle 26ai vector search (RAG); APIM for OAuth2 | API, M365 Copilot, Agent Store | • Governed REST APIs, no raw SQL<br/>• Oracle 26ai RAG / vector search<br/>• APIM enforces Entra ID OAuth2<br/>• All traffic private |
| **4** | **MS Foundry + MCP + ORDS + Foundry IQ** | MCP + ORDS + 26ai vectors + Foundry IQ (Blob, SharePoint, Fabric Files) | API, M365 Copilot, Agent Store | • Complete: structured + unstructured + RAG<br/>• RBAC at every layer<br/>• Separate DB users per tool<br/>• Maximum AI value |
| **5** | **Oracle MCP** (developer) | SQLcl MCP in VS Code or hosted | VS Code, Foundry, Copilot Studio | • Natural language → SQL in minutes<br/>• Zero infrastructure to start<br/>• Schema discovery on demand<br/>• DBA task automation |
| **6** | **Power Apps** | Gateway / Oracle Connector | Power Platform | • Modernize workflows without rebuilding<br/>• AI Builder for forms & predictions<br/>• Citizen developer friendly<br/>• Incremental AI adoption |
| **7** | **Logic Apps** | Oracle DB Connector / ORDS REST calls | Workflow orchestration, enterprise integration | • Event-driven automation<br/>• 400+ enterprise connectors<br/>• No custom code needed<br/>• Orchestrate Oracle + SaaS + Azure |

### Managed Replication � Mirrored Analytics Data

Oracle data is replicated into Fabric Mirrored Database via managed private endpoints. Data Agents on Mirrored Database can be published as MCP servers, to Teams, or connected to Copilot Studio and MS Foundry via native connectors.

| Pattern | AI Platform | How It Connects | Surfaces | Value Proposition |
|---------|------------|-----------------|----------|-------------------|
| **Pattern 8** | **Mirrored Database + Data Agents** | Oracle → Fabric Mirroring (private PE) → Mirrored Database → Data Agents → Published as MCP Server / Teams / Copilot Studio / Foundry | Teams, Copilot Studio, Foundry, MCP clients | • NL analytics on mirrored Oracle data<br/>• Data Agent as MCP server for any client<br/>• Publish direct to Teams<br/>• Native connectors to Copilot Studio + Foundry<br/>• Entra ID + private networking end-to-end |
| **Pattern 9** | **Fabric Mirroring + Data Agents + Foundry** | Mirrored Database → Data Agent → Foundry agent (native connector as tool) | API, M365 Copilot, Agent Store | • Foundry agent uses Data Agent as a tool<br/>• Combine mirrored analytics with live MCP/ORDS<br/>• Best of Fabric + Foundry<br/>• Entra ID RBAC at every layer |

### AI Enrichment: IQ — Intelligent Data Processing

AI-powered intelligence layers that process, enrich, and surface insights from structured, unstructured, and work data.

| Pattern | AI Platform | What It Does | Surfaces | Value Proposition |
|---------|------------|--------------|----------|-------------------|
| **Pattern 10** | **Fabric IQ** | AI-powered analytics and insights over data in OneLake (mirrored Oracle + other sources) | Fabric, Data Agents | • Automated insight discovery<br/>• AI finds patterns humans miss<br/>• Multi-source data intelligence<br/>• Scales with Fabric capacity |
| **Pattern 11** | **Foundry IQ** | Unstructured data processing — ingests docs from Blob, SharePoint, Fabric Files to ground Foundry agents | Foundry, M365 Copilot | • Unlock PDFs, docs, emails<br/>• Combine unstructured + structured Oracle data<br/>• Single agent, full context<br/>• Enterprise-grade grounding |
| **Pattern 12** | **Work IQ** | AI-driven productivity insights across M365 work patterns connected to Oracle business data | M365, Copilot | • Bridge work signals + business data<br/>• Meeting, email, doc intelligence<br/>• Organizational productivity insights<br/>• Connected to Oracle context |
| **Pattern 13** | **Unified IQ** | All IQ layers combined — Fabric IQ + Foundry IQ + Work IQ feeding a single intelligent agent | Fabric, Foundry, M365 Copilot | • Complete organizational intelligence<br/>• Structured + unstructured + work signals<br/>• One agent, all context<br/>• Maximum AI value from Oracle investment |

> **See [Reference Architecture Patterns](02-reference-architectures.md) for detailed Mermaid diagrams of each pattern.**

---

## 5. Decision Matrix

### 5.1 Quick Decision Guide

| Customer Need | Lead With | Why |
|--------------|-----------|-----|
| Live data Q&A, no movement | **Pattern 1:** Copilot Studio | Direct gateway, no-code, fastest |
| Custom AI apps — SQL-first | **Pattern 2:** MS Foundry + MCP | Natural language → SQL, schema discovery |
| Custom AI apps — governed APIs + RAG | **Pattern 3:** MS Foundry + ORDS | Pre-built REST endpoints, 26ai vector search |
| Full stack agent (structured + unstructured) | **Pattern 4:** MS Foundry + MCP + ORDS + Foundry IQ | Complete agent across all data types |
| DBA / developer automation | **Pattern 5:** Oracle MCP | SQL generation, schema exploration |
| Business workflow modernization | **Pattern 6:** Power Apps | Low-code, incremental AI |
| Enterprise integration / event-driven | **Pattern 7:** Logic Apps | 400+ connectors, no custom code |
| Cross-source analytics | **Pattern 8:** Mirrored DB + Data Agents | Managed mirroring, NL queries, publish as MCP/Teams/Copilot Studio/Foundry |
| AI agents on analytics data | **Pattern 9:** Fabric + Data Agents + Foundry | Data Agent as Foundry tool + live Oracle tools |
| Automated data insights | **Pattern 10:** Fabric IQ | AI-powered pattern discovery |
| Unstructured + structured grounding | **Pattern 11:** Foundry IQ | PDFs, docs, emails + Oracle data |
| Productivity + business data | **Pattern 12:** Work IQ | M365 signals + Oracle context |
| Full organizational intelligence | **Pattern 13:** Unified IQ | All IQ layers in one agent |

### 5.2 Detailed Comparison

| Dimension | Pattern 1 Copilot Studio | Pattern 2 Foundry+MCP | Pattern 3 Foundry+ORDS | Pattern 4 Full Stack | Pattern 5 Oracle MCP | Pattern 6 Power Apps | Pattern 7 Logic Apps | Pattern 8 Mirrored DB | Pattern 13 Unified IQ |
|-----------|-------------------|----------------|-------------------|-----------------|---------------|---------------|---------------|----------------|---------------|
| **Skill level** | Low-code | Pro-dev | Pro-dev | Pro-dev | DBA/Dev | Low-code | Low-code | Data eng | Pro-dev |
| **Data movement** | None | None | None | None | None | None | None | Mirror | Mirror + IQ |
| **Real-time data** | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes | ⚠️ Near-RT | ⚠️ Near-RT |
| **Knowledge grounding** | ✅ Oracle as Knowledge | ⚠️ Schema context | ✅ Via ORDS + 26ai vectors | ✅ Foundry IQ + ORDS + 26ai | ⚠️ Schema context | ❌ No | ❌ No | ✅ Semantic model | ✅ All IQ layers |
| **Tool calling** | ✅ Connector | ✅ MCP | ✅ ORDS OpenAPI | ✅ MCP + ORDS | ✅ Native | ❌ No | ✅ Connectors | ⚠️ Via Foundry | ✅ Via Foundry |
| **Vector search** | ❌ No | ❌ No | ✅ Oracle 26ai | ✅ Oracle 26ai | ✅ Via SQL | ❌ No | ❌ No | ❌ No | ✅ Via Foundry |
| **Unstructured data** | ❌ No | ❌ No | ❌ No | ✅ Foundry IQ | ❌ No | ❌ No | ❌ No | ❌ No | ✅ All IQ |
| **RBAC** | Entra ID + DLP | Entra ID + Azure RBAC | Entra ID + APIM OAuth2 | All layers | DB users | Entra ID | Entra ID | Entra ID | Entra ID |
| **Private networking** | Gateway + PE | VNET + PE | VNET + PE + APIM | VNET + PE + APIM | PE | Gateway + PE | Connectors | Fabric | Fabric + VNET |
| **Cost model** | Per-message | Per-compute | Per-compute + APIM | Per-compute + APIM | Free (local) | Per-user | Per-execution | Fabric CU | Combined |

### 5.3 Combination Patterns (Most Common)

| Pattern | Combined | Use Case |
|---------|----------|----------|
| **SQL Agent + Live Data** | Pattern 2 + Pattern 5 | Foundry agent using MCP tools for live Oracle queries |
| **RAG Agent** | Pattern 3 | Foundry agent with ORDS + 26ai vector search for semantic answers |
| **Full Stack Agent** | Pattern 4 | MCP + ORDS + Foundry IQ — structured, unstructured, and RAG |
| **Analytics + Agent** | Pattern 8 + Pattern 3 | Mirrored data feeding Foundry agents with ORDS for insight delivery |
| **Business + Dev** | Pattern 1 + Pattern 2 | Copilot Studio for business; Foundry+MCP for dev |
| **Business Process AI** | Pattern 6 + Pattern 1 | Power Apps workflow with Copilot Studio Q&A |
| **Enterprise Automation** | Pattern 7 + Pattern 3 | Logic Apps orchestration triggering Foundry agents with ORDS |
| **Complete Intelligence** | Pattern 4 + Pattern 8 + Pattern 13 | Full stack agent + mirrored analytics + all IQ layers |

---

## 6. Objection Handling

| Objection | Response |
|-----------|----------|
| *"We can't move our Oracle data"* | You don't have to. Zero Data Movement patterns (1A–1E) all work on live Oracle data with zero data movement. Managed Replication uses managed Fabric mirroring only if analytics require it. |
| *"We're worried about security and governance"* | Oracle Database@Azure runs in Azure with full network isolation (Private Endpoints, VNETs). Oracle MCP operates inside Oracle DB security — it doesn't bypass it. All patterns support Entra ID. See [Security & Governance](10-security-governance.md) for full guardrails. |
| *"We don't know where to start"* | Start with a single-scenario pilot. Most customers begin with Copilot Studio (1A — 48-hour proof of value) or an MCP demo (1C — 2-hour setup in VS Code). |
| *"We already have a vector database"* | Oracle 26ai has native vector support — one fewer service to manage. But Microsoft Foundry agents can also call external vector DBs via tools. Your choice. |
| *"Is MCP production-ready?"* | MCP is an open standard (Anthropic-initiated, now broadly adopted). Oracle's SQLcl MCP server is GA. For production, host on Azure Functions with Entra ID auth and API Management. See [Path 3 — Oracle MCP](05-path3-oracle-mcp.md). |
| *"What about cost?"* | Start with free/low-cost paths: MCP local is free; Copilot Studio has per-message pricing; Azure OpenAI is pay-per-token. No upfront platform investment required. |
| *"We need multi-agent orchestration"* | Microsoft Foundry supports multi-agent patterns natively. Combine with MCP tools for Oracle access and 26ai vectors for RAG. See [Combined Patterns](09-combined-patterns.md). |

---

## 7. Field Motion & Engagement Model

### 7.1 Engagement Lifecycle

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  Discover │───►│   Map    │───►│  Pilot   │───►│  Prove   │───►│  Scale   │
│           │    │ to Path  │    │          │    │          │    │          │
│ Discovery │    │ Decision │    │ 2-4 week │    │ Metrics  │    │ Prod     │
│ questions │    │ matrix   │    │ POC      │    │ & ROI    │    │ rollout  │
└──────────┘    └──────────┘    └──────────┘    └──────────┘    └──────────┘
```

### 7.2 Pilot Playbook

| Pattern | Pilot Scope | Duration | Success Metric |
|---------|-------------|----------|----------------|
| 1A — Copilot Studio | 1 business Q&A scenario on live Oracle data | 1 – 2 days | Users get accurate answers without writing SQL |
| 1B — MS Foundry | 1 agent with 2-3 Oracle tools (MCP + ORDS) | 1 – 2 weeks | Agent completes a multi-step task end-to-end |
| 1C — Oracle MCP | Developer workspace with MCP in VS Code | 2 hours | Developer generates and runs SQL via natural language |
| 1D — Power Apps | 1 Oracle-connected workflow with AI Builder | 3 – 5 days | Business process automated with AI assistance |
| 1E — Logic Apps | 1 event-driven Oracle integration flow | 2 – 3 days | Automated Oracle workflow triggered by events |
| 2A — Mirrored DB | Mirror 1 Oracle schema into Fabric | 1 week | Cross-source dashboard with Data Agent queries |
| 3D — Unified IQ | Connect all IQ layers to a single agent | 2 weeks | Agent answers across structured, unstructured, and work data |
