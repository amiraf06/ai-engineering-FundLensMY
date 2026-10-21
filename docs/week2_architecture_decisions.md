# TicketIQ: V1 Architecture Specification, Decisions, and Traceability

**Version:** 1.0  
**Status:** Week 2 Conceptual V1 Architecture  

---

## Step C1 - Starting with the Simplest Architecture

### 1. Baseline Simple Flow
`Support Agent Query -> TicketIQ Application -> Single AI Model Capability -> Category & Troubleshooting Response`

### 2. What the Simple Architecture Can Do
* Accepts raw support ticket text from a help-desk agent and returns a predicted category or generic troubleshooting advice based on a single model's internal parameters.
* Minimises initial setup complexity by avoiding external data stores, pre-processing filters, and post-processing verification gates.

### 3. Why the Simple Architecture Fails TicketIQ's Requirements (Limitations)
* **Fails Verifiable Grounding (FR2, FR3, RAI2):** A standalone model without access to an audited historical ticket store cannot retrieve real past incidents or cite verifiable source Ticket IDs.
* **Fails Safe Abstention (FR4, RAI2):** Without an explicit relevance threshold control, a simple pipeline will force a best-guess output even when an incoming ticket represents a novel or unsupported issue.
* **Fails Privacy & Scope Boundaries (FR5, RAI1):** Passing raw user text directly to an AI capability without an input guardrail risks indexing or exposing Personal Identifiable Information (PII) and processing empty or out-of-domain inputs.
* **Fails Independent Maintainability (NFR4):** Coupling historical knowledge inside model weights prevents support teams from adding newly resolved tickets in under 5 minutes without retraining the model.

---

## Step C2 - Identifying Architecture Needs from Requirements

Each additional component in the TicketIQ V1 conceptual architecture is strictly justified by one or more requirements from `docs/week2_requirements.md` and evaluation criteria from `docs/week2_evaluation_criteria.md`:

| Requirement ID | What Does the Requirement Need? | Architecture Response (Component Added to V1) |
| :--- | :--- | :--- |
| **FR5, RAI1** | Incoming ticket text must be validated for domain scope (non-empty textual support queries) and scrubbed of PII (names, emails, phone numbers, IPs) before model inference or indexing. | **Input Scope & Privacy Control** |
| **FR1, NFR1, RAI3** | The system must predict a primary ticket category and confidence score accurately across imbalanced classes using only submission-time fields. | **Ticket Classification Capability** |
| **FR2, NFR2, NFR4** | The system must search historical incidents and allow new resolved tickets to be added in < 5 minutes without retraining the classifier. | **Similarity Retrieval Capability** connected to an external **Historical Ticket Knowledge Source** |
| **FR3, FR4, RAI2** | Recorded resolutions must only be displayed when match similarity meets the usefulness threshold, always accompanied by a source Ticket ID; otherwise, the system must abstain. | **Relevance & Abstention Control** |
| **NFR1, NFR2, NFR3, RAI2, RAI3** | The system must log predictions, similarity scores, abstention decisions, source IDs, and end-to-end execution latency (< 2,000 ms p90) for systematic evaluation. | **Evaluation & Telemetry Point** |
| **RAI4** | Historical data must be deduplicated, stripped of PII, and separated from post-submission leakage fields prior to indexing. | **Offline Data Audit & Sanitization Pipeline** (feeding the Historical Ticket Knowledge Source) |

---

## Step C3 - V1 Conceptual Architecture Overview

*(Refer to `docs/week2_architecture_v1.png` for the visual diagram)*

To adhere to V1 conceptual design principles, components are defined by their functional capabilities rather than premature technology commitments:

1. **User:** IT Help-Desk Agent submitting an incoming ticket (Subject + Initial Description).
2. **Application Interface (TicketIQ Triage Interface):** Captures ticket input and renders structured decision-support outputs (Predicted Category, Top-3 Similar Cases, Recorded Resolution with Source Ticket ID, or an Explicit Abstention Notice).
3. **Input Scope & Privacy Control:** Validates that the query is non-empty support text (`FR5`) and redacts PII tokens (`RAI1`) before forwarding sanitised text to downstream AI capabilities.
4. **Ticket Classification Capability:** Generates the primary category prediction and confidence score (`FR1`, `NFR1`, `RAI3`).
5. **Similarity Retrieval Capability:** Queries the knowledge source to rank and return the top-3 most similar historical ticket records (`FR2`, `NFR2`).
6. **Historical Ticket Knowledge Source:** Stores audited, deduplicated, and PII-redacted historical tickets containing ticket IDs, initial descriptions, categories, and verified resolutions (`NFR4`, `RAI1`, `RAI4`).
7. **Relevance & Abstention Control:** Evaluates whether retrieved candidates meet the defined similarity threshold. Passes valid resolutions with Source IDs (`FR3`, `RAI2`) or triggers an explicit abstention alert for manual triage (`FR4`, `RAI2`).
8. **Evaluation & Telemetry Point:** Captures latency metrics, classification outputs, retrieval scores, and abstention flags for offline benchmark validation (`NFR1-NFR5`, `RAI1-RAI3`).

---

## Step C4 - Architecture Decision Record (ADR) and Traceability

### 1. Architecture Decision Record (ADR)

| Decision | Requirement / Constraint | Alternatives Considered | Selected Option | Trade-off Accepted |
| :--- | :--- | :--- | :--- | :--- |
| **ADR 1: Evidence-Grounded Retrieval with Abstention Gate vs. Generative Troubleshooting** | **FR3, FR4, RAI2, NFR5** (Zero fabricated fixes, 100% source ID citation, >= 90% abstention on unmatchable queries, zero API budget) | 1. Generative Language Model synthesizing custom fixes from prompt context.<br>2. Retrieval-only display without threshold filtering.<br>3. Retrieval capability paired with an explicit **Relevance & Abstention Control**. | **Option 3:** Retrieve verbatim historical resolutions gated by an explicit **Relevance & Abstention Control**. | Responses are limited to existing historical resolution text rather than tailored conversational prose, and borderline valid matches may be suppressed (false abstentions); however, this completely eliminates generative hallucination and satisfies zero-cost constraints. |
| **ADR 2: Decoupled Classification Capability and Similarity Retrieval Capability** | **FR1, FR2, NFR1, NFR4, RAI3** (Macro-F1 >= 0.70 across imbalanced classes; index updates in < 5 min without classifier retraining) | 1. Single nearest-neighbour retrieval engine doing both case lookup and majority-vote classification.<br>2. Single classification model without historical case lookup.<br>3. Decoupled **Ticket Classification Capability** and **Similarity Retrieval Capability** executed in parallel. | **Option 3:** Separate **Ticket Classification Capability** and **Similarity Retrieval Capability**. | Increases pipeline maintenance overhead (two distinct capability modules to manage), but allows the classifier to optimise global decision boundaries for minority classes (`RAI3`) while enabling instant, training-free updates to the **Historical Ticket Knowledge Source** (`NFR4`). |
| **ADR 3: Dedicated Input Scope & Privacy Control Layer** | **FR5, RAI1** (0 exposed raw PII tokens; immediate rejection of empty or invalid inputs) | 1. Relying solely on raw dataset pre-cleaning without runtime input filtering.<br>2. Sending raw queries directly to AI capabilities and masking outputs post-hoc.<br>3. Dedicated pre-inference **Input Scope & Privacy Control**. | **Option 3:** Dedicated **Input Scope & Privacy Control** placed before classification and retrieval capabilities. | Adds minor preprocessing latency (~10-50 ms per query) and risks over-redacting technical terms that resemble PII patterns, but guarantees that raw PII never enters model logs, embeddings, or evaluation telemetry. |
| **ADR 4: Local / Self-Contained Knowledge Source and Inference Execution** | **NFR3, NFR5, Important Constraints** (p90 latency < 2,000 ms; US$0.00 operational cost; student compute budget) | 1. Cloud-hosted commercial LLM and managed vector database APIs.<br>2. Self-contained local/Colab execution with an in-memory/local **Historical Ticket Knowledge Source**. | **Option 2:** Self-contained execution and local **Historical Ticket Knowledge Source**. | Limits maximum corpus scalability compared to distributed cloud infrastructure, but guarantees US$0.00 inference cost (`NFR5`), eliminates external network latency (`NFR3`), and prevents third-party data transmission (`RAI1`). |

---

### 2. Initial Requirements Traceability Matrix

| Objective | Requirement | Evaluation Criterion | Architecture Element | Later Evidence (Week 3 - Week 14) |
| :--- | :--- | :--- | :--- | :--- |
| **O1** | **FR1, NFR1, RAI3, RAI4** | **Ticket Category Classification Accuracy** (Macro-F1 >= 0.70; Per-class Recall >= 0.55; 0% leakage) | **Ticket Classification Capability** & **Evaluation Point** | Held-out test split classification report, per-class confusion matrix, and leakage audit log (Weeks 5, 8, and 10). |
| **O2** | **FR2, NFR2, NFR4** | **Similar-Ticket Retrieval Relevance** (Hit Rate@3 >= 80%; Precision@3 >= 0.65; Index update < 5 min) | **Similarity Retrieval Capability** & **Historical Ticket Knowledge Source** | 50-query benchmark rubric evaluation sheet and timed 100-ticket index update log (Weeks 6, 9, and 10). |
| **O2, O3** | **FR3, FR4, RAI2** | **Evidence Grounding and Low-Relevance Abstention** (100% Source ID presence; >= 90% abstention rate on unmatchable queries) | **Relevance & Abstention Control** & **TicketIQ Triage Interface** | Abstention test suite results across 30 matchable and 20 unmatchable/out-of-scope queries (Weeks 9 and 10). |
| **O1, O2** | **FR5, RAI1** | **PII Redaction Compliance & Input Validation** (0 exposed PII tokens in index or UI output) | **Input Scope & Privacy Control** & **Offline Data Sanitization Pipeline** | Automated privacy & boundary unit test logs on 50 seeded PII queries and 100 indexed records (Weeks 4 and 11). |
| **O1, O2, O3** | **NFR3, NFR5** | **Triage Response Latency and Cost Efficiency** (p90 latency < 2.0 s; Cost = US$0.00) | **End-to-End V1 Pipeline** monitored via **Evaluation Point** | Latency distribution benchmark report across 100 sequential queries and compute cost log (Weeks 10 and 12). |

---

### 3. Final V1 Architecture Review Checklist

* **Does each major component support a requirement or evaluation need?**  
  Yes. Every box in the V1 architecture maps directly to at least one functional, non-functional, or Responsible AI requirement in the traceability matrix above.
* **Is any important data source, safety control, or evaluation point missing?**  
  No. The **Historical Ticket Knowledge Source** supplies domain grounding, **Input Scope & Privacy Control** and **Relevance & Abstention Control** enforce entry and exit safety boundaries, and the **Evaluation Point** captures performance telemetry.
* **Have I added any technology or component that is not yet justified?**  
  No. All components are specified at the functional capability level, and optional features such as automated priority prediction have been excluded from V1 pending data validation.
* **Is there a simpler architecture that could still satisfy the requirements?**  
  No. Removing either the **Input Scope & Privacy Control** or the **Relevance & Abstention Control** would violate `RAI1` (privacy) and `RAI2` (safe abstention), while merging classification and retrieval into a single static component would violate `NFR4` (training-free index maintainability).

---

## Part C Reflection

**Question:** *Which architecture decision involved the most important trade-off? Which requirement drove the decision, and what trade-off did you accept?*

**Reflection:**  
The architecture decision involving the most critical trade-off is **ADR 1: Evidence-Grounded Retrieval with an Explicit Relevance & Abstention Control versus Generative Troubleshooting**, which was directly driven by **FR3, FR4, and RAI2 (Transparency, Grounding, and Safe Abstention)**. In many modern AI help-desk tools, a generative model synthesizes a fluent, step-by-step troubleshooting guide even when retrieval confidence is mediocre. However, because TicketIQ relies on public ITSM datasets where historical resolutions may be uneven in quality and where an incorrect fix could disrupt enterprise IT operations, allowing free-form text generation introduces an unacceptable risk of hallucinated instructions. By choosing to display only verbatim recorded resolutions gated behind a strict **Relevance & Abstention Control**, I accepted the trade-off that TicketIQ's outputs will be less conversational and that borderline threshold tuning may occasionally trigger false abstentions (hiding a potentially related ticket when similarity falls slightly below the cutoff). In an operational IT triage context, however, failing safely by asking a human agent to perform manual triage is far preferable to presenting an unverified or fabricated technical fix with false confidence.