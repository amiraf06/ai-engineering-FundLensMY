# TicketIQ: AI-Assisted Support Ticket Triage and Similar-Case Retrieval

**Status:** Initial project proposal — scope and dataset still need validation.

## 1. Project Name and Domain

**TicketIQ** is an AI Engineering project in the IT support / IT Service Management (ITSM) domain. It will help support staff review incoming tickets and find useful historical cases.

## 2. Problem Statement

Support staff may need to read each incoming ticket, assign a category, and search old tickets for relevant fixes. This can take time and may lead to inconsistent categorisation. Research and existing tools show that automatic ticket classification and AI-assisted support are practical areas to investigate [1–3]. However, the size of this problem in a particular organisation has not been measured for this project; it is the problem hypothesis to test.

TicketIQ will be a decision-support prototype, not an autonomous IT operator. It will classify ticket text, retrieve similar historical tickets, and show recorded resolutions only when the available data supports them.

## 3. Evidence of the Problem

- **Research evidence:** Al-Hawari and Barham describe a machine-learning help-desk system for ticket classification. In their reported experiment, including ticket comments and descriptions improved prediction accuracy from 53.8% to 81.4%. This is a result from their study, not a performance claim for TicketIQ [1].
- **Engineering example:** A public Microsoft/Endava proof-of-concept reports a help-desk ticket-classification task and describes around 50,000 labelled support tickets used for the project. It is an example of an existing workflow, not a survey proving that every support team has the same problem [2].
- **Existing commercial capability:** Jira Service Management documents AI suggestions based on work-item context and similar past items, including possible steps for service requests and probable root causes for incidents. This confirms that similar capabilities already exist; TicketIQ must not claim to be the first system of its kind [3].

## 4. Initial Related Work

| Related work / solution | What it already does | Limitation relevant to this project |
|---|---|---|
| Al-Hawari & Barham, *A machine learning based help desk system for IT service management* (2021) [1] | Uses machine learning to classify help-desk tickets and describes a help-desk workflow. | The paper's described classification results do not by themselves establish that TicketIQ's retrieval and historical-resolution display will be reliable. TicketIQ must evaluate those functions separately. |
| Microsoft / Endava ITSM ticket-classification proof-of-concept [2] | Demonstrates text preparation, classification of ticket fields, and a web-service deployment approach. | Its documented focus is automatic classification; TicketIQ will specifically test whether retrieved historical cases and their recorded resolutions are relevant. |
| Atlassian Jira Service Management AI suggestions [3] | Uses work-item context and past similar work items to suggest steps or probable root causes. | Atlassian documents that suggestions cannot be manually configured or influenced by knowledge-base content. TicketIQ's prototype will instead show the matching historical tickets and their source records so the user can inspect the evidence. This is a design difference to evaluate, not a claim that TicketIQ is better. |
| ServiceNow priority lookup rules [6] | Calculates ticket priority from configured impact and urgency rules. | This shows why a priority model could be misleading if it merely learns a rule-based label from impact and urgency. Priority prediction will be optional and used only if the dataset and evaluation design justify it. |

## 5. Engineering Gap / Limitation

The project does **not** claim that no similar systems exist. Its practical goal is to build and evaluate a small, transparent prototype using public data that combines:

1. Ticket-category prediction from text.
2. Retrieval of similar historical tickets.
3. Display of the recorded resolution and source ticket when the match is sufficiently relevant.

The engineering question is whether this workflow provides useful, inspectable references on the selected public dataset. If the dataset's resolution field is unreliable, TicketIQ will limit the prototype to classification and similar-ticket retrieval rather than inventing or generating fixes.

## 6. Project Objectives

- **O1:** Develop and evaluate a baseline machine-learning model that classifies support tickets from their text.
- **O2:** Build a similar-ticket retrieval feature that shows the matching historical tickets and any usable recorded resolutions.
- **O3:** Evaluate classification quality and retrieval relevance, including cases where the system should say that it cannot find a sufficiently relevant match.

**Optional extension:** Evaluate priority prediction only if the selected data contains reliable priority labels and the experiment avoids label leakage. It is not required for the first working version.

## 7. Target Users

- IT help-desk agents.
- IT support teams reviewing and categorising incoming tickets.

The system is intended to assist a human support agent. It will not automatically close incidents, change production systems, or apply a suggested fix.

## 8. Data Sources

The data sources below are publicly accessible candidates. The dataset files must be inspected and their reuse terms checked before they are used in the final experiment.

1. **Customer Support Ticket Dataset — Kaggle**  
   URL: https://www.kaggle.com/datasets/suraj520/customer-support-ticket-dataset  
   The uploader describes more than 8,000 tickets with fields such as subject, description, ticket type, priority, and resolution [4]. The inspected public description does not establish that these are authentic organisational ITSM records, and a public comment questions the usefulness of the resolution field [4]. Therefore, first inspect the CSV, missing values, duplicates, label distributions, resolution quality, and license. Remove personal fields such as names and emails before modelling. Use this dataset for resolution retrieval only if its recorded resolutions pass the audit.

2. **Microsoft / Endava ITSM ticket-classification project**  
   URL: https://github.com/pourabkarchaudhuri/itsm-ticket-classification  
   Its README describes around 50,000 labelled support tickets from an internal help-desk source, anonymised for the public proof-of-concept, and demonstrates classification tasks [2]. Confirm current download availability and data reuse terms before using the data. The public project description does not establish that it contains reliable resolution text for TicketIQ's retrieval feature.

3. **Incident Management Process Enriched Event Log — UCI Machine Learning Repository**  
   URL: https://archive.ics.uci.edu/dataset/498/incident+management+process+enriched+event+log  
   UCI reports 24,918 incidents, 141,712 events, and a CC BY 4.0 licence. Crucially, UCI states that textual-information attributes are not included [5]. This dataset is therefore **not suitable as TicketIQ's main text-classification or similar-ticket dataset**. It can be considered only for separate structured incident-process analysis; it should not be merged casually with the text datasets.

## 9. Comparison with EduAdvisor

**Similarities:** Both are domain-specific AI applications with a defined user group, explicit scope boundaries, evaluation criteria, and Responsible AI considerations. Both should avoid unsupported outputs and provide evidence that users can inspect.

**Differences:** EduAdvisor answers academic questions from approved university documents. TicketIQ analyses support-ticket text, predicts ticket categories, and retrieves similar historical tickets. Its main evidence will be the input ticket and the retrieved historical records, rather than university document passages.

## 10. Responsible AI Risks

1. **Privacy:** Public support-ticket data may contain names, emails, or other personal information. Inspect and remove personal fields, avoid using them as model inputs, and do not expose them in search results.
2. **Incorrect or unsafe resolution references:** A similar ticket may not have the same cause, and an old fix may be unsuitable. Show historical cases as references, include their identifiers or source links where available, show a relevance score, and ask a human to verify the proposed fix. If no sufficiently relevant case is found, return no resolution suggestion.
3. **Class imbalance and prioritisation risk:** Rare but serious categories may be missed. Report per-class precision/recall or macro-F1 and do not let the prototype automatically decide or downgrade a critical incident.
4. **Data leakage:** Do not use post-resolution text or fields unavailable when a new ticket arrives as input for predicting its initial category. Deduplicate before splitting data. If testing priority prediction, check that the model is not merely reproducing a deterministic impact/urgency lookup [6].

## References

[1] F. Al-Hawari and H. Barham, “A machine learning based help desk system for IT service management,” *Journal of King Saud University – Computer and Information Sciences*, 2021. https://doi.org/10.1016/j.jksuci.2019.04.001

[2] Microsoft / Endava proof-of-concept repository, “ITSM ticket classification.” https://github.com/pourabkarchaudhuri/itsm-ticket-classification

[3] Atlassian Support, “About suggestions on work items.” https://support.atlassian.com/jira-service-management-cloud/docs/about-suggestions-on-issue-view/

[4] Suraj, “Customer Support Ticket Dataset,” Kaggle. https://www.kaggle.com/datasets/suraj520/customer-support-ticket-dataset

[5] C. Amaral, M. Fantinato, and S. Peres, “Incident management process enriched event log,” UCI Machine Learning Repository. https://archive.ics.uci.edu/dataset/498/incident+management+process+enriched+event+log

[6] ServiceNow documentation, “Define priority lookup rules.” https://www.servicenow.com/docs/r/it-service-management/incident-management/def-prio-lookup-rules.html
