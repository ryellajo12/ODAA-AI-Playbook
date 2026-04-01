# 18. Oracle ORDS as an AI Tool Layer

## 18.1 Why ORDS Matters for AI

Oracle REST Data Services (ORDS) transforms Oracle database objects (tables, views, PL/SQL procedures) into REST APIs. For AI agents, this means:

| Benefit | Detail |
|---------|--------|
| **Pre-built, governed endpoints** | DBAs create curated REST APIs; agents call them without writing SQL |
| **No direct SQL needed** | Reduces risk of SQL injection or unintended queries |
| **OpenAPI compatible** | ORDS generates OpenAPI specs that AI agents can consume directly |
| **Authentication** | Supports OAuth2, Basic Auth, API keys |
| **Pagination** | Built-in `limit`/`offset` for large result sets |
| **Filtering** | JSON-based `q` parameter for flexible filtering |

## 18.2 ORDS Configuration for AI Agents

**Best practices for ORDS endpoints consumed by AI:**

1. **Create purpose-built views** — Don't expose raw tables; create views with business-friendly column names and pre-computed metrics
2. **Use modules for grouping** — Group related endpoints under a single ORDS module (e.g., `/aisearch/`, `/vectorsearch/`)
3. **Document with OpenAPI** — Generate or hand-craft OpenAPI specs with rich descriptions; LLMs use descriptions to decide which endpoint to call
4. **Add examples in OpenAPI** — Include example request/response pairs; this dramatically improves LLM tool selection accuracy
5. **Implement pagination** — Set sensible `items_per_page` defaults (25-50); agents can request more if needed
6. **Use filtering** — Expose the `q` parameter for agents to filter dynamically

## 18.3 Sample OpenAPI Pattern for AI

```json
{
  "operationId": "getPromotionPerformance",
  "summary": "Get detailed promotion performance with ROI metrics",
  "description": "Returns ROI percentage, profit, revenue per dollar spent. Use this when the user asks about promotion effectiveness, best/worst promotions, or marketing ROI. Filter by year with q={\"sales_year\":2025} or by channel with q={\"promo_category\":\"internet\"}."
}
```

> **Tip:** The `description` field in OpenAPI is the most important field for AI agents. Write it as if you're explaining the endpoint to a human analyst.
