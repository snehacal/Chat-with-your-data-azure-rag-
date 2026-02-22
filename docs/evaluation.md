# Evaluation

This document outlines how the retrieval system was evaluated qualitatively and aligned with production evaluation considerations for a Retrieval Augmented Generation (RAG) application on 10-Q financial filings.

## Evaluation Objective

The objective of this evaluation is to assess the reliability, grounding accuracy, and financial precision of the RAG system built using Azure AI Search and Azure OpenAI.

The evaluation focuses on:

- Retrieval relevance  
- Citation accuracy  
- Financial numerical precision  
- Hallucination control  
- Structured response compliance

## Evaluation Method

Five prompts were tested across core financial analysis dimensions:

| Category | Prompt Focus |
|-----------|-------------|
| Financial Performance | Revenue, margins, quarterly trends |
| Risk Factors | Item 1A disclosures |
| Operations | Operational drivers and constraints |
| Management Outlook | Forward-looking statements |
| Legal / Disclosures | Material disclosures or proceedings |

The scores below reflect **manual assessment** using the scoring rubric defined in the next section.

## Scoring Rubric (0–2 Scale)

Each dimension is scored from 0 to 2:

| Dimension | 0 | 1 | 2 |
|-----------|----|----|----|
| Grounding | Unsupported claims | Partial citations | All claims supported by citations |
| Citation Accuracy | Incorrect sections | Minor mismatch | Fully accurate section references |
| Financial Precision | Inaccurate figures | Minor rounding | Exact figures with reporting period |
| Hallucination Control | Fabricated content | Minor inference | Strictly document-based |
| Structure Compliance | Unstructured response | Partially structured | Fully structured output |

**Maximum score per prompt: 10**

## Sample Evaluation Results

| Prompt Category | Score | Observations |
|----------------|--------|-------------|
| Financial Performance | 9/10 | Exact figures reported with correct citations |
| Risk Factors | 10/10 | Section-aware extraction with precise citations |
| Operations | 8/10 | Accurate but slightly verbose |
| Management Outlook | 9/10 | Clear citation traceability |

**Average Evaluation Score: 9/10**

## Retrieval Performance Observations

- Heading-based chunking improved contextual alignment with filing sections.
- Metadata tagging reduced cross-section noise.
- Top-K = 5 provided a balanced precision/recall trade-off.
- `minimumCoverage` ensured complete index scanning.
- No hallucinations were observed when relevant context was retrieved.

## Model Performance

Model used: **gpt-4o-mini**

Observed characteristics:

- Low latency and responsive interaction  
- Strong citation discipline under the system prompt constraints  
- Reliable for structured financial document Q&A  
- Response quality closely tied to retrieval accuracy

**Conclusion:**  
*gpt-4o-mini is sufficient for citation-grounded financial analysis when paired with structured retrieval and a strict system prompt.*

## Limitations

- Cross-quarter comparisons require retrieval of both periods.
- Very large financial tables may exceed token limits.
- Evaluation is based on **manual scoring**, not automated metrics.

## Future Enhancements

- Introduce lightweight automated evaluation scoring.
- Benchmark GPT-4o vs gpt-4o-mini for reasoning depth comparison.
- Add retrieval precision/recall measurement.
- Incorporate latency benchmarking across prompt categories.

## Production-Level Evaluation Considerations

In enterprise environments, additional metrics are typically monitored:

### Retrieval Precision
Measures how often retrieved chunks contain relevant sections.  
Not formally measured here but observed to be strong with heading-based chunking and metadata filtering.

### Hallucination Rate
Tracks the frequency of unsupported claims in model responses.  
No hallucinations were observed during manual evaluation with correct context retrieval.

### Latency
Time taken from user query to response generation.  
Observed to be low using gpt-4o-mini; formal latency benchmarking was not conducted.

### Cost per Query
Estimated based on token usage and model pricing.  
gpt-4o-mini was selected to optimize cost efficiency.

### Error Rate
Includes failed retrievals or empty context returns.  
No runtime failures were observed during manual testing.

### Citation Accuracy
Measures whether citations correctly map to the source sections.  
Manual review confirmed high citation accuracy across evaluated prompts.
