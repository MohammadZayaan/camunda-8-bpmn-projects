# Camunda 8 BPMN Workflow Projects

**Author:** Mohammad Zayaan

This repository contains two BPMN workflow diagrams designed in **Camunda 8 Modeler**. They demonstrate workflow orchestration using swimlanes, user tasks, service tasks, gateways, timer events, message tasks, and failure-handling paths.

---

## 1. Student Project Approval System

### Overview

The Student Project Approval System models the submission, validation, review, approval, and faculty-guide allocation process for a student project proposal.

**Lanes and responsibilities**
- **Student / Team:** Submits the proposal and corrects proposal details when required.
- **Project Coordinator:** Screens the proposal for feasibility and handles clarification.
- **Review Committee / HoD:** Evaluates proposals and makes the committee decision.
- **Faculty Guide:** Confirms availability and accepts the allocation.
- **Project Management System:** Validates forms and team rules, records validation failures, and checks topic similarity.

### Workflow

1. **Proposal submission and deadline handling**
   - The student/team submits a project proposal.
   - A submission-deadline timer supports late-submission handling.
   - The process checks whether late submission is approved. If it is not approved, the proposal reaches `Proposal Lapsed`.

2. **Form and team validation**
   - The system checks form completeness. If incomplete, the student/team corrects the proposal details and resubmits it.
   - Team rules and proposal requirements are validated.
   - Invalid team/proposal details are recorded, the team is notified, and the process ends at `Invalid Team`.

3. **Feasibility screening and clarification**
   - The Project Coordinator screens the proposal for feasibility.
   - If screening does not pass, the proposal is returned for clarification and resubmission.
   - Proposals that pass screening continue to the review stage.

4. **Topic similarity check**
   - The system checks for a duplicate or similar topic.
   - If a duplicate is detected, the topic is reviewed and the process checks whether a topic change is required.
   - A required change returns the proposal to the student/team for correction; otherwise, it continues to committee evaluation.

5. **Committee decision**
   - The Review Committee evaluates the proposal.
   - The decision gateway separates the outcomes:
     - **Approved:** Continue to faculty-guide allocation.
     - **Revision required:** Return the proposal for revision.
     - **Rejected:** Notify the team and end at `Proposal Rejected`.

6. **Faculty-guide allocation**
   - A faculty guide is assigned and availability is confirmed.
   - If the guide is unavailable, a timer allows time for a response before manual reassignment.
   - The process then sends an allocation request, receives guide acceptance, records the allocation, and notifies the team.

### Paths demonstrated

- **Happy path:** Proposal submission → validation → feasibility screening → topic check → committee approval → guide allocation and notification.
- **Failure/exception paths:** Late submission and lapsed proposal, incomplete form, invalid team, failed screening and clarification, duplicate-topic review and revision, committee rejection, and unavailable guide with timer-based follow-up and reassignment.

---

## 2. Loan Origination & Approval System

### Overview

The Loan Origination & Approval System models a loan application from initial submission through document validation, identity and financial checks, fraud screening, risk assessment, and a loan decision.

**Lanes and responsibilities**
- **Applicant:** Submits the application and missing documents and participates in offer acceptance and agreement signing.
- **Loan Officer:** Checks documents, verifies income/employment, and handles manual checks and identity follow-up.
- **Underwriter / Credit Committee:** Calculates debt-to-income ratio, evaluates credit risk and eligibility, and reviews the loan.
- **Fraud & Compliance Team:** Handles KYC-related work and investigates suspicious applications.
- **Credit Bureau:** Provides the credit score and participates in retry handling when a request fails.
- **Operations / Disbursement:** Generates offers and sends applicant notifications.
- **Core Banking System:** Represents system-side loan processing and applicant-acceptance messaging.

### Workflow

1. **Application submission and document completeness**
   - The applicant submits a loan application.
   - The Loan Officer checks document completeness.
   - If documents are missing, the applicant is asked to submit them.
   - A timer on the missing-document submission task leads to `Application Lapsed` if the required documents are not submitted in time.

2. **Parallel verification checks**
   - Once documents are complete, the process starts verification activities in parallel:
     - Verify income and employment.
     - Verify identity through KYC.
     - Retrieve the credit score from the Credit Bureau.
   - A parallel gateway synchronizes the required verification paths before the process continues.

3. **Credit bureau failure handling**
   - A boundary error event on `Retrieve Credit Score` routes failures to `Retry Credit Score Request`.
   - The `Retries Remaining?` gateway determines whether another attempt is made.
   - If retries are exhausted, the process routes to `Manual Credit Check`, which has a timer-based hold path ending at `Application On Hold`.

4. **Identity verification and manual handling**
   - The process checks whether identity verification succeeded.
   - Identity issues can be routed to `Manually Verify Identity`.
   - If the identity issue remains unresolved, the applicant is notified and the application reaches `Application Rejected`.

5. **Fraud and AML screening**
   - A fraud/AML flag can escalate the application to a `Fraud Investigation` subprocess.
   - The team investigates the suspicious application and evaluates whether fraud is confirmed.
   - A confirmed case records a compliance incident and follows the termination path.
   - If fraud is not confirmed, the process uses `Clear Fraud Flag` as the clearance activity.

6. **Underwriting and loan eligibility**
   - The process calculates the debt-to-income ratio and then calculates credit risk and eligibility.
   - The `Loan Eligibility Decision` gateway branches into the modeled loan-decision outcomes.

7. **Loan offer and counter-offer handling**
   - The diagram includes loan-offer generation and sending, a counter-offer path, applicant review/acceptance, and loan-agreement signing.
   - The `Counter-Offer Accepted?` gateway distinguishes acceptance from the `Counter-Offer Declined` end event.
   - A rejection notification leads to `Loan Application Rejected`.

### Paths demonstrated

- **Happy path:** Application submission → complete documents → parallel verification → underwriting → loan decision → loan offer and applicant acceptance.
- **Failure/exception paths:** Missing documents and application lapse, credit bureau errors and retries, manual credit check and hold, identity failure and rejection, fraud investigation and compliance termination, and counter-offer decline.

---

## BPMN Elements Used

The diagrams illustrate:
- Start and end events
- User tasks, service tasks, and business rule tasks
- Send and receive tasks
- Exclusive gateways for decisions
- Parallel gateways for concurrent verification
- Timer events for deadlines and waiting periods
- Boundary error events for credit bureau failure handling
- Escalation events and subprocesses for fraud investigation
- Sequence flows between tasks and lanes

## Files

- `Mohammad Zayaan_StudentProjectApproval.bpmn` — Student Project Approval System
- `Mohammad Zayaan_Loan Origination & Approval System.bpmn` — Loan Origination & Approval System

