# 16. Security & Governance Guardrails

## 16.1 Security Architecture

```mermaid
graph LR
    subgraph L1["Layer 1 ÃÂ· Network"]
        N1["Private Endpoints"]
        N2["VNET Integration"]
        N3["NSG Rules"]
        N4["TLS 1.2+"]
    end

    subgraph L2["Layer 2 ÃÂ· Identity"]
        I1["Entra ID"]
        I2["Managed Identities"]
        I3["Azure Key Vault"]
        I4["Dedicated DB Users"]
    end

    subgraph L3["Layer 3 ÃÂ· Data"]
        D1["Read-Only AI Users"]
        D2["Oracle VPD Row-Level"]
        D3["Data Redaction PII"]
        D4["Separate AI Schemas"]
    end

    subgraph L4["Layer 4 ÃÂ· AI Governance"]
        G1["Content Safety"]
        G2["Prompt Guardrails"]
        G3["APIM Rate Limiting"]
        G4["Token Budgets"]
    end

    subgraph L5["Layer 5 ÃÂ· Audit"]
        A1["Oracle Unified Audit"]
        A2["DBTOOLS$MCP_LOG"]
        A3["Azure Monitor"]
        A4["Fabric Audit Logs"]
    end

    L1 --> L2 --> L3 --> L4 --> L5
```

## 16.2 Security Checklist

| # | Control | Required | Notes |
|--|--|--|--|
| 1 | Dedicated read-only Oracle user per agent | --... Yes | Never use ADMIN/SYS |
| 2 | Private Endpoints for Oracle Database@Azure | --... Yes | No public IP |
| 3 | Entra ID auth for Azure services | --... Yes | Managed identities preferred |
| 4 | Key Vault for Oracle credentials | --... Yes | No plaintext secrets |
| 5 | System prompt restricts DDL/DML | --... Yes | Agent can only read by default |
| 6 | Column masking for PII | --Å¡Â Ã¯Â¸Â Recommended | Data Redaction for sensitive columns |
| 7 | MCP audit logging enabled | --... Yes | Check `DBTOOLS$MCP_LOG` |
| 8 | Rate limiting via APIM | --Å¡Â Ã¯Â¸Â Recommended | Prevents runaway agent queries |
| 9 | Azure AI Content Safety | --Å¡Â Ã¯Â¸Â Recommended | Filters harmful inputs/outputs |
| 10 | Network segmentation (NSGs) | --... Yes | Agent services in separate subnet |
| 11 | Encryption at rest and in transit | --... Yes | TLS 1.2+ for all connections; Oracle TDE |

## 16.3 Key Principle

> **MCP does not bypass Oracle security -- it operates inside it.** Every SQL statement executed via MCP runs under the connected Oracle user's privileges, subject to Oracle's standard authentication, authorization, auditing, and VPD policies.
