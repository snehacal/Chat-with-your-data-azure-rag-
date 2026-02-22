# Prompt Design

This document describes the core prompts used to extract structured insights from SEC 10-Q filings using a Retrieval-Augmented Generation (RAG) approach.

All prompts are designed to operate **only on retrieved context**, enforce **citation grounding**, and produce **structured, decision-ready outputs**.

---

## 1. Financial Performance Prompt

### Objective
Summarize key financial metrics for the current quarter and year-to-date (if available).

### Instructions

From the retrieved 10-Q excerpts:

- Summarize financial performance for the **current quarter** and **year-to-date**, if reported.
- Include the following metrics:
  - Revenue
  - Gross margin
  - Operating income
  - Net income
  - EPS
  - Cash flow highlights
- If non-GAAP metrics are reported:
  - List them separately
  - Clearly label them as **non-GAAP**
- Provide a short explanation of **“What changed vs the prior period.”**

### Output Requirements

- Present all metrics in a **table**
- Every numeric value must include a **citation**
- If a metric is not found, write **“Not found”**
- Do not infer or calculate missing values

---

## 2. Business Operations Prompt

### Objective
Identify major operational or strategic changes disclosed in the filing.

### Focus Areas

Look specifically for mentions of:

- Acquisitions or divestitures
- Restructuring activities
- Product launches or discontinuations
- Segment or geographic changes
- Supply chain updates
- Major customer or partner changes

### Output Requirements

- Return the **top 5 changes** (or fewer if less are disclosed)
- For each change, include:
  - **What happened**
  - **Why it matters**
  - **Evidence** (direct quote ≤ 20 words)
  - **Citation**
- If no relevant changes are disclosed, state this clearly

---

## 3. Risk Factors Prompt

### Objective
Extract and organize disclosed risks from the 10-Q.

### Risk Categories

Group risks into the following categories **only if present**:

- Market
- Operational
- Financial / Liquidity
- Legal / Regulatory
- Cyber / Technology
- Supply Chain
- Other

### Output Requirements

- Use category headings
- List **2–6 risks per category** (only those disclosed)
- Each risk must include:
  - One-sentence summary
  - Citation
- Answer this explicitly **only if stated in the filing**:
  - Are these new or materially changed risks versus prior disclosures?
- Do **not** invent or infer risks

---

## 4. Management Discussion (MD&A) Prompt

### Objective
Create a concise executive-level summary of management’s discussion and analysis.

### Output Format

Produce a **one-page executive brief** with the following sections:

1. **Financial Performance**
   - Table of key metrics (with citations)
2. **Operational Highlights**
   - Top 5 bullet points
3. **Key Risks**
   - Top 5 risks disclosed
4. **MD&A Summary**
   - Management’s explanation in **5 concise bullets**
5. **Open Questions**
   - Any unclear, missing, or forward-looking items explicitly noted or implied in the filing

### Constraints

- Use only retrieved 10-Q context
- Every factual claim must be cited
- If information is missing, state it explicitly

---

## Design Principles

All prompts follow these principles:

- **Grounded generation only** (no external knowledge)
- **Citation enforcement**
- **Structured outputs**
- **No hallucination or inference**
- **Clear separation of facts vs missing data**

---

## Notes

These prompts are used within Azure AI Foundry and rely on upstream retrieval quality (chunking, metadata filtering, TopK tuning) to ensure accurate responses.
