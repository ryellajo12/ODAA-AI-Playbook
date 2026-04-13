# 16. Security & Governance Guardrails

## 16.1 Security Architecture

```mermaid
graph LR
    subgraph L1["Layer 1 Â· Network"]
        N1["Private Endpoints"]
        N2["VNET Integration"]
        N3["NSG Rules"]
        N4["TLS 1.2+"]
    end

    subgraph L2["Layer 2 Â· Identity"]
        I1["Entra ID"]
        I2["Managed Identities"]
        I3["Azure Key Vault"]
        I4["Dedicated DB Users"]
    end

    subgraph L3["Layer 3 Â· Data"]
        D1["Read-Only AI Users"]
        D2["Oracle VPD Row-Level"]
        D3["Data Redaction PII"]
        D4["Separate AI Schemas"]
    end

    subgraph L4["Layer 4 Â· AI Governance"]
        G1["Content Safety"]
        G2["Prompt Guardrails"]
        G3["APIM Rate Limiting"]
        G4["Token Budgets"]
    end

    subgraph L5["Layer 5 Â· Audit"]
        A1["Oracle Unified Audit"]
        A2["DBTOOLS$MCP_LOG"]
        A3["Azure Monitor"]
        A4["Fabric Audit Logs"]
    end

    L1 --> L2 --> L3 --> L4 --> L5
```

## 16.2 Security Checklist

| # | Control | Required | Notes |
|---|---------|----------|-------|
| 1 | Dedicated read-only Oracle user per agent | âœ... Yes | Never use ADMIN/SYS |
| 2 | Private Endpoints for Oracle Database@Azure | âœ... Yes | No public IP |
| 3 | Entra ID auth for Azure services | âœ... Yes | Managed identities preferred |
| 4 | Key Vault for Oracle credentials | âœ... Yes | No plaintext secrets |
| 5 | System prompt restricts DDL/DML | âœ... Yes | Agent can only read by default |
| 6 | Column masking for PII | âš ï¸ Recommended | Data Redaction for sensitive columns |
| 7 | MCP audit logging enabled | âœ... Yes | Check `DBTOOLS$MCP_LOG` |
| 8 | Rate limiting via APIM | âš ï¸ Recommended | Prevents runaway agent queries |
| 9 | Azure AI Content Safety | âš ï¸ Recommended | Filters harmful inputs/outputs |
| 10 | Network segmentation (NSGs) | âœ... Yes | Agent services in separate subnet |
| 11 | Encryption at rest and in transit | âœ... Yes | TLS 1.2+ for all connections; Oracle TDE |

## 16.3 Key Principle

> **MCP does not bypass Oracle security â€" it operates inside it.** Every SQL statement executed via MCP runs under the connected Oracle user's privileges, subject to Oracle's standard authentication, authorization, auditing, and VPD policies.
