# 9. Path 1 — Copilot Studio + Oracle (Gateway, Knowledge, Tool)

## 9.1 Architecture

Copilot Studio connects to Oracle Database@Azure through multiple integration modes:

1. **Oracle as a Connector (via Gateway)** — Direct read/write access to Oracle tables through the On-Premises Data Gateway
2. **Oracle as Knowledge** — Ground your custom copilot on specific Oracle tables or data so the LLM uses Oracle data as context to answer questions
3. **Oracle as a Tool** — Register Oracle queries or ORDS endpoints as copilot actions/tools that the copilot can call during conversations

These modes can be combined in a single copilot for maximum flexibility.

## 9.2 Three Integration Modes (Detailed)

### Mode A: Oracle via Gateway Connector (Direct Data Access)

The On-Premises Data Gateway provides a direct, secure channel to Oracle data. The copilot executes actions that read/write Oracle tables.

**Use when:** You need the copilot to fetch specific rows, run parameterized queries, or update records.

### Mode B: Oracle as Knowledge Source (Grounding)

Copilot Studio allows you to add **Knowledge sources** that ground the copilot's responses. You can point Knowledge at Oracle tables or views so the copilot uses that data as context when answering questions — the LLM retrieves relevant Oracle data before generating a response.

**Use when:** You want the copilot to "know" about Oracle data (e.g., product catalogs, policies, FAQs stored in Oracle) and answer questions conversationally without the user needing to specify exact queries.

**How it works:**
1. Create Oracle views that expose the data you want to ground on (e.g., `V_PRODUCT_FAQ`, `V_POLICY_DOCS`)
2. Expose those views via ORDS REST endpoints or the Oracle connector
3. In Copilot Studio → **Knowledge** → Add the Oracle data source
4. Select the specific tables, views, or data you want the copilot to use
5. The copilot automatically retrieves relevant rows when answering questions

### Mode C: Oracle as a Tool (Action-Based)

Register Oracle-backed actions as **Tools** in Copilot Studio. The copilot decides when to call these tools based on the conversation.

**Use when:** You want the copilot to perform specific Oracle operations (lookup a customer, check order status, run a report) as part of a conversation flow.

**How it works:**
1. Create ORDS REST endpoints for each action (e.g., `/customer_lookup/`, `/order_status/`)
2. In Copilot Studio → **Tools** → Add an **OpenAPI** or **Connector** tool
3. Point to your ORDS endpoint or Oracle connector action
4. Define when the tool should be called (trigger phrases or AI-driven)

## 9.3 Prerequisites

- Microsoft 365 license with Copilot Studio entitlement
- On-Premises Data Gateway installed on an Azure VM or hybrid machine with network access to OD@A (for Gateway mode)
- Oracle Database@Azure instance (ADBS or Exadata)
- Oracle client libraries (Oracle Instant Client) on the gateway machine
- ORDS endpoints configured (for Knowledge and Tool modes)

## 9.4 Setup Steps

**For Gateway Connector:**
1. **Install the On-Premises Data Gateway** on an Azure VM within the same VNET (or peered VNET) as OD@A
2. **Install Oracle Instant Client** on the gateway VM
3. **Configure the Oracle connection** in Power Platform Admin Center:
   - Connection type: Oracle Database
   - Server: `<OD@A hostname>:<port>/<service_name>`
   - Authentication: Basic (Oracle DB user) or Entra ID pass-through

**For Oracle as Knowledge:**
4. **Create curated Oracle views** for the data you want to ground on
5. **Expose via ORDS** or connector
6. In Copilot Studio → **Knowledge** → **+ Add data source** → Select Oracle tables/views
7. Choose specific tables or columns to include (don't expose entire schemas)

**For Oracle as Tool:**
8. **Create ORDS REST endpoints** for specific actions
9. **Generate OpenAPI spec** for the endpoints
10. In Copilot Studio → **Tools** → **+ Add tool** → Upload OpenAPI spec or select connector action
11. Configure tool descriptions (the LLM uses these to decide when to call the tool)

**Deploy:**
12. **Test in the embedded chat** → Deploy to Teams / Web / Mobile

## 9.5 Design Considerations

| Consideration | Guidance |
|--------------|----------|
| **Knowledge vs Tool** | Use Knowledge for open-ended Q&A ("tell me about product X"); use Tools for specific actions ("look up order #123") |
| **Query complexity** | Pre-build Oracle views for complex joins; keep connector queries simple |
| **Data scoping** | For Knowledge sources, expose only the tables/columns relevant to the copilot's purpose; don't expose entire schemas |
| **Performance** | Gateway adds ~100-300ms latency; ORDS endpoints are generally faster for Knowledge/Tool modes |
| **Security** | Use a dedicated read-only Oracle user; never expose DBA credentials; scope Knowledge to non-sensitive data |
| **Freshness** | Knowledge sources reflect live Oracle data; no caching delay |
| **Scaling** | Gateway supports clustering for high availability |
| **Data types** | Gateway handles standard Oracle types; LOBs and custom types may need views |

## 9.6 Sample Configurations

**Knowledge Grounding Example:**
```yaml
Knowledge Source: Oracle Product Catalog
Type: Oracle Database (via connector)
Table: SH.PRODUCTS (view: V_PRODUCT_DETAILS)
Columns: PROD_NAME, PROD_CATEGORY, PROD_DESC, PROD_LIST_PRICE, PROD_STATUS
Behavior: Copilot answers "What products do we have in Golf?" by retrieving relevant rows
```

**Tool Example:**
```yaml
Tool: Get Promotion ROI
Type: OpenAPI (ORDS endpoint)
Endpoint: GET /ords/aisearch/promo_performance/?q={"sales_year":{year}}
Trigger: User asks about promotion performance or ROI
Response: Copilot calls the endpoint, formats the results as a table
```

**Topic + Connector Example:**
```yaml
Topic: Quarterly Sales Summary
Trigger: "What were sales in {quarter} {year}?"
Action:
  - Get rows from Oracle (via gateway)
  - Table: SH.SALES joined with SH.TIMES
  - Filter: CALENDAR_QUARTER = {quarter}, CALENDAR_YEAR = {year}
  - Aggregate: SUM(AMOUNT_SOLD), COUNT(*)
Response: "In {quarter} {year}, total sales were ${total} across {count} transactions."
```
