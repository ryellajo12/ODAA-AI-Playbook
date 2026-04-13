# Path 5 â€" Power Apps + Power Automate AI

## Architecture

Power Apps and Power Automate connect to Oracle Database@Azure via the Oracle Database connector and the On-Premises Data Gateway, enabling business workflows with incremental AI capabilities.

## Setup Steps

1. **Set up On-Premises Data Gateway** (same as Path 1 â€" see [Copilot Studio Prerequisites](03-copilot-studio.md#93-prerequisites))
2. **Create a Power App:**
   - Steps: https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/connections/connection-oracledb
   - Use the Oracle Database connector
   - Build forms, views, and dashboards connected to Oracle tables
3. **Add AI Builder:**
   - Form processing (OCR on Oracle-stored documents)
   - Prediction models on Oracle data
   - Text classification and summarization
4. **Create Power Automate flows:**
   - Trigger on Oracle data changes (via polling or scheduled)
   - Use Copilot in Power Automate for natural language flow creation
   - Chain with Azure AI services for enrichment

## AI Capabilities in Power Platform

| Capability | How It Works |
|-----------|-------------|
| **AI Builder** | Pre-built and custom AI models (prediction, form processing, object detection) |
| **Copilot in Power Apps** | Natural language app building; auto-generates screens from Oracle tables |
| **Copilot in Power Automate** | Describe a flow in English; Copilot builds it |
| **GPT action** | Call Azure OpenAI from within a flow for summarization, extraction |
