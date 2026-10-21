# TicketIQ: Project Framing, Application Scope, and Requirements Specification

**Version:** 1.0  
**Status:** Week 2 Engineering Specification  

---

## Section 0 - Project Framing & Application Scope Checkpoint

| Framing Element | TicketIQ Specification |
| :--- | :--- |
| **Problem + Evidence** | IT support staff must manually read incoming tickets, assign categories, and search historical records for relevant past fixes, which takes time and risks inconsistent categorisation. **Evidence:** (1) Al-Hawari and Barham (2021) demonstrated in a help-desk study that incorporating ticket descriptions and comments improved machine-learning classification accuracy from 53.8% to 81.4% in their experimental setup [1]. (2) A public Microsoft/Endava ITSM proof-of-concept documents automated ticket classification across approximately 50,000 labelled support tickets as a practical enterprise workflow [2]. |
| **Existing Approaches** | 1. **Al-Hawari & Barham (2021) [1]:** Applies supervised machine learning to classify help-desk tickets within an IT service management workflow.<br>2. **Microsoft / Endava ITSM Proof-of-Concept [2]:** Demonstrates text preprocessing, multi-field ticket classification, and web-service deployment.<br>3. **Atlassian Jira Service Management AI [3]:** Uses work-item context and similar historical items to suggest steps for service requests and probable root causes for incidents.<br>4. **ServiceNow Priority Lookup Rules [6]:** Computes incident priority deterministically from configured impact and urgency matrices. |
| **Project / Engineering Gap** | Existing public proof-of-concepts focus primarily on standalone category prediction [1, 2], while commercial tools generate black-box suggestions that cannot be easily audited at the source-record level [3]. The practical engineering gap for TicketIQ is building and evaluating a transparent, inspectable prototype on public data that combines category prediction with similar-case retrieval, displays recorded resolutions only when match relevance is sufficient, and abstains when historical evidence is weak. |
| **Objective O1** | **To develop** and evaluate a baseline machine-learning classification pipeline that predicts support-ticket categories from initial ticket text. |
| **Objective O2** | **To develop** a similar-ticket retrieval capability that surfaces matching historical tickets alongside their source identifiers, similarity scores, and usable recorded resolutions. |
| **Objective O3** | **To evaluate** classification performance, retrieval relevance, and abstention behaviour when no sufficiently relevant historical ticket exists in the knowledge base. |
| **Intended Users** | Tier-1 IT help-desk agents and IT support triage teams reviewing incoming support tickets. |
| **Supported Tasks** | 1. Predicting the primary category of a newly submitted support ticket from its subject and description.<br>2. Retrieving the top matching historical tickets for reference.<br>3. Displaying the recorded historical resolution and source ticket ID when similarity meets a defined threshold.<br>4. Flagging low-confidence or unmatched queries for manual human triage. |
| **Information / Knowledge Domain** | Public IT support and customer support ticket datasets (specifically audited subsets of the Kaggle Customer Support Ticket Dataset [4] and/or Microsoft/Endava ITSM dataset [2] containing ticket subjects, descriptions, category labels, and historical resolution records). |
| **Operating Context** | Interactive human-in-the-loop decision-support prototype used during initial ticket triage. The human agent reviews the predicted category and historical references before taking action in the ticketing workflow. |
| **Important Constraints** | 1. **Data Quality Constraint:** Public datasets may contain noisy, generic, or synthetic resolution text; resolution display depends on passing Week 3-4 data quality audits.<br>2. **Leakage Constraint:** Only fields available at ticket creation time (subject and initial description) may be used as inputs.<br>3. **Budget & Compute Constraint:** Must run within student prototype constraints (Google Colab / local CPU environment) without paid proprietary enterprise APIs. |
| **Explicit Out-of-Scope Tasks** | 1. Automatically closing tickets, executing scripts, or modifying production IT systems.<br>2. Generating or inventing unrecorded troubleshooting steps when historical data lacks a valid match.<br>3. Overriding organisational deterministic priority rules (Impact x Urgency) or automatically downgrading critical incidents.<br>4. Processing non-textual event logs without ticket descriptions (e.g., UCI Incident Event Log [5]). |
| **Evaluation Focus** | Later evaluation must determine: (1) whether initial ticket text supports reliable multi-class category prediction across imbalanced classes, (2) whether retrieved historical tickets and their recorded resolutions are genuinely relevant to incoming queries, and (3) whether the system reliably abstains from suggesting resolutions when match similarity is below the threshold. |

---

## Section 1 - Functional Requirements

| # | Requirement - The system SHALL | Scope Boundary - SHALL NOT (where relevant) |
| :--- | :--- | :--- |
| **FR1** | The system shall predict a primary ticket category and associated confidence score from an incoming ticket's initial subject and description text. | The system shall not use post-resolution fields, closing notes, or fields unavailable at initial ticket submission time to predict categories. |
| **FR2** | The system shall retrieve and rank the top-*k* (e.g., top 3) most similar historical tickets from the indexed ticket repository for a given incoming ticket query. | The system shall not retrieve records from unverified external web sources outside the audited historical ticket repository. |
| **FR3** | The system shall display the historical ticket identifier, similarity score, and recorded resolution text for retrieved tickets that meet or exceed the defined relevance threshold. | The system shall not invent, extrapolate, or synthesize new troubleshooting instructions that are not explicitly present in the retrieved source ticket. |
| **FR4** | The system shall abstain from displaying resolution recommendations and output an explicit "No sufficiently similar historical case found - manual triage required" notice when all retrieved candidates fall below the relevance threshold. | The system shall not force a best-guess resolution display when similarity scores fall below the minimum threshold. |
| **FR5** | The system shall reject empty, non-textual, or out-of-domain inputs and prompt the support agent to provide a valid support ticket description. | The system shall not automatically assign incident severity, downgrade critical tickets, or execute automated ticket closure. |

---

## Section 2 - Non-Functional Requirements

| # | Quality Attribute | Requirement | Measurement Method | Metric | Threshold |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **NFR1** | **Correctness (Classification)** | Category predictions on incoming support tickets shall achieve acceptable classification performance across potentially imbalanced ticket classes. | Evaluate classification model outputs on a held-out, deduplicated test split (minimum 15-20% of dataset) and compare against a majority-class baseline. | Macro-F1 score | Macro-F1 >= 0.70 (and >= 15 percentage points above majority baseline) |
| **NFR2** | **Correctness (Retrieval Relevance)** | Retrieved historical tickets presented to the support agent shall be topically and operationally relevant to the incoming ticket query. | Evaluate top-3 retrieved results across a curated evaluation set of 50 test queries using a defined 3-point relevance rubric (0 = irrelevant, 1 = partially related, 2 = directly relevant). | Precision@3 and Hit Rate@3 | Hit Rate@3 >= 80%; Precision@3 >= 0.65 |
| **NFR3** | **Performance (Latency)** | The system shall return both the predicted ticket category and retrieved similar cases within an interactive response window suitable for live triage. | Measure end-to-end execution latency across a benchmark workload of 100 sequential test queries in the target runtime environment. | 90th percentile (p90) end-to-end latency | p90 latency < 2,000 ms (2.0 s) |
| **NFR4** | **Maintainability** | New resolved historical tickets shall be integrable into the retrieval knowledge index without retraining the classification model or modifying application interface code. | Execute a controlled index-update test by appending a batch of 100 newly formatted resolved ticket records to the retrieval store. | Index update completion time and code modification count | Update time < 5 minutes; 0 application code changes required |
| **NFR5** | **Cost** | Prototype inference and retrieval operations shall remain within zero-budget academic compute constraints. | Log compute and external API expenditure across a full 500-query evaluation benchmark run. | Total monetary cost per 100 queries | US$0.00 (fully local/Colab open-source execution) |

---

## Section 3 - Responsible AI Requirements

| # | Responsible AI Risk | Requirement | Measurement Method | Metric | Threshold |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **RAI1** | **Privacy (PII Exposure)** | The system shall strip or mask Personal Identifiable Information (names, email addresses, phone numbers, and IP addresses) during data preprocessing and shall never expose raw PII in retrieved ticket displays. | Run an automated privacy test suite injecting 50 synthetic ticket queries and inspecting 100 indexed historical records containing seeded PII patterns. | Number of exposed raw PII instances in indexed data or UI output | 0 exposed PII instances (100% redaction on standard regex/NER patterns) |
| **RAI2** | **Transparency & Grounding (Unsafe Resolution Reference)** | Every displayed historical resolution shall be accompanied by its verifiable source Ticket ID and similarity score, and the system shall abstain from suggesting resolutions when query similarity is below the threshold. | Evaluate system responses on an evaluation set comprising 30 supported in-domain queries and 20 unmatchable/out-of-scope queries. | Source ID citation presence rate; Abstention success rate on unmatchable queries | 100% source ID presence on displayed resolutions; >= 90% abstention rate on unmatchable queries |
| **RAI3** | **Fairness / Class Imbalance Risk** | Classification evaluation shall audit performance across all individual ticket categories to ensure rare or critical incident classes are not systematically neglected. | Compute per-class precision, recall, and F1 scores on the held-out test split across all categories with at least 30 test instances. | Minimum per-class recall and per-class metric reporting completeness | Per-class recall >= 0.55 for all supported classes; 100% of classes reported in evaluation logs |
| **RAI4** | **Data Integrity (Data Leakage Prevention)** | The classification and retrieval pipelines shall exclude post-submission fields from model inputs and eliminate duplicate ticket records prior to train/validation/test splitting. | Run an automated pre-training audit script checking input feature schemas and calculating exact/near-duplicate text overlap across splits. | Count of post-submission input features; Train-test duplicate leakage rate | 0 post-submission features used as input; 0% duplicate overlap across train and test splits |

---

## Part A Reflection

**Question:** *Which Responsible AI requirement in your project will be the most difficult to measure or enforce technically? Why?*

**Reflection:**  
The most technically challenging Responsible AI requirement to enforce and measure is **RAI2 (Transparency & Grounding / Abstention on Unsafe Resolution References)**, closely followed by unstructured PII redaction in **RAI1**. While attaching a source Ticket ID to a retrieved record is straightforward, calibrating the **abstention threshold** for similar-ticket retrieval is inherently difficult. In support-ticket text, two tickets may share high lexical or semantic similarity (for example, both mentioning "VPN connection timeout error on Windows 11") while having completely different underlying root causes and incompatible fixes. Consequently, a simple cosine similarity score does not guarantee that a historical resolution is safe or applicable. Furthermore, public support ticket datasets rarely provide ground-truth "unmatchable" query sets or explicit pair-wise relevance labels. Measuring abstention accuracy therefore requires constructing a dedicated evaluation set containing both valid matches and adversarial/novel ticket queries, and tuning the similarity threshold to balance false positive suggestions (showing an irrelevant fix) against false abstentions (hiding a genuinely useful historical case).

---

## References

[1] F. Al-Hawari and H. Barham, "A machine learning based help desk system for IT service management," *Journal of King Saud University - Computer and Information Sciences*, vol. 33, no. 6, pp. 702-718, 2021.  
[2] Microsoft / Endava, "ITSM ticket classification proof-of-concept repository," GitHub. https://github.com/pourabkarchaudhuri/itsm-ticket-classification  
[3] Atlassian Support, "About suggestions on work items," Jira Service Management Cloud Documentation. https://support.atlassian.com/jira-service-management-cloud/docs/about-suggestions-on-issue-view/  
[4] Suraj, "Customer Support Ticket Dataset," Kaggle. https://www.kaggle.com/datasets/suraj520/customer-support-ticket-dataset  
[5] C. Amaral, M. Fantinato, and S. Peres, "Incident management process enriched event log," UCI Machine Learning Repository. https://archive.ics.uci.edu/dataset/498/incident+management+process+enriched+event+log  
[6] ServiceNow, "Define priority lookup rules," ServiceNow Product Documentation. https://www.servicenow.com/docs/r/it-service-management/incident-management/def-prio-lookup-rules.html