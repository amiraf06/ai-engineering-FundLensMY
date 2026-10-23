# TicketIQ: Evaluation Criteria Specification

**Version:** 1.3  
**Status:** Week 2 Evaluation Plan  

---

## 1. Evaluation Criteria Table

The table below maps TicketIQ's core project objectives (`O1`, `O2`, `O3`) and key engineering requirements from `docs/week2_requirements.md` to concrete evaluation criteria, capability buckets, measurement methods, metrics, usefulness thresholds (acceptance targets), and expected user/business outcomes.

| Objective / Requirement ID | Evaluation Criterion | Capability Bucket | Measurement Method | Metric | Threshold (Acceptance Target) | User / Business Outcome |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **O1 / FR1 / NFR1 / RAI3** | **Ticket Category Classification Accuracy** | Domain-specific capability | Evaluate classifier predictions on a held-out, deduplicated test split of historical support tickets and compare against a majority-class baseline. | Macro-F1 score and per-class Recall | Macro-F1 >= 0.70; Per-class Recall >= 0.55 | Support agents receive reliable initial category tags, reducing manual sorting effort and routing delays across both common and minority ticket types. |
| **O2 / FR2 / NFR2 / NFR4** | **Similar-Ticket Retrieval Relevance & Maintainability** | Domain-specific capability | Evaluate top-3 retrieved historical tickets across a curated benchmark of 50 representative test queries using a 3-point domain relevance rubric, and time a 100-ticket index update test. | Hit Rate@3, Precision@3, and Index Update Time | Hit Rate@3 >= 80%; Precision@3 >= 0.65; Index update < 5 min | Agents quickly find genuinely comparable past incidents without manual keyword searches, while new resolved cases can be added without classifier retraining. |
| **O2 / O3 / FR3 / FR4 / RAI2** | **Evidence Grounding and Low-Relevance Abstention** | Instruction-following capability | Test the pipeline on a mixed evaluation set of 30 matchable queries and 20 unmatchable/out-of-scope queries; inspect outputs for source Ticket ID presence and abstention notices. | Source ID presence rate; Abstention success rate | 100% Source ID presence; >= 90% abstention rate on unmatchable queries | Reduces the risk of agents acting on unverified or misleading troubleshooting steps and ensures every displayed resolution can be audited back to its source ticket by a human agent. |
| **O1 / O2 / FR5 / RAI1** | **PII Redaction Compliance & Input Scope Validation** | Instruction-following capability | Run an automated privacy and boundary test suite with 50 synthetic queries and 100 indexed records containing seeded names, emails, phone numbers, and IP addresses. | Count of exposed raw PII tokens on configured test patterns in indexed text, telemetry, or UI outputs | 0 exposed raw PII tokens on configured test patterns | Minimises customer and employee privacy exposure during ticket search and ensures historical case lookup does not leak configured PII patterns across tickets. |
| **O1 / O2 / O3 / NFR3 / NFR5** | **Triage Response Latency and Cost Efficiency** | Cost and latency | Measure end-to-end execution time (classification + top-3 retrieval) across 100 sequential queries on target hardware and log external AI/API costs. | p90 latency; External AI/API cost per 100 queries | p90 latency < 2.0 s (< 2,000 ms); External AI/API cost = US$0.00 | Supports responsive interactive help-desk triage without slowing down the human agent or incurring paid external API expenses. |

---

## 2. Optional Extension: Simple Rubric for Retrieval & Resolution Relevance (NFR2 / O2)

Because public support ticket datasets lack explicit ground-truth relevance pairs for similar-case retrieval, structured rubric scoring on a curated evaluation sample is required to assess **Similar-Ticket Retrieval Relevance (NFR2)**. During Week 10 evaluation, each retrieved ticket in the top-3 results for the 50 benchmark queries will be scored using the following 3-point rubric:

| Rubric Score | Label | Operational Definition | Counted as Relevant in Precision@3? |
| :---: | :--- | :--- | :---: |
| **2** | **Directly Relevant** | The retrieved historical ticket addresses the same core technical issue or service request category, and its recorded resolution (having passed data-quality checks) provides a directly applicable reference or troubleshooting check for human verification. | Yes |
| **1** | **Partially Related** | The retrieved ticket belongs to the same broad category (e.g., both are network issues) or shares surface keywords, but describes a different root problem or has a generic resolution text. | No (Tracked separately for error analysis) |
| **0** | **Irrelevant / Noise** | The retrieved ticket deals with an unrelated problem, or contains unusable text that offers no triage value to the support agent. | No |

---

## Part B Reflection

**Question:** *Which evaluation criterion is most important for determining whether your AI application will be useful to its intended users? Why?*

**Reflection:**  
While **Ticket Category Classification Accuracy (NFR1)** is necessary for basic triage, **Evidence Grounding and Low-Relevance Abstention (RAI2 / FR4)** combined with **Similar-Ticket Retrieval Relevance (NFR2)** is the single most critical evaluation criterion for determining whether TicketIQ is genuinely useful to IT help-desk agents. In a real support workflow, an agent can manually select a ticket category from a dropdown menu in a few seconds; the true bottleneck is investigating the problem and finding a working fix from past incidents. However, because similarity scores only measure text closeness rather than guaranteed fix compatibility, retrieving superficial keyword matches with irrelevant resolutions - or failing to abstain when a ticket describes a novel issue - will quickly erode user trust and cause alert fatigue. Therefore, verifying that the system achieves a **Hit Rate@3 >= 80%** on matchable issues while maintaining a **>= 90% abstention rate** on unmatchable queries is what separates a trustworthy human-in-the-loop decision-support assistant from a noisy distraction.
