# 19. Monitoring, Observability & Cost

## 19.1 Monitoring Stack

| Component | Monitoring Tool | Key Metrics |
|-----------|----------------|-------------|
| AI Agent interactions | Azure Monitor / Application Insights | Request count, latency, token usage, error rate |
| Oracle MCP activity | `DBTOOLS$MCP_LOG` table | SQL executed, user, timestamp, duration |
| Oracle database | Oracle Enterprise Manager / OCI Monitoring | CPU, memory, I/O, query performance |
| ORDS endpoints | ORDS access logs + Azure APIM analytics | Call volume, response times, errors |
| Fabric mirroring | Fabric monitoring hub | Sync latency, row counts, failures |
| Azure OpenAI | Azure Monitor | Token consumption, throttling, latency |
| Network | Azure Network Watcher | Traffic flows, NSG hits |

## 19.2 Cost Estimation Guide

| Component | Pricing Model | Typical Pilot Cost |
|-----------|--------------|-------------------|
| OD@A (ADBS) | OCPU-hour | Existing — no incremental for AI |
| Azure OpenAI (GPT-4.1) | Per 1M tokens (input/output) | ~$50-200/month for pilot |
| Azure OpenAI (Embeddings) | Per 1M tokens | ~$5-20/month for pilot |
| Microsoft Foundry | Compute + model hosting | ~$100-300/month for pilot |
| Copilot Studio | Per message (varied plans) | ~$200/month per 25K messages |
| Microsoft Fabric | Fabric CU-hour | ~$200-500/month (F2) |
| Azure Functions | Per execution + compute | ~$5-20/month |
| API Management | Per unit | ~$50/month (Developer tier) |

## 19.3 Cost Optimization Tips

1. **Start with Copilot Studio or MCP local** — lowest-cost entry points
2. **Use `o4-mini` for non-critical tasks** — significantly cheaper than GPT-4.1 or o3
3. **Cache ORDS responses** via APIM — reduce Oracle query load and Azure OpenAI token usage
4. **Batch embeddings** — generate embeddings in bulk rather than per-request
5. **Use Fabric mirroring selectively** — don't mirror tables you won't query
6. **Set token limits** on agents — prevent runaway conversations
