# 17. Step-by-Step Implementation Guides

## 17.1 Guide: Build a Microsoft Foundry agent with Oracle MCP + ORDS in 1 Hour

**Goal:** Create an AI agent in Microsoft Foundry that can query Oracle Sales History data via both MCP (SQL) and ORDS (REST).

**Prerequisites:**
- Microsoft Foundry project with GPT-4.1 deployed
- Oracle ADBS on Oracle Database@Azure with SH schema
- ORDS endpoints configured
- [Optional] SQLcl MCP server accessible

**Steps:**

| Step | Action | Time |
|------|--------|------|
| 1 | Open [ai.azure.com](https://ai.azure.com) → select your project | 2 min |
| 2 | **Agents** → **+ New Agent** → Name: `Oracle Sales Agent` → Model: `gpt-4.1` | 3 min |
| 3 | Paste system prompt from [Path 2 — Agent System Prompt](04-path2-foundry-agents.md#104-agent-system-prompt-template) into System message | 5 min |
| 4 | **+ Add Tool** → **OpenAPI** → Upload `promotion-insights-api.json` → Set base URL → Enable all operations | 10 min |
| 5 | **+ Add Tool** → **OpenAPI** → Upload `clinical-vector-search-api.json` → Set base URL → Configure Basic Auth credentials | 10 min |
| 6 | [Optional] **+ Add Tool** → **MCP Server** → Configure Oracle SQLcl connection | 10 min |
| 7 | Test in Playground: "What were the top 5 promotions by ROI?" | 5 min |
| 8 | Test: "Search for adverse events related to severe breathing problems" | 5 min |
| 9 | Test: "Compare promotion performance across internet vs TV channels" | 5 min |
| 10 | Deploy via API endpoint | 5 min |

---

## 17.2 Guide: Set Up Oracle MCP in VS Code in 15 Minutes

**Goal:** Get natural language → SQL working on Oracle Database@Azure in VS Code.

**Steps:**

| Step | Action | Time |
|------|--------|------|
| 1 | Install **SQL Developer Extension for VS Code** from marketplace | 2 min |
| 2 | Click the Database icon → **+ New Connection** | 2 min |
| 3 | Enter Oracle Database@Azure connection details (host, port, service name, username, password) | 3 min |
| 4 | Test connection → connected ✅ | 1 min |
| 5 | Open **GitHub Copilot** → switch to **Agent Mode** | 1 min |
| 6 | Type: `@oracle List all tables in the SH schema` | 1 min |
| 7 | Type: `@oracle Write a query to find the top 10 products by revenue` | 2 min |
| 8 | Type: `@oracle Explain the execution plan for this query` | 2 min |
| 9 | Verify MCP logs: `SELECT * FROM DBTOOLS$MCP_LOG ORDER BY timestamp DESC` | 1 min |

---

## 17.3 Guide: Build Oracle 26ai Vector Search + RAG in 2 Hours

**Goal:** Implement semantic search on Oracle data using 26ai vectors and Azure OpenAI embeddings.

**Prerequisites:**
- Oracle 26ai on Oracle Database@Azure
- Azure OpenAI with `text-embedding-3-small` deployed
- ORDS configured

**Steps:**

| Step | Action | Time |
|------|--------|------|
| 1 | Add VECTOR column to existing table (see [Pattern 2 — Step 1](04-path2-foundry-agents.md#step-1--add-vector-columns-to-existing-tables)) | 10 min |
| 2 | Configure Azure OpenAI credential in Oracle (DBMS_CLOUD) (see [Pattern 2 — Step 2](04-path2-foundry-agents.md#step-2--configure-embedding-generation)) | 10 min |
| 3 | Create embedding generation function ([Pattern 2 — Step 2](04-path2-foundry-agents.md#step-2--configure-embedding-generation)) | 15 min |
| 4 | Backfill embeddings for existing data (batch process) | 15 min |
| 5 | Create vector index ([Pattern 2 — Step 2](04-path2-foundry-agents.md#step-2--configure-embedding-generation)) | 5 min |
| 6 | Test vector search via SQL | 5 min |
| 7 | Create ORDS endpoint ([Pattern 2 — Step 4](04-path2-foundry-agents.md#step-4--expose-vector-search-via-ords-already-running-on-oracle-26ai)) | 15 min |
| 8 | Test ORDS endpoint via curl/Postman | 5 min |
| 9 | Create OpenAPI spec for the endpoint | 15 min |
| 10 | Register as tool in Microsoft Foundry agent | 10 min |
| 11 | Test end-to-end: natural language → embedding → vector search → LLM answer | 15 min |

---

## 17.4 Guide: Configure Fabric Mirroring for Oracle in 1 Hour

**Goal:** Mirror Oracle SH schema into Microsoft Fabric OneLake.

**Steps:**

| Step | Action | Time |
|------|--------|------|
| 1 | Open Fabric workspace → **+ New** → **Mirrored Database** | 2 min |
| 2 | Select **Oracle Database** as source | 1 min |
| 3 | Enter Oracle Database@Azure connection: host, port, service name | 5 min |
| 4 | Provide Oracle credentials (read-only user) | 2 min |
| 5 | Select tables: SH.SALES, SH.PRODUCTS, SH.PROMOTIONS, SH.CUSTOMERS, SH.TIMES | 5 min |
| 6 | Configure refresh schedule (e.g., every 15 minutes) | 3 min |
| 7 | Start mirroring → wait for initial sync | 10 min |
| 8 | Create SQL analytics endpoint on mirrored data | 5 min |
| 9 | Build a semantic model with business-friendly names | 10 min |
| 10 | Create a Fabric Data Agent on the semantic model | 10 min |
| 11 | Test: "What is the year-over-year revenue trend for golf products?" | 7 min |
