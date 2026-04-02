# 8. Reference Architecture Patterns

Patterns are organized into three categories based on how Oracle data flows into the Microsoft AI ecosystem.
Platforms used are Microsoft Foundry, Copilot Studio, Power Apps, Logic apps for workflows 

---

## Category 1: Live Oracle Data (No Migration)

Agents query Oracle data directly running on Oracle Database@Azure at runtime. No data leaves Oracle.

| Pattern | AI Platform | How It Connects | Surfaces | Value Proposition |
|---------|------------|-----------------|----------|-------------------|
| **1A** | **Copilot Studio** | Gateway / Oracle as Knowledge / Oracle as Tool | Teams, Web, M365 | • Fastest time-to-value (hours)<br/>• No-code builder<br/>• Business users self-serve answers<br/>• Zero data movement |
| **1B** | **MS Foundry** | Agent Framework: Oracle MCP server (hosted on Azure Functions / Azure Container Apps) + ORDS APIs; Knowledge Base (Blob, SharePoint, Fabric Files); Oracle 23ai vectors | API, M365 Copilot, Agent Store | • Full model & tool control<br/>• Multi-agent orchestration<br/>• Production-grade custom AI apps<br/>• Live Oracle data, no migration<br/>• Publish to M365 + Agent Store |
| **1C** | **Oracle MCP** (developer) | SQLcl MCP in VS Code or hosted | VS Code, Foundry, Copilot Studio | • Natural language → SQL in minutes<br/>• Zero infrastructure to start<br/>• Schema discovery on demand<br/>• DBA task automation |
| **1D** | **Power Apps** | Gateway / Oracle Connector | Power Platform | • Modernize workflows without rebuilding<br/>• AI Builder for forms & predictions<br/>• Citizen developer friendly<br/>• Incremental AI adoption |
| **1E** | **Logic Apps** | Oracle DB Connector / ORDS REST calls | Workflow orchestration, enterprise integration | • Event-driven automation<br/>• 400+ enterprise connectors<br/>• No custom code needed<br/>• Orchestrate Oracle + SaaS + Azure |

---

### Pattern 1A: Copilot Studio + Oracle Connector (On-Prem Data Gateway)

```mermaid
graph TB
    subgraph Users["End Users"]
        BU["Business Users<br/>Teams / Web / M365"]
    end

    subgraph EntraID["Microsoft Entra ID"]
        AUTH["SSO / MFA<br/>Conditional Access"]
    end

    subgraph CS["Microsoft Copilot Studio"]
        COP["Custom Copilot"]
        C["Oracle Connector"]
        K["Oracle as Knowledge<br/>Grounds on tables,<br/>views, data"]
        T["Oracle as Tool<br/>Connector actions<br/>called during chat"]
    end

    subgraph VNET["Azure VNET"]
        subgraph GWSub["Gateway Subnet"]
            GATEWAY["On-Premises<br/>Data Gateway<br/>(Azure VM)"]
        end
        subgraph PESub["Private Endpoint Subnet"]
            PE["Private Endpoint"]
        end
    end

    subgraph ODA["Oracle Database@Azure"]
        DB[("ADBS / Exadata etc<br/>No Public IP")]
    end

    BU -->|SSO| AUTH
    AUTH --> COP
    COP --> C
    C --> K
    C --> T
    K -->|Azure Relay<br/>HTTPS 443| GATEWAY
    T -->|Azure Relay<br/>HTTPS 443| GATEWAY
    GATEWAY -->|Port 1521<br/>Private Endpoint| PE
    PE --> DB
```
Azure Relay is the service that the On-Premises Data Gateway uses to communicate with cloud services like Copilot Studio. This is already how the On-Premises Data Gateway works by default — you don't configure Azure Relay separately. It's built into the gateway installer.

Here's how it works:

How the Gateway Communicates:
The gateway VM makes an outbound HTTPS connection (port 443) to Azure Relay when it starts up.
This creates a persistent, secure tunnel — no inbound ports need to be opened on the gateway VM.
When Copilot Studio needs Oracle data, the request flows through this tunnel to the gateway, which then queries Oracle over port 1521 via the Private Endpoint.

---

### Pattern 1B: MS Foundry Agent + MCP + ORDS + Knowledge Base

```mermaid
graph TB
    subgraph Foundry["Microsoft Foundry"]
        AGENT["AI Agent<br/>Orchestrator"]
        LLM["LLM Model<br/>GPT-4.1 / o3 / o4-mini"]

        subgraph Tools["Agent Tools"]
            MCP["Oracle MCP<br/>SQLcl Server<br/>(Functions / Container Apps)"]
            OPENAPI["ORDS OpenAPI<br/>REST Tools"]
            FUNC["Azure Functions<br/>Custom Logic"]
            VSEARCH["Vector Search<br/>ORDS Endpoint<br/>Oracle 23ai"]
        end

        subgraph KB["Knowledge Base"]
            BLOB["Azure Blob"]
            SP["SharePoint"]
            FF["Fabric Files"]
            FIQ["Foundry IQ<br/>(Unstructured)"]
        end
    end

    subgraph ODA["Oracle Database@Azure · ADBS"]
        SQL["SQL / PL-SQL Engine"]
        ORDS_EP["ORDS REST Endpoints"]
        VEC["23ai Vector Engine"]
        DATA[("Oracle Data")]
    end

    subgraph Publish["Published To"]
        M365["M365 Copilot / Teams"]
        STORE["Agent Store"]
        API["API Endpoint"]
    end

    AGENT <--> LLM
    AGENT --> MCP
    AGENT --> OPENAPI
    AGENT --> FUNC
    AGENT --> VSEARCH
    AGENT --> FIQ
    MCP --> SQL
    OPENAPI --> ORDS_EP
    VSEARCH --> VEC
    SQL --> DATA
    ORDS_EP --> DATA
    VEC --> DATA
    AGENT --> M365
    AGENT --> STORE
    AGENT --> API
```

---

### Pattern 1C: Oracle MCP → Multi-Client Agent Access

```mermaid
graph TB
    subgraph Clients["AI Agent Clients"]
        VSC["VS Code +<br/>GitHub Copilot"]
        FAG["Microsoft Foundry<br/>Agent"]
        CSA["Copilot Studio<br/>Agent"]
        CUSTOM["Custom App<br/>via HTTP"]
    end

    subgraph Hosting["Deployment Options"]
        LOCAL["Local MCP<br/>VS Code Extension<br/>Free · Instant"]
        HOSTED["Hosted MCP<br/>Azure Functions /<br/>Container Apps<br/>+ APIM + Entra ID"]
    end

    subgraph MCP_Server["Oracle MCP Server - SQLcl"]
        MCPS["MCP Protocol Layer<br/>Schema Discovery<br/>SQL Generation<br/>Query Execution"]
    end

    subgraph ODA["Oracle DB@Azure"]
        DB[("ADBS / Exadata<br/>Private Endpoint")]
    end

    VSC --> LOCAL
    FAG --> HOSTED
    CSA --> HOSTED
    CUSTOM --> HOSTED
    LOCAL --> MCPS
    HOSTED --> MCPS
    MCPS --> DB
```

---

### Pattern 1E: Logic Apps + Oracle

```mermaid
graph LR
    subgraph Triggers["Event Triggers"]
        SCHED["Schedule"]
        HTTP["HTTP Request"]
        EVENT["Event Grid"]
    end

    subgraph LA["Logic Apps"]
        FLOW["Workflow Engine<br/>400+ Connectors"]
        ORA_CONN["Oracle DB Connector"]
        ORDS_CALL["ORDS REST Call"]
    end

    subgraph ODA["Oracle DB@Azure"]
        DB[("ADBS / Exadata")]
    end

    subgraph Downstream["Downstream"]
        TEAMS["Teams Notification"]
        EMAIL["Email / Outlook"]
        SAAS["SaaS Apps"]
    end

    SCHED --> FLOW
    HTTP --> FLOW
    EVENT --> FLOW
    FLOW --> ORA_CONN
    FLOW --> ORDS_CALL
    ORA_CONN --> DB
    ORDS_CALL --> DB
    FLOW --> TEAMS
    FLOW --> EMAIL
    FLOW --> SAAS
```

---

## Category 2: Mirrored / Analytics Data

Oracle data is replicated into Microsoft Fabric for analytics, cross-source joins, and AI grounding.

| Pattern | AI Platform | How It Connects | Surfaces | Value Proposition |
|---------|------------|-----------------|----------|-------------------|
| **2A** | **Mirrored Database + Data Agents** | Oracle → Fabric Mirroring → Mirrored Database → Data Agents | Fabric, Foundry | • Natural language analytics<br/>• Cross-source joins (SQL Server, Dataverse, etc.)<br/>• Managed mirroring, no ETL pipelines<br/>• Governed semantic models |
| **2B** | **Fabric Mirroring + Foundry** | Mirrored Database → Semantic Model → grounds Foundry agents | API, M365 Copilot | • AI agents grounded in curated analytics<br/>• Best of Fabric + Foundry<br/>• Governed data layer<br/>• Publish insights to M365 Copilot |

---

### Pattern 2A: Mirrored Database + Data Agents

```mermaid
graph LR
    subgraph ODA["Oracle DB@Azure"]
        DB[("Oracle ADBS<br/>Source Tables")]
    end

    subgraph Fabric["Microsoft Fabric"]
        MIRROR["Fabric<br/>Mirroring"]
        MDB["Mirrored<br/>Database"]
        DA["Data<br/>Agents"]
    end

    subgraph Out["End Users"]
        BA["Business Analyst"]
        DS["Data Scientist"]
    end

    DB --> MIRROR
    MIRROR --> MDB
    MDB --> DA
    DA --> BA
    DA --> DS
```

---

### Pattern 2B: Fabric Mirroring + Foundry Agents

```mermaid
graph LR
    subgraph ODA["Oracle DB@Azure"]
        DB[("Oracle ADBS")]
    end

    subgraph Fabric["Microsoft Fabric"]
        MIRROR["Fabric<br/>Mirroring"]
        MDB["Mirrored<br/>Database"]
        SEM["Semantic<br/>Model"]
    end

    subgraph AI["Microsoft Foundry"]
        FAGENT["Foundry Agent<br/>Analytics Grounding"]
    end

    subgraph Publish["Published To"]
        M365["M365 Copilot"]
        API["API Endpoint"]
    end

    DB --> MIRROR
    MIRROR --> MDB
    MDB --> SEM
    SEM --> FAGENT
    FAGENT --> M365
    FAGENT --> API
```

---

## Category 3: IQ — Intelligent Data Processing

AI-powered intelligence layers that process, enrich, and surface insights from structured, unstructured, and work data.

| Pattern | AI Platform | What It Does | Surfaces | Value Proposition |
|---------|------------|--------------|----------|-------------------|
| **3A** | **Fabric IQ** | AI-powered analytics and insights over data in OneLake (mirrored Oracle + other sources) | Fabric, Data Agents | • Automated insight discovery<br/>• AI finds patterns humans miss<br/>• Multi-source data intelligence<br/>• Scales with Fabric capacity |
| **3B** | **Foundry IQ** | Unstructured data processing — ingests docs from Blob, SharePoint, Fabric Files to ground Foundry agents | Foundry, M365 Copilot | • Unlock PDFs, docs, emails<br/>• Combine unstructured + structured Oracle data<br/>• Single agent, full context<br/>• Enterprise-grade grounding |
| **3C** | **Work IQ** | AI-driven productivity insights across M365 work patterns connected to Oracle business data | M365, Copilot | • Bridge work signals + business data<br/>• Meeting, email, doc intelligence<br/>• Organizational productivity insights<br/>• Connected to Oracle context |
| **3D** | **Unified IQ** | All IQ layers combined — Fabric IQ + Foundry IQ + Work IQ feeding a single intelligent agent | Fabric, Foundry, M365 Copilot | • Complete organizational intelligence<br/>• Structured + unstructured + work signals<br/>• One agent, all context<br/>• Maximum AI value from Oracle investment |

---

### Pattern 3D: Unified IQ — All Layers Combined

```mermaid
graph TB
    subgraph DataSources["Data Sources"]
        ORA[("Oracle DB@Azure<br/>Structured Data")]
        BLOB["Azure Blob<br/>Documents"]
        SP["SharePoint<br/>Files & Sites"]
        M365D["M365<br/>Emails, Meetings, Docs"]
    end

    subgraph IQLayers["IQ Layers"]
        FIQ["Fabric IQ<br/>Analytics Intelligence"]
        FOIQ["Foundry IQ<br/>Unstructured Processing"]
        WIQ["Work IQ<br/>Productivity Signals"]
    end

    subgraph Agent["Unified Agent"]
        UA["Foundry Agent<br/>Full Context:<br/>Structured + Unstructured + Work"]
    end

    subgraph Publish["Published To"]
        COP["M365 Copilot"]
        TEAMS["Teams"]
        API["API"]
    end

    ORA --> FIQ
    ORA --> FOIQ
    BLOB --> FOIQ
    SP --> FOIQ
    M365D --> WIQ
    FIQ --> UA
    FOIQ --> UA
    WIQ --> UA
    UA --> COP
    UA --> TEAMS
    UA --> API
```
