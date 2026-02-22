# Retrieval Strategy

This document explains the structured retrieval approach implemented in the project to ensure precise, relevant, and citation-grounded answers from 10-Q financial documents.

The design focuses on aligning retrieval outputs with the structure of SEC filings, minimizing noise, and enforcing context grounding before generation.

---

## 1) Document Chunking

### Heading-Based Chunking

Rather than splitting documents by token count alone, this system leverages the natural structure of 10-Q filings:

- Detects major sections like:
  - Item 1A – Risk Factors  
  - Item 2 – Management’s Discussion & Analysis (MD&A)  
  - Item 1 – Financial Statements  
  - Notes and Legal Proceedings

Chunks are created around these headings so that each chunk represents a coherent semantic block.

**Benefits**
- Preserves section meaning  
- Reduces retrieval noise  
- Improves grounding quality

---

## 2) Metadata Tagging

Each chunk carries structured metadata:

| Metadata Field | Description |
|----------------|-------------|
| `company`      | Company name from the filing |
| `filing_date`  | Report date (e.g., 2024-09-30) |
| `section`      | Filing section (e.g., MD&A, Risk Factors) |
| `item`         | Item number (1, 1A, 2, etc.) |

This enables precise filtering and query routing:

```text
filter=company eq 'Tesla' and item eq '1A'



This improves relevance and reduces irrelevant context.

---

## 3) Section-Aware Filtering

Not all queries need the entire document. Based on the user’s question intent, the system applies section filters:

- **Risk-related questions** → Item 1A – Risk Factors  
- **Performance / outlook questions** → Item 2 – MD&A  
- **Numerical metrics** → Item 1 – Financial Statements

This focused filtering reduces retrieval noise and improves precision.

---

## 4) Embeddings & Vector Search

### Embedding Model

- **text-embedding-3-large** is used for semantic representation of text chunks.
- It produces high-quality embeddings for financial language and context.

### Indexing

- Azure AI Search builds a hybrid index combining:
  - Vector embeddings  
  - Metadata fields

This allows semantic search with metadata filters.

---

## 5) Top-K Tuning

The number of chunks retrieved per query is controlled by the **Top-K** setting.

**Configured value:**

**Rationale**
- A small TopK increases precision (less noise).
- A larger TopK could increase recall but may introduce unrelated chunks.
- Value chosen based on manual observation across multiple prompts.

---

## 6) minimumCoverage Setting

The `minimumCoverage = 100` setting ensures:

- The index is scanned completely before returning results.
- No early cut-off due to partial coverage.

This reduces the risk of missing relevant chunks and improves grounding quality.

---

## 7) Production Reliability Considerations

While not core to retrieval logic, these aspects improve robustness in production:

### Replica Scaling
- Increase Azure AI Search replicas to support higher query rates and reduce 503 errors.

### Monitoring
- Monitor query latency and error rates.
- Scale replicas or adjust service tiers based on load.

---

## 8) Why This Matters

A good retrieval strategy for RAG systems must:

- Return relevant context that aligns with the user’s question
- Minimize noise and unrelated information
- Support accurate, citation-backed generation
- Optimize performance without sacrificing grounding

By combining structured chunking, metadata tagging, section filters, controlled retrieval (TopK), and complete index coverage, this strategy ensures high precision and useful responses for financial Q&A.

---

## 9) Future Enhancements

Possible improvements include:

- Automated retrieval precision/recall measurement
- Dynamic TopK tuning based on query type
- Cross-document retrieval
- Token-aware chunk overlap adjustments
