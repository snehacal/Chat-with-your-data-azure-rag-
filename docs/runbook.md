# Runbook

This runbook provides operational guidance for maintaining, testing, troubleshooting, and scaling the Azure RAG Financial Document Assistant built for 10-Q analysis.

It includes:

- Environment setup
- Document indexing procedures
- Retrieval configuration validation
- Testing checklist
- Common errors and fixes
- Monitoring and scaling guidance

---

## 1. Environment Setup

### Azure Resources Required

| Resource | Purpose |
|-----------|---------|
| Azure Blob Storage | Stores 10-Q PDF documents |
| Azure AI Search | Vector + metadata indexing |
| Azure OpenAI | gpt-4o-mini + embedding model deployment |
| Azure AI Foundry | RAG orchestration and chat interface |

Before proceeding, verify:

- Azure AI Search service is provisioned
- OpenAI deployments are active
- Indexer status is `Succeeded`
- Documents are successfully indexed

---

## 2. Document Upload & Indexing

### Step 1: Upload Documents

1. Open Azure Blob Storage
2. Navigate to the container (e.g., `10qdocs`)
3. Upload 10-Q PDF files

### Step 2: Validate Data Source

1. Open Azure AI Search
2. Confirm data source connection to Blob container
3. Ensure index schema includes:
   - Content field
   - Vector embedding field
   - Metadata fields (`company`, `filing_date`, `item`, `section`)

### Step 3: Run Indexer

- Run indexer manually if needed
- Confirm status shows: `Succeeded`
- Confirm indexed document count > 0

---

## 3. Retrieval Configuration

### Embedding Model
text-embedding-3-large


### Generation Model
gpt-4o-mini


### Retrieval Parameters

```json
{
  "topK": 5,
  "minimumCoverage": 100
}






