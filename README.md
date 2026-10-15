# FundLens MY — An Evidence-Linked Assistant for Malaysian Scholarship and Education Funding Information

> FundLens MY is a document-grounded question-answering assistant that helps Malaysian students find scholarship and study-loan information, answering only from official documents, citing its sources, and declining to answer when the evidence is insufficient.

## Problem Statement
Malaysian students looking for education funding must read guidelines published separately by different agencies (e.g. JPA, MARA, PTPTN). Each agency has its own eligibility rules, document format and update cycle, and some schemes blur the line between loans and scholarships. Reading and comparing these documents manually is slow and error-prone.

AI engineering is suitable here because student questions are written in free-form, mixed Malay/English language, and the answer often has to be assembled from several sections of a document. FundLens MY retrieves the relevant passages from official documents and generates an answer that is restricted to those passages, with a citation for every claim. It aims to reduce unsupported claims and to measure how often they still occur. It provides information only and does not make final eligibility decisions.

## Evidence of the Problem
- **Evidence 1 — Funding rules involve several conditions and change over time.** News coverage of PTPTN's Wang Pendahuluan Pinjaman (advance loan money) reports that eligibility depends on additional conditions such as the parent or guardian receiving STR, and that new PTPTN applicants are expected to hold an SSPN savings account. A student must track conditions like these across separate announcements and documents.
  Source: Utusan Malaysia (2025), *27,674 pelajar terima Wang Pendahuluan Pinjaman bernilai RM41.5 juta – PTPTN*. https://www.utusan.com.my/nasional/2025/09/27674-pelajar-terima-wang-pendahuluan-pinjaman-bernilai-rm41-5-juta-ptptn/
- **Evidence 2 — Funding requirements are publicly debated as a barrier for school leavers.** A student union president claimed that strict financing conditions discourage SPM leavers from continuing to higher education, while the Minister of Higher Education disputed this. The same article notes that JPA sponsorship expects very high SPM results. The disagreement shows that students find the requirements difficult to interpret.
  Source: Sinar Harian, *KPT nafi syarat ketat biasiswa punca tidak lanjut pengajian*. https://www.sinarharian.com.my/ampArticle/264771 (publication date to be confirmed)
- **Evidence 3 — Loan and scholarship categories overlap.** PTPTN offers BAeK PTPTN, which converts the loan of eligible students into a scholarship that does not need to be repaid. A system that answers funding questions must therefore keep loan and scholarship information clearly distinguished.
  Source: RinggitPlus, *PTPTN sasar 10,000 pelajar terima Biasiswa Anak eKasih*. https://ringgitplus.com/ms/blog/berita-kewangan-peribadi/ptptn-sasar-10000-pelajar-terima-biasiswa-anak-ekasih.html
- **Evidence 4 (planned) — Short student survey.** A small Google Form survey with course mates on how hard it is to find and understand funding requirements. Results will be added in Week 4 or 5.

## Initial Related Work / Existing Approaches
1. **Official agency portals and helpdesks (e.g. PTPTN, JPA, MARA).**
   Source: https://www.ptptn.gov.my/
   Key limitation: Authoritative, but each agency covers only its own schemes, so students must search several portals, and human helpdesks depend on staff availability.
2. **Third-party explainer and aggregator sites.**
   Source (example): https://ecentral.my/ptptn/
   Key limitation: Convenient to read and bring information together, but they are secondary sources whose content may lag behind official updates and do not link each statement to a specific official document and date.
3. **General-purpose AI assistants and Retrieval-Augmented Generation (RAG).**
   Source: Lewis et al. (2020), *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*. https://arxiv.org/abs/2005.11401
   Key limitation: General assistants are flexible but are not guaranteed to use current official documents. RAG grounds generation in retrieved text, but it does not by itself guarantee that the extracted information is accurate or that each citation really supports the claim.

## Engineering Gap / Limitation
Existing approaches are either fragmented across agencies, secondary and possibly outdated, or not guaranteed to be grounded in official documents. What remains unresolved is a small, reproducible retrieval system for Malaysian funding documents that attaches verifiable citations to its answers, abstains when evidence is insufficient, and is evaluated against a manually verified test set, so that unsupported claims can be reduced and measured.

## Project Objectives
- **O1:** To design a document-grounded assistant for Malaysian scholarship and study-loan information based on official documents.
- **O2:** To develop the assistant using retrieval-augmented generation, citation verification, and guardrails that make it abstain when evidence is insufficient.
- **O3:** To evaluate the assistant using 30–50 benchmark questions, measuring retrieval relevance, information accuracy, citation correctness, unsupported-claim rate, and response latency.

## Target Users
Malaysian students looking for post-SPM or post-diploma scholarship and study-loan information.

## Data Sources
- **Source 1:** JPA scholarship / sponsorship guideline — https://www.jpa.gov.my/ *(replace with the exact guideline URL after source audit)*
- **Source 2:** MARA education sponsorship guideline — https://www.mara.gov.my/ *(replace with the exact guideline URL after source audit)*
- **Source 3:** PTPTN education loan requirements — https://www.ptptn.gov.my/ *(replace with the exact requirements page URL after source audit)*

Only source URLs, metadata, and dates checked are stored in this repository. Official documents are not redistributed. The final document list is confirmed during the Week 2 source audit.

## Weekly Progress
| Week | Focus | Deliverable | Status |
|------|-------|-------------|--------|
| W1 | System map + project scope | README + docs (`week1_observation_sheet.md`, `week1_system_map.png`, `week1_project_scope.md`) | In progress |
| W2 | Source audit, evidence, requirements | Final document list, survey results, evaluation criteria | Planned |

## Disclaimer
FundLens MY provides information extracted from official documents and is not a substitute for the agencies themselves. Users should always confirm requirements and deadlines on the official portals before applying.
