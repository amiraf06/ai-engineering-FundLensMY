# Week 1 Project Scope

**Project Name:** FundLens MY - An Evidence-Linked Assistant for Malaysian Scholarship and Education Funding Information

**Domain:** Higher Education / Malaysian Scholarships and Education Funding

## 1. Problem Statement

Malaysian students looking for education funding may need to consult guidelines published separately by different agencies, such as JPA, MARA, and PTPTN. Each agency has its own eligibility requirements, document formats, and update cycles. Students may also need to distinguish between scholarships, study loans, and loans that can be converted into scholarships. Finding and comparing the relevant information manually can be time-consuming and may lead to misunderstandings.

An AI-based solution is suitable because students ask questions in different ways, including informal Malay and English, while the answers may depend on information found across multiple sections of official documents. FundLens MY will retrieve relevant passages from selected official documents and generate answers supported by source citations. It will also be designed to abstain when the available evidence is insufficient. The system will provide information only and will not make final eligibility decisions.

## 2. Evidence of the Problem

- **Evidence 1 - Funding requirements can involve multiple conditions:** News coverage of PTPTN's Wang Pendahuluan Pinjaman (WPP) describes eligibility conditions involving the applicant's parent or guardian receiving STR and the SSPN requirement for new PTPTN applicants. This illustrates why applicants need to check the conditions associated with a funding scheme carefully.
  * **Source:** Utusan Malaysia (2025), *27,674 pelajar terima Wang Pendahuluan Pinjaman bernilai RM41.5 juta - PTPTN*.
  * **URL:** https://www.utusan.com.my/nasional/2025/09/27674-pelajar-terima-wang-pendahuluan-pinjaman-bernilai-rm41-5-juta-ptptn/

- **Evidence 2 - Education funding requirements have been publicly debated:** A Sinar Harian article reports disagreement over whether financing and scholarship requirements discourage SPM leavers from continuing their studies. The article also notes that sponsorship bodies such as JPA require high SPM academic performance. This provides context for investigating how students understand and interpret funding requirements.
  * **Source:** Sinar Harian, *KPT nafi syarat ketat biasiswa punca tidak lanjut pengajian*.
  * **URL:** https://www.sinarharian.com.my/ampArticle/264771

- **Evidence 3 - Scholarships and loans can have different financial implications:** PTPTN's Biasiswa Anak eKasih (BAeK PTPTN) initiative illustrates why funding categories and repayment conditions need to be explained clearly. The system must distinguish an ordinary study loan from a scheme that converts a loan into a scholarship for eligible recipients.
  * **Source:** RinggitPlus, *PTPTN sasar 10,000 pelajar terima Biasiswa Anak eKasih*.
  * **URL:** https://ringgitplus.com/ms/blog/berita-kewangan-peribadi/ptptn-sasar-10000-pelajar-terima-biasiswa-anak-ekasih/

- **Evidence 4 - Planned student survey:** A short Google Forms survey will be conducted among university course mates in Week 2/3 to investigate the difficulties they face when finding and understanding scholarship and education funding requirements. The findings will be used to refine the problem statement and project requirements.

## 3. Initial Related Work

### 3.1 Official Agency Portals and Helpdesks
* **Examples:** PTPTN, JPA, and MARA official websites.
* **Source:** https://www.ptptn.gov.my/
* **Description:** Official portals provide authoritative information about their respective funding schemes.
* **Limitation:** Students need to consult multiple agencies to compare different schemes. Because the information is distributed across separate websites and documents, students must identify the relevant pages and interpret the requirements themselves, while human helpdesks depend on staff availability.

### 3.2 Third-Party Education Funding Explainers
* **Example:** eCentral's PTPTN information page.
* **Source:** https://ecentral.my/ptptn/
* **Description:** Third-party explainer websites present funding information in an accessible format and help students understand basic requirements.
* **Limitation:** These are secondary sources. Their content may lag behind official agency updates and does not link each statement to a specific official document and verification date.

### 3.3 Retrieval-Augmented Generation (RAG)
* **Study:** Lewis et al. (2020), *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*.
* **Source:** https://arxiv.org/abs/2005.11401
* **Description:** The study introduces an architecture that combines information retrieval with text generation to ground responses in external knowledge sources.
* **Limitation:** Retrieval-augmented generation does not automatically guarantee factual accuracy. A system may retrieve irrelevant passages or generate claims that are not adequately supported by the retrieved evidence. FundLens MY will therefore enforce citation verification and measure unsupported claims as part of its evaluation.

## 4. Engineering Gap / Limitation

Existing approaches provide useful information but have practical limitations. Official agency portals are fragmented across different institutions, third-party explainers may not reflect the latest official requirements, and standard RAG systems can still generate unsupported claims.

FundLens MY aims to address this gap by developing a small, reproducible question-answering system for selected Malaysian education funding documents. The system will retrieve relevant passages, provide verifiable source citations, display source metadata such as dates checked, and abstain when evidence is insufficient. Its performance will be evaluated against a manually verified benchmark of questions and reference answers.

## 5. Initial Project Objectives

- **O1:** To design a document-grounded question-answering assistant for selected Malaysian scholarship and study-loan schemes using official documents.
- **O2:** To develop a retrieval-augmented generation system that provides verifiable source citations and abstains when the available evidence is insufficient.
- **O3:** To evaluate the system using a manually verified benchmark of 30-50 questions, measuring answer accuracy, citation correctness, unsupported-claim rate, retrieval relevance, and response latency.

## 6. Target Users

Malaysian students, particularly post-SPM and post-diploma students, who need to find and understand scholarship and study-loan requirements.

## 7. Data Sources

The project will initially focus on publicly accessible official documents and requirement pages from the following agencies:

- **Source 1 - JPA:** Official scholarship or sponsorship guidelines.  
  * **URL:** https://www.jpa.gov.my/ *(exact document URL to be finalised during Week 2 source audit)*

- **Source 2 - MARA:** Official education sponsorship guidelines.  
  * **URL:** https://www.mara.gov.my/ *(exact document URL to be finalised during Week 2 source audit)*

- **Source 3 - PTPTN:** Official education loan requirements and funding-scheme guidelines.  
  * **URL:** https://www.ptptn.gov.my/ *(exact document URL to be finalised during Week 2 source audit)*

**Note on data handling:** Only source URLs, metadata, and dates checked are stored in this public repository. Official copyrighted documents are not redistributed.

## 8. Comparison with EduAdvisor

- **Similar to EduAdvisor:** Both systems operate in the higher education support domain and use a document-grounded Retrieval-Augmented Generation (RAG) approach over official documents to give students instant, 24/7 cited answers while using guardrails to handle out-of-scope queries.
- **Different from EduAdvisor:** While EduAdvisor assists first- and second-year undergraduate students at a single university with course registration, prerequisites, and graduation requirements (grounded in the university's course catalogue, student handbook, and faculty programme guides), FundLens MY assists Malaysian post-SPM and post-diploma students across multiple external funding agencies (JPA, MARA, and PTPTN). FundLens MY specifically focuses on distinguishing between scholarships and study loans, tracking document check dates, and strictly abstaining when policy evidence is insufficient.

## 9. Responsible AI Risks

- **Risk 1 - Unsupported or incorrect claims (Hallucination):** The system may state an incorrect eligibility condition, SPM requirement, or application deadline, which could mislead students or cause them to miss funding opportunities. *Mitigation:* Require supporting passage evidence for every claim, evaluate citation correctness, and abstain when reliable evidence is unavailable.

- **Risk 2 - Confusing scholarships with loans (Financial Harm):** The system may blur repayment obligations or scholarship-conversion conditions (such as BAeK PTPTN), causing students to misunderstand their long-term debt commitments. *Mitigation:* Clearly label each scheme as a loan, convertible loan, or scholarship based on official documentation.

- **Risk 3 - Outdated policy information:** Funding requirements change between intake cycles, and retrieving an older guideline could produce invalid advice. *Mitigation:* Record document publication dates and source-check dates, prioritize current official sources, and warn users when document currency cannot be verified.

- **Risk 4 - Incomplete source coverage (Fairness / Scope Risk):** The initial document collection only covers selected major schemes (JPA, MARA, PTPTN) and omits smaller state or corporate scholarships, which may lead users to assume no other aid exists. *Mitigation:* Explicitly state the covered agencies in the interface and direct users to official portals for final confirmation.