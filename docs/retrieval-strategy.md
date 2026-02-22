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
