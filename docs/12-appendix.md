# 20. Appendix -- Resources & References

## 20.1 Official Documentation

| Resource | URL |
|--|--|
| Oracle Database@Azure | https://docs.oracle.com/en-us/iaas/Content/multicloud/oaa.htm |
| Oracle MCP Server (SQLcl) | https://docs.oracle.com/en/database/oracle/sql-developer-command-line/ |
| Oracle 26ai Vector Search | https://docs.oracle.com/en/database/oracle/oracle-database/23/vecse/ |
| Oracle ORDS | https://docs.oracle.com/en/database/oracle/oracle-rest-data-services/ |
| Microsoft Foundry | https://learn.microsoft.com/en-us/azure/ai-studio/ |
| Copilot Studio | https://learn.microsoft.com/en-us/microsoft-copilot-studio/ |
| Microsoft Fabric | https://learn.microsoft.com/en-us/fabric/ |
| Power Platform Oracle Connector | https://learn.microsoft.com/en-us/connectors/oracle/ |
| Azure Functions MCP Hosting | https://learn.microsoft.com/en-us/azure/azure-functions/ |
| Model Context Protocol (MCP) | https://modelcontextprotocol.io/ |

## 20.2 OpenAPI Specifications (Include with Agent Setup)

| API | File | Auth |
|--|--|--|
| Promotion Insights | `openapi/promotion-insights-api.json` | None |
| Clinical Vector Search | `openapi/clinical-vector-search-api.json` | Basic Auth |

## 20.3 Configuration Files (Ready to Use)

| File | Purpose |
|--|--|
| `mcp-config/oracle-mcp-config.json` | MCP server configuration for Oracle ADBS |
| `mcp-config/agent-tools.json` | Function tool definitions for Microsoft Foundry Agents |
| `mcp-config/agent-system-prompt.md` | System prompt template for Oracle analytics agent |
| `mcp-config/FOUNDRY-AGENT-SETUP.md` | Step-by-step Microsoft Foundry agent creation guide |

## 20.4 Architecture Decision Record Template

Use this template when documenting a customer's chosen path:

```markdown
# ADR: AI Agent Architecture for [Customer Name]

## Context
- Oracle DB version: [19c / 26ai]
- Oracle Database@Azure deployment: [ADBS / Exadata]
- Primary use case: [Q&A / Analytics / Automation / RAG]
- User persona: [Business / Developer / DBA]
- Data movement: [Allowed / Not allowed]

## Decision
Selected Path(s): [1-6]
Rationale: [Why this path fits]

## Architecture
[Reference pattern from Section 8]

## Security Controls
[Checklist from Section 16.2]

## Estimated Timeline
- Pilot: [X weeks]
- Production: [X months]

## Cost Estimate
[From Section 19.2]
```

## 20.5 Glossary

| Term | Definition |
|--|--|
| **Oracle Database@Azure** | Oracle Database@Azure -- Oracle databases running natively on Azure infrastructure |
| **ADBS** | Autonomous Database Serverless -- Oracle's fully managed database service |
| **MCP** | Model Context Protocol -- open standard for AI agent tool integration |
| **ORDS** | Oracle REST Data Services -- REST API layer for Oracle Database |
| **RAG** | Retrieval-Augmented Generation -- grounding LLM responses with retrieved data |
| **Vector Search** | Semantic similarity search using embedding vectors |
| **Microsoft Foundry** | Microsoft Foundry -- Microsoft's platform for building AI applications |
| **Copilot Studio** | Microsoft's low-code platform for building copilots |
| **Fabric** | Microsoft Fabric -- unified analytics platform with OneLake |
| **VPD** | Virtual Private Database -- Oracle's row-level security feature |
| **Entra ID** | Microsoft's identity platform (formerly Azure AD) |
| **APIM** | Azure API Management -- API gateway service |

--

## Version History

| Version | Date | Change |
|--|--|--|
| 1.0 | 2025 | Initial field playbook (4 paths) |
| 2.0 | February 2026 | Expanded to 6 paths; added Oracle 26ai vector search; added step-by-step implementation guides; added ORDS as AI tool layer; added monitoring and cost sections; added security checklist |
| 2.1 | April 2026 | Split monolithic README into modular docs/ structure for easier navigation and maintenance |

--

*This playbook is maintained by the Oracle Database@Azure AI Solutions team. For contributions, corrections, or customer-specific architecture reviews, contact the authors.*
