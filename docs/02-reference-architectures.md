# 8. Reference Architecture Patterns

Patterns are organized into three categories based on how Oracle data flows into the Microsoft AI ecosystem.
Platforms used are Microsoft Foundry, Copilot Studio, Power Apps, Logic apps for workflows 

---

## Category 1: Live Oracle Data (No Migration)

Agents query Oracle data directly running on Oracle Database@Azure at runtime. No data leaves Oracle.

| Pattern | AI Platform | How It Connects | Surfaces | Value Proposition |
|---------|------------|-----------------|----------|-------------------|
| **1A** | **Copilot Studio** | Gateway / Oracle as Knowledge / Oracle as Tool | Teams, Web, M365 | • Fastest time-to-value (hours)<br/>• No-code builder<br/>• Business users self-serve answers<br/>• Zero data movement |
| **1B** | **Copilot Studio** | Oracle MCP Server (hosted on Azure Functions / Azure Container Apps) + Copilot Studio MCP Tool  | Teams, Web, M365 | • Fastest time-to-value (hours)<br/>• No-code builder<br/>• Business users self-serve answers<br/>• Zero data movement<br/>• Better tool curation than generic Oracle connector |
| **1C** | **Copilot Studio** | Oracle ORDS API Endpoints + Custom Connector  | Teams, Web, M365 | • Fastest time-to-value (hours)<br/>• No-code builder<br/>• Business users self-serve answers<br/>• Zero data movement<br/>• Seamless native 26ai vector search on the DB |
| **1D** | **MS Foundry** | Agent Framework: Oracle MCP server (hosted on Azure Functions / Azure Container Apps) + ORDS APIs; Knowledge Base (Blob, SharePoint, Fabric Files); Oracle 23ai vectors | API, M365 Copilot, Agent Store | • Full model & tool control<br/>• Multi-agent orchestration<br/>• Production-grade custom AI apps<br/>• Live Oracle data, no migration<br/>• Publish to M365 + Agent Store |
| **1E** | **Oracle MCP** (developer) | SQLcl MCP in VS Code or hosted | VS Code, Foundry, Copilot Studio | • Natural language → SQL in minutes<br/>• Zero infrastructure to start<br/>• Schema discovery on demand<br/>• DBA task automation |
| **1F** | **Power Apps** | Gateway / Oracle Connector | Power Platform | • Modernize workflows without rebuilding<br/>• AI Builder for forms & predictions<br/>• Citizen developer friendly<br/>• Incremental AI adoption |
| **1G** | **Logic Apps** | Oracle DB Connector / ORDS REST calls | Workflow orchestration, enterprise integration | • Event-driven automation<br/>• 400+ enterprise connectors<br/>• No custom code needed<br/>• Orchestrate Oracle + SaaS + Azure |

---

### Pattern 1A: Copilot Studio + Oracle Connector (On-Prem Data Gateway)


```mermaid
graph TB
    subgraph Users[End Users]
        BU[Business Users<br/>Teams / Web / M365]
    end

    subgraph EntraID[Microsoft Entra ID / OAuth2]
        AUTH[SSO / MFA<br/>Conditional Access]
    end

    subgraph CS[Microsoft Copilot Studio]
        COP[Custom Copilot]
        C[Oracle Connector]
        K[Oracle as Knowledge<br/>Grounds on tables,<br/>views, data]
        T[Oracle as Tool<br/>Connector actions<br/>called during chat]
        subgraph OBS[Native Observability]
            AN[Native Analytics<br/>Copilot Studio "Analytics" tab]
            DV[Dataverse<br/>ConversationTranscript + related tables]
        end
    end

    subgraph PURV[Data Governance / Compliance Plane]
        PURVIEW[Microsoft Purview<br/>Data Map + Catalog]
    end

    subgraph GOV[Governance / Publishing Plane]
        A365[Agent 365 / Copilot Control System<br/>Approve / Publish / Deploy / Assign]
    end

    subgraph VNET[Azure VNET]
        subgraph GWSub[Gateway Subnet]
            GATEWAY[On-Premises Data Gateway<br/>Azure VM]
        end
        subgraph PESub[Private Endpoint Subnet]
            PE[Private Endpoint]
        end
    end

    subgraph ODA[Oracle Database@Azure]
        DB[(ADBS / Exadata etc<br/>No Public IP)]
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

    COP -.->|Submit / Publish request| A365
    A365 -.->|Approval / Assignment / Deployment| BU
    PURVIEW -.-> DB
```
Azure Relay is the service that the On-Premises Data Gateway uses to communicate with cloud services like Copilot Studio. This is already how the On-Premises Data Gateway works by default — you don't configure Azure Relay separately. It's built into the gateway installer.

Here's how it works:

The gateway VM makes an outbound HTTPS connection (port 443) to Azure Relay when it starts up.
This creates a persistent, secure tunnel — no inbound ports need to be opened on the gateway VM.
When Copilot Studio needs Oracle data, the request flows through this tunnel to the gateway, which then queries Oracle over port 1521 via the Private Endpoint.

---
### Pattern 1B: Copilot Studio + Oracle MCP Server

```mermaid
graph TB
    subgraph Users[End Users]
        BU[Business Users<br/>Teams / Web / M365]
    end

    subgraph EntraID[Microsoft Entra ID / OAuth2]
        AUTH[SSO / MFA<br/>Conditional Access]
    end

    subgraph CS[Microsoft Copilot Studio]
        COP[Custom Copilot]
        CC[Copilot Studio MCP Tool]
    end

    subgraph GOV[Governance / Publishing Plane]
        A365[Agent 365 / Copilot Control System<br/>Approve / Publish / Deploy / Assign]
    end

    subgraph PURV[Data Governance / Compliance Plane]
        PURVIEW[Microsoft Purview<br/>Data Map + Catalog]
    end

    subgraph AZ[Azure Integration Layer]
        MCP[Oracle MCP Server<br/>Azure Functions or Azure Container Apps]
        TOOLS[Purpose-built Oracle tools<br/>Search / Lookup / Summarize / Actions]
    end

    subgraph VNET[Azure VNET]
        subgraph PESub[Private Endpoint Subnet]
            PE[Private Endpoint]
        end
    end

    subgraph ODA[Oracle Database@Azure]
        DB[(ADBS / Exadata / EBS-backed Oracle DB<br/>No Public IP)]
    end

    BU -->|SSO| AUTH
    AUTH --> COP
    COP --> CC
    CC --> MCP
    MCP --> TOOLS
    TOOLS -->|Port 1521<br/>Private connectivity| PE
    PE --> DB

    COP -.->|Submit / Publish request| A365
    A365 -.->|Approval / Assignment / Deployment| BU
    PURVIEW -.-> DB
```
---
### Pattern 1C: Copilot Studio + ORDS API Endpoints (Pre-built Analytics / Vector Search)


```mermaid
graph TB
    subgraph Users[End Users]
        EU[Business Users / Analysts<br/>Teams / Web / M365]
    end

    subgraph EntraID[Microsoft Entra ID / OAuth2]
        AUTH[SSO / MFA<br/>Conditional Access]
    end

    subgraph CS[Microsoft Copilot Studio]
        COP[Custom Copilot]

        subgraph Actions[Connector Actions]
            CC1[Custom Connector's:<br/>ORDS REST APIs<br/>1. Vector Search 26ai RAG<br/>2. Pre-built Analytics]
        end
    end

    subgraph GOV[Governance / Publishing Plane]
        A365[Agent 365 / Copilot Control System<br/>Approve / Publish / Deploy / Assign]
    end

    subgraph PURV[Data Governance / Compliance Plane]
        PURVIEW[Microsoft Purview<br/>Data Map + Catalog]
    end

    subgraph AOAI[Azure OpenAI]
        EMB[Embedding API]
    end

    subgraph VNET[Azure VNET]
        subgraph ORDSSub[ORDS Subnet]
            ORDS[ORDS<br/>VNET-integrated<br/>App Service / Container Apps]
        end
        subgraph PESub[Private Endpoint Subnet]
            PE[Private Endpoint]
        end
        KV[Azure Key Vault]
        APIM[API Management<br/>OAuth2 validation<br/>Rate limiting]
    end

    subgraph ODA[Oracle Database@Azure]
        ORDS_EP[ORDS REST Endpoints]
        VEC[26ai Vector Engine<br/>Vector Search]
        DATA[("Oracle Data<br/>No Public IP")]
    end


    EU -->|SSO| AUTH
    AUTH --> COP

    COP --> CC1

    CC1 --> APIM

    APIM -->|OAuth2 token validation| ORDS

    ORDS -->|Port 1521<br/>Private Endpoint| PE
    PE --> ORDS_EP
    PE --> VEC

    ORDS_EP --> DATA
    VEC --> DATA
    VEC -.->|Embeddings| EMB

    COP -.->|Submit / Publish request| A365
    A365 -.->|Approval / Assignment / Deployment| EU

    PURVIEW -.-> DATA

```

---
### Pattern 1D: MS Foundry + Oracle MCP Server

```mermaid
graph TB
    subgraph Users["End Users"]
        EU["Developers / Business Users"]
    end

    subgraph EntraID["Microsoft Entra ID"]
        AUTH["SSO / MFA<br/>Conditional Access"]
        RBAC_E["RBAC:<br/>Foundry User /<br/>Foundry Contributor"]
    end

    subgraph Foundry["Microsoft Foundry"]
        AGENT["AI Agent"]
        LLM["LLM<br/>GPT-4.1 / o3 / o4-mini"]
        MCP["Oracle MCP Tool"]
    end

    subgraph VNET["Azure VNET"]
        subgraph FuncSub["Functions / Container Apps Subnet"]
            MCPHOST["Hosted MCP Server<br/>VNET-integrated"]
        end
        subgraph PESub["Private Endpoint Subnet"]
            PE["Private Endpoint"]
        end
        KV["Azure Key Vault<br/>Oracle credentials"]
    end

    subgraph ODA["Oracle Database@Azure"]
        SQL["SQL / PL-SQL Engine"]
        DATA[("Oracle Data<br/>No Public IP")]
    end

    subgraph Publish["Published To"]
        M365["M365 Copilot / Teams"]
        STORE["Agent Store"]
        API["API Endpoint"]
    end

    EU -->|SSO| AUTH
    AUTH --> AGENT
    RBAC_E --> AGENT
    AGENT <--> LLM
    AGENT --> MCP
    MCP --> MCPHOST
    MCPHOST -->|Managed Identity| KV
    MCPHOST -->|Port 1521<br/>Private Endpoint| PE
    PE --> SQL
    SQL --> DATA
    AGENT --> M365
    AGENT --> STORE
    AGENT --> API
```

---

### Pattern 1D-2: MS Foundry + Oracle ORDS API Endpoints (RAG / Vector Search)

```mermaid
graph TB
    subgraph Users["End Users"]
        EU["Analysts / App Users"]
    end

    subgraph EntraID["Microsoft Entra ID"]
        AUTH["SSO / MFA<br/>Conditional Access"]
        RBAC_E["RBAC:<br/>Foundry User /<br/>ORDS OAuth2 scope"]
    end

    subgraph Foundry["Microsoft Foundry"]
        AGENT["AI Agent"]
        LLM["LLM<br/>GPT-4.1 / o3 / o4-mini"]

        subgraph Tools["Agent Tools (OpenAPI)"]
            OPENAPI["ORDS REST APIs<br/>Pre-built Analytics"]
            VSEARCH["ORDS Vector Search<br/>Oracle 23ai RAG"]
        end
    end

    subgraph AOAI["Azure OpenAI"]
        EMB["Embedding API<br/>text-embedding-3-small"]
    end

    subgraph VNET["Azure VNET"]
        subgraph ORDSSub["ORDS Subnet"]
            ORDS["ORDS<br/>VNET-integrated<br/>App Service / Container Apps"]
        end
        subgraph PESub["Private Endpoint Subnet"]
            PE["Private Endpoint"]
        end
        KV["Azure Key Vault"]
        APIM["API Management<br/>OAuth2 validation<br/>Rate limiting"]
    end

    subgraph ODA["Oracle Database@Azure"]
        ORDS_EP["ORDS REST Endpoints"]
        VEC["23ai Vector Engine<br/>VECTOR_DISTANCE"]
        DATA[("Oracle Data<br/>No Public IP")]
    end

    subgraph Publish["Published To"]
        M365["M365 Copilot / Teams"]
        STORE["Agent Store"]
        API["API Endpoint"]
    end

    EU -->|SSO| AUTH
    AUTH --> AGENT
    RBAC_E --> AGENT
    AGENT <--> LLM
    AGENT --> OPENAPI
    AGENT --> VSEARCH
    OPENAPI --> APIM
    VSEARCH --> APIM
    APIM -->|OAuth2 token<br/>validation| ORDS
    ORDS -->|Private Endpoint<br/>Port 1521| PE
    PE --> ORDS_EP
    PE --> VEC
    VEC <-.->|Embeddings| EMB
    ORDS_EP --> DATA
    VEC --> DATA
    AGENT --> M365
    AGENT --> STORE
    AGENT --> API
```

---

### Pattern 1D-3: MS Foundry + Oracle MCP + Oracle ORDS APIs + Foundry IQ (Full Stack)

```mermaid
graph TB
    subgraph Users["End Users"]
        EU["All Personas"]
    end

    subgraph EntraID["Microsoft Entra ID"]
        AUTH["SSO / MFA<br/>Conditional Access"]
        RBAC_E["RBAC:<br/>Foundry User/Contributor<br/>ORDS OAuth2 scopes<br/>KB Reader"]
    end

    subgraph Foundry["Microsoft Foundry"]
        AGENT["AI Agent<br/>Orchestrator"]
        LLM["LLM<br/>GPT-4.1 / o3 / o4-mini"]

        subgraph Tools["Agent Tools"]
            MCP["Oracle MCP<br/>(Functions /<br/>Container Apps)"]
            OPENAPI["ORDS REST APIs"]
            VSEARCH["ORDS Vector Search<br/>Oracle 23ai RAG"]
        end

        subgraph KB["Foundry IQ + Knowledge"]
            FIQ["Foundry IQ<br/>Unstructured Processing"]
            BLOB["Azure Blob"]
            SP["SharePoint"]
            FF["Fabric Files"]
        end
    end

    subgraph AOAI["Azure OpenAI"]
        EMB["Embedding API"]
    end

    subgraph VNET["Azure VNET"]
        subgraph FuncSub["MCP Subnet"]
            MCPHOST["Hosted MCP<br/>VNET-integrated"]
        end
        subgraph ORDSSub["ORDS Subnet"]
            ORDS["ORDS<br/>VNET-integrated"]
        end
        subgraph PESub["Private Endpoint Subnet"]
            PE["Private Endpoint"]
        end
        KV["Key Vault"]
        APIM["API Management<br/>OAuth2 + Rate Limiting"]
    end

    subgraph ODA["Oracle Database@Azure"]
        SQL["SQL / PL-SQL"]
        ORDS_EP["ORDS Endpoints"]
        VEC["23ai Vector Engine"]
        DATA[("Oracle Data<br/>No Public IP")]
    end

    subgraph Publish["Published To"]
        M365["M365 Copilot / Teams"]
        STORE["Agent Store"]
        API["API Endpoint"]
    end

    EU -->|SSO| AUTH
    AUTH --> AGENT
    RBAC_E --> AGENT
    AGENT <--> LLM
    AGENT --> MCP
    AGENT --> OPENAPI
    AGENT --> VSEARCH
    AGENT --> FIQ
    FIQ --> BLOB
    FIQ --> SP
    FIQ --> FF
    MCP --> MCPHOST
    OPENAPI --> APIM
    VSEARCH --> APIM
    APIM --> ORDS
    MCPHOST -->|Private Endpoint| PE
    ORDS -->|Private Endpoint| PE
    PE --> SQL
    PE --> ORDS_EP
    PE --> VEC
    VEC <-.-> EMB
    SQL --> DATA
    ORDS_EP --> DATA
    VEC --> DATA
    AGENT --> M365
    AGENT --> STORE
    AGENT --> API
```

---

---

### Pattern 1F: Logic Apps + Oracle connector

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

Oracle data is replicated into Microsoft Fabric via Mirrored Database for analytics, cross-source joins, and AI grounding. Data Agents built on Mirrored Database can be published as MCP servers, deployed to Teams, or connected to Copilot Studio and MS Foundry via native connectors.

| Pattern | AI Platform | How It Connects | Surfaces | Value Proposition |
|---------|------------|-----------------|----------|-------------------|
| **2A** | **Mirrored Database + Data Agents** | Oracle → Fabric Mirroring → Mirrored Database → Data Agents → Published as MCP Server / Teams / Copilot Studio / Foundry | Teams, Copilot Studio, Foundry, MCP clients | • Natural language analytics on mirrored Oracle data<br/>• Data Agent as MCP server for any MCP client<br/>• Publish directly to Teams<br/>• Connect to Copilot Studio or Foundry via native connectors<br/>• Cross-source joins<br/>• Entra ID + private networking |
| **2B** | **Fabric Mirroring + Foundry** | Mirrored Database → Data Agents → Foundry agents (via native connector) | API, M365 Copilot, Agent Store | • AI agents grounded in curated analytics<br/>• Data Agent feeds Foundry as a tool<br/>• Best of Fabric + Foundry<br/>• Governed data layer<br/>• Publish insights to M365 Copilot |

---

### Pattern 2A: Mirrored Database + Data Agents

```mermaid
graph TB
    subgraph EntraID["Microsoft Entra ID"]
        AUTH["SSO / MFA<br/>Conditional Access"]
        RBAC_E["RBAC:<br/>Fabric Viewer/Contributor<br/>Workspace roles"]
    end

    subgraph VNET["Azure VNET"]
        subgraph PESub["Private Endpoint Subnet"]
            PE_ORA["Oracle<br/>Private Endpoint"]
        end
        subgraph FabricSub["Fabric Managed VNET"]
            FGWY["Fabric Managed<br/>Private Endpoint<br/>to Oracle"]
        end
    end

    subgraph ODA["Oracle Database@Azure"]
        DB[("ADBS / Exadata<br/>No Public IP")]
    end

    subgraph Fabric["Microsoft Fabric"]
        MIRROR["Fabric<br/>Mirroring"]
        MDB["Mirrored<br/>Database"]
        DA["Data Agent<br/>(on Mirrored DB)"]
    end

    subgraph Publish["Data Agent Published As"]
        MCP_PUB["MCP Server<br/>(any MCP client)"]
        TEAMS["Teams<br/>(direct publish)"]
        CS_CONN["Copilot Studio<br/>(native connector)"]
        FOUNDRY_CONN["MS Foundry<br/>(native connector)"]
    end

    subgraph Users["End Users"]
        BA["Business Analyst"]
        DEV["Developer<br/>(VS Code / MCP)"]
        BU["Business User<br/>(Teams)"]
    end

    AUTH --> DA
    RBAC_E --> DA
    DB -->|Private Endpoint| FGWY
    FGWY --> MIRROR
    MIRROR --> MDB
    MDB --> DA
    DA --> MCP_PUB
    DA --> TEAMS
    DA --> CS_CONN
    DA --> FOUNDRY_CONN
    MCP_PUB --> DEV
    TEAMS --> BU
    CS_CONN --> BU
    FOUNDRY_CONN --> BA
```

#### RBAC Model

| Layer | Role | Who Gets It | What It Controls |
|-------|------|-------------|------------------|
| **Entra ID** | Security Group: `Fabric-DataAgent-Users` | Analysts, business users | Who can query the Data Agent |
| **Entra ID** | Conditional Access | All users | MFA, device compliance |
| **Fabric Workspace** | Viewer | End users | Read-only access to mirrored data + Data Agent |
| **Fabric Workspace** | Contributor | Data engineers | Create/modify mirroring, Data Agents, semantic models |
| **Fabric Workspace** | Admin | Platform admin | Manage workspace security, capacity, private endpoints |
| **Copilot Studio** | Maker / User | Citizen devs / End users | Build copilots using Data Agent connector vs use them |
| **MS Foundry** | Foundry User / Contributor | End users / Developers | Use vs create agents connected to Data Agent |
| **Oracle DB** | Dedicated mirroring user | Fabric mirroring connection | `SELECT` on mirrored schemas only; read-only, no DDL/DML |

#### Private Networking

| # | Control | Details |
|---|---------|---------|
| 1 | Oracle Private Endpoint | No public IP on Oracle; Fabric connects via managed private endpoint |
| 2 | Fabric Managed VNET | Fabric workspace uses managed private endpoints for outbound connections to Oracle |
| 3 | Mirroring over private path | All data replication flows through private networking — no public internet |
| 4 | Entra ID auth for Fabric | All Fabric access authenticated via Entra ID SSO/MFA |
| 5 | Workspace-level security | Data Agent inherits Fabric workspace RBAC — controls who can query |
| 6 | Data Agent publishing security | MCP server / Teams / Copilot Studio access controlled by Entra ID groups |
| 7 | No Oracle credentials in agent | Mirrored Database is the source — Data Agent never connects to Oracle directly |

#### Data Agent Publishing Options

| Publish As | How | Use Case |
|-----------|-----|----------|
| **MCP Server** | Data Agent published as MCP endpoint — any MCP-compatible client can connect | Developers in VS Code, custom agents, multi-agent workflows |
| **Teams App** | Data Agent published directly into Teams | Business users ask questions in natural language in Teams chat |
| **Copilot Studio Connector** | Native connector in Copilot Studio connects to the Data Agent | Build no-code copilots grounded on mirrored Oracle analytics |
| **MS Foundry Tool** | Native connector in Foundry registers Data Agent as a tool | Foundry agents call Data Agent for analytics alongside other tools |

---

### Pattern 2B: Fabric Mirroring + Data Agents + Foundry

```mermaid
graph TB
    subgraph EntraID["Microsoft Entra ID"]
        AUTH["SSO / MFA<br/>Conditional Access"]
        RBAC_E["RBAC:<br/>Fabric roles +<br/>Foundry roles"]
    end

    subgraph VNET["Azure VNET"]
        subgraph PESub["Private Endpoint Subnet"]
            PE_ORA["Oracle<br/>Private Endpoint"]
        end
    end

    subgraph ODA["Oracle Database@Azure"]
        DB[("ADBS / Exadata<br/>No Public IP")]
    end

    subgraph Fabric["Microsoft Fabric"]
        MIRROR["Fabric<br/>Mirroring"]
        MDB["Mirrored<br/>Database"]
        DA["Data Agent<br/>(on Mirrored DB)"]
    end

    subgraph Foundry["Microsoft Foundry"]
        FAGENT["Foundry Agent"]
        LLM["LLM<br/>GPT-4.1 / o3"]
        DA_TOOL["Data Agent<br/>(native connector<br/>as Foundry tool)"]
        OTHER["Other Tools<br/>(MCP / ORDS / Functions)"]
    end

    subgraph Publish["Published To"]
        M365["M365 Copilot / Teams"]
        STORE["Agent Store"]
        API["API Endpoint"]
    end

    EU["End Users"] -->|SSO| AUTH
    AUTH --> FAGENT
    RBAC_E --> FAGENT
    DB -->|Private Endpoint| PE_ORA
    PE_ORA --> MIRROR
    MIRROR --> MDB
    MDB --> DA
    DA --> DA_TOOL
    FAGENT <--> LLM
    FAGENT --> DA_TOOL
    FAGENT --> OTHER
    FAGENT --> M365
    FAGENT --> STORE
    FAGENT --> API
```

#### RBAC Model

| Layer | Role | Who Gets It | What It Controls |
|-------|------|-------------|------------------|
| **Entra ID** | Security Group: `Foundry-Analytics-Users` | All agent users | Who can use the Foundry agent |
| **Fabric Workspace** | Viewer / Contributor | Data team | Access to mirrored data and Data Agent |
| **MS Foundry** | Foundry User / Contributor | End users / Developers | Use vs create agents |
| **Azure RBAC** | Key Vault Secrets User (if other tools used) | Managed Identity | Read credentials for MCP/ORDS |
| **Oracle DB** | Dedicated mirroring user | Fabric mirroring | `SELECT` on mirrored schemas only |

#### Private Networking

| # | Control | Details |
|---|---------|---------|
| 1 | Oracle Private Endpoint | No public IP; Fabric mirroring via managed PE |
| 2 | Fabric Managed VNET | Mirroring over private path |
| 3 | Data Agent → Foundry (native connector) | Internal Azure service-to-service connection — no public exposure |
| 4 | Other Foundry tools (MCP/ORDS) | VNET-integrated as per Category 1 patterns |
| 5 | Entra ID everywhere | SSO/MFA for Fabric, Foundry, and published surfaces |

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
