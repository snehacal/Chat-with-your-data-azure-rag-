
# Runbook

This runbook provides operational guidance for deploying, validating, maintaining, and scaling the 10-Q Retrieval-Augmented Generation (RAG) system built using Azure AI Search and Azure OpenAI.

This document is intended to support repeatable deployment and reliable operation.

---

# 1. System Overview

The system architecture consists of:

- Azure Blob Storage (stores 10-Q PDFs)
- Azure AI Search (vector + metadata index)
- Azure OpenAI
  - text-embedding-3-large (embeddings)
  - gpt-4o-mini (generation)
- Azure AI Foundry (chat orchestration)

---

# 2. Initial Deployment

## 2.1 Provision Azure Resources

Ensure the following resources exist:

| Service | Purpose |
|----------|----------|
| Azure Blob Storage | Document storage |
| Azure AI Search | Indexing and vector search |
| Azure OpenAI | Embeddings and generation |
| Azure AI Foundry | Chat orchestration |

Confirm:
- All services are in the same region
- OpenAI deployments are active
- Search service tier supports vector search

---

## 2.2 Upload Documents

1. Open Azure Portal → Blob Storage
2. Navigate to container (e.g., `10qdocs`)
3. Upload 10-Q PDF documents

Verify:
- Files appear in container
- PDFs are not image-only (text extractable)

---

## 2.3 Configure Data Source

1. Open Azure AI Search
2. Create or verify Data Source connection to Blob container
3. Confirm authentication method
4. Save configuration

---

## 2.4 Create / Validate Index

Index should include:

- Content field (searchable)
- Vector field (embedding)
- Metadata fields:
  - company
  - filing_date
  - item
  - section

Confirm vector dimension matches embedding model output.

---

## 2.5 Run Indexer

1. Navigate to Indexers
2. Run indexer
3. Wait until status = **Succeeded**

Verify:
- Document count > 0
- No extraction errors

---

# 3. Retrieval Configuration

## 3.1 Embedding Model


text-embedding-3-large


## 3.2 Generation Model


gpt-4o-mini


## 3.3 Retrieval Parameters

```json
{
  "topK": 5,
  "minimumCoverage": 100
}
Configuration Rationale

TopK = 5 balances precision and recall.

minimumCoverage = 100 ensures full index scanning.

Metadata filters reduce section noise.

## 4. System Prompt Deployment

Open Azure AI Foundry → Chat Configuration

Paste the validated system prompt

Save configuration

Test with sample prompts

Ensure prompt enforces:

Grounding

Citation requirement

No external knowledge

Structured output

##5. Functional Validation
###5.1 Test Prompts

Run the following:

"Summarize the key risk factors."

"What were the revenue highlights?"

"Describe management’s outlook."

5.2 Validation Checklist
Validation Item	Expected Result
Response returned	Yes
Citations included	Yes
Claims match source	Yes
No hallucination	Yes
Section relevance	Correct

If any fail, review retrieval configuration.

##6. Monitoring
###6.1 Key Metrics

Monitor via Azure Portal:

Metric	Where
Query latency	Azure AI Search metrics
HTTP 503 errors	Azure AI Search logs
Token usage	Azure OpenAI dashboard
Throughput	Azure Monitor
6.2 Recommended Alert Thresholds
Metric	Alert Condition
Latency	> 1 second
Error Rate	> 1%
503 errors	> 0 occurrences
Token Cost Spike	> planned budget
##7. Troubleshooting Guide
###7.1 Indexer Failure

Symptoms:

Status = Failed

Actions:

Verify Blob connection

Check PDF extractability

Re-run indexer

##7.2 No Relevant Results

Symptoms:

Empty or unrelated responses

Actions:

Validate metadata filters

Temporarily increase TopK

Confirm embeddings generated correctly

##7.3 503 Service Unavailable

Cause:

Search replicas insufficient

Fix:

Increase replica count

Monitor throughput

##8. Scaling Strategy

Scale when:

Latency increases

Query volume increases

503 errors occur

Options:

Increase AI Search replicas

Upgrade service tier

Optimize TopK if cost increases

##9. Cost Management

Monitor:

OpenAI token usage

AI Search replica count

Embedding calls per document

Cost Controls:

Use gpt-4o-mini for generation

Keep TopK controlled

Avoid unnecessary re-indexing

##10. Security Practices

Never commit API keys

Use environment variables

Maintain .env.example template

Example:

AZURE_OPENAI_ENDPOINT=
AZURE_OPENAI_KEY=
AZURE_SEARCH_ENDPOINT=
AZURE_SEARCH_KEY=
##11. Rollback Plan

If deployment fails:

Revert to last working configuration

Re-validate indexer

Re-test prompt suite

Confirm citation correctness

##12. Post-Deployment Review

After changes:

Re-run validation prompts

Verify citation accuracy

Log latency and cost

Document issues

Maintain log:

Date	Change	Impact	Resolved





