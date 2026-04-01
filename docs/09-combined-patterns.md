# 15. Combined Patterns — Multi-Path Architectures

Real-world deployments often combine multiple paths. Here are the most common patterns:

## 15.1 Pattern: Microsoft Foundry Agent + MCP + Vector Search (Full Agentic RAG)

```mermaid
graph TB
    subgraph Agent["Microsoft Foundry Agent"]
        SYS["System Prompt:<br/>You are an Oracle data analyst..."]
        subgraph AgentTools["Tools"]
            MCP["Oracle MCP - SQLcl<br/>Schema exploration, custom SQL"]
            ORDS["ORDS OpenAPI<br/>Pre-built analytics endpoints"]
            VS["Vector Search - ORDS<br/>Semantic RAG on 23ai vectors"]
            AF["Azure Functions<br/>Custom business logic"]
        end
        subgraph Knowledge["Knowledge"]
            SCHEMA["Schema documentation"]
            GLOSS["Business glossary"]
        end
    end

    subgraph ODA["Oracle Database@Azure"]
        SQL["SQL / PL-SQL"]
        REST["ORDS REST Endpoints"]
        VEC["23ai Vector Engine"]
    end

    MCP --> SQL
    ORDS --> REST
    VS --> VEC
    AF --> SQL
```

**When to use:** Most comprehensive pattern for teams that need SQL access, pre-built analytics, AND semantic search.

## 15.2 Pattern: Copilot Studio (Business) + Microsoft Foundry (Technical)

Deploy separate experiences for different audiences:

| Audience | Tool | Data Access |
|----------|------|-------------|
| Business users | Copilot Studio | Gateway → Oracle (simple queries) |
| Data analysts | Fabric Data Agent | Mirrored Oracle data in OneLake |
| Developers | Microsoft Foundry Agent + MCP | Full SQL + vector search |
| DBAs | VS Code + MCP | Schema management, automation |

## 15.3 Pattern: ETL + RAG Hybrid

```mermaid
graph LR
    ODA[("Oracle DB@Azure")] -->|Live| MCP_ORDS["MCP / ORDS"] --> AGENT["Microsoft Foundry Agent<br/>Real-time Q&A"]
    ODA -->|Mirror| FABRIC["Fabric"] --> LAKE["OneLake"] --> EMB["Embeddings"] --> SEARCH["AI Search"] --> RAG["RAG"]
```

**When to use:** Some questions need real-time data; others benefit from pre-computed embeddings on historical data.
