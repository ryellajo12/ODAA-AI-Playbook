# 12. Path 4 — Microsoft Fabric + Data Agents

## 12.1 Architecture

Microsoft Fabric mirrors Oracle data into OneLake, creating a unified analytics layer that can power Fabric Data Agents and Microsoft Foundry agents.

## 12.2 Prerequisites

- Microsoft Fabric capacity (F2 or above)
- Oracle Database@Azure instance
- Fabric mirroring configured for Oracle

## 12.3 Setup Steps

1. **Configure Fabric Mirroring for Oracle:**
   - In Fabric workspace, create a new **Mirrored Database**
   - Select Oracle Database as the source
   - Provide OD@A connection details (host, port, service name, credentials)
   - Select tables/schemas to mirror (e.g., SH schema)
   - Configure refresh schedule (near-real-time or scheduled)

2. **Create a Lakehouse:**
   - Mirrored data lands in OneLake automatically
   - Create SQL analytics endpoints for querying
   - Build a semantic model for business-friendly naming

3. **Create a Fabric Data Agent:**
   - Navigate to **Data Engineering** → **AI** in Fabric
   - Create a new Data Agent connected to your mirrored data
   - Configure natural language understanding
   - Data Agent can answer questions across mirrored Oracle data + other Fabric sources

4. **Connect to Microsoft Foundry (Optional):**
   - Use Fabric's Microsoft Foundry integration to create agents grounded in mirrored data
   - Combine with Azure AI Search for hybrid retrieval

## 12.4 Design Considerations

| Consideration | Guidance |
|--------------|----------|
| **Latency** | Mirroring introduces latency (minutes to hours depending on config); not suitable for real-time transactional Q&A |
| **Data scope** | Mirror only the tables/schemas needed for analytics; don't mirror entire databases |
| **Cross-source** | Fabric's strength is joining Oracle data with SQL Server, Azure SQL, Dataverse, etc. |
| **Cost** | Fabric CU consumption scales with data volume and query complexity |
| **Security** | Data inherits Fabric workspace security; does NOT inherit Oracle RLS |
