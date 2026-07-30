# Research Notes: Problem Statement 1 (PS1)

## Overview & Definition

* **Statement:** *"AI Rural Health Assistant for multilingual healthcare access and government health scheme guidance."*
* **Core Classification:** Solution category disguised as a problem statement.
* **Primary Critique:** Broad, unfocused, and bundles two distinct sub-problems with conflicting user requirements.

---

## 1. Decoding & Scope Analysis

### Deconstructed Sub-Problems

* **Problem A (Healthcare Access):** Citizens/workers struggling to get timely, reliable medical guidance and triage.
* **Problem B (Government Scheme Guidance):** Citizens struggling to identify eligibility, required documents, and application steps for health benefits.

### Stakeholder / Actor Conflict

The problem statement lacks a single primary user. Different actors in this ecosystem have completely different workflows and objectives:

| Actor Group | Primary Objective / Job-to-be-Done (JTBD) |
| --- | --- |
| **Rural Citizens / Pregnant Women / Elderly** | Symptom checking, nearest clinic locator, scheme eligibility lookup. |
| **ASHA Workers / ANM Nurses** | Patient registration, vaccination tracking, follow-up scheduling. |
| **PHC Doctors** | Patient medical history retrieval, rapid diagnostic assistance. |
| **Government Health Officers** | Scheme utilization tracking, reducing untreated case rates. |

> **Major Red Flag:** Attempting to serve all these actors simultaneously results in a bloated "feature buffet" rather than a focused MVP.

---

## 2. Current Workflows & Existing Workarounds

### Escalation Path for Illness

$$\text{Feel Sick} \longrightarrow \text{Family Advice} \longrightarrow \text{Local Chemist} \longrightarrow \text{Local Healer} \longrightarrow \text{ASHA Advice} \longrightarrow \text{PHC} \longrightarrow \text{District Hospital}$$

### Key Insights

* **Human-Centric Trust:** Initial healthcare queries rely heavily on local human networks (family, local pharmacists, ASHA workers) rather than digital tools or search engines.
* **Existing Information Channels:** WhatsApp groups, local doctors, YouTube, and direct community engagement.

---

## 3. Critical Hidden Assumptions

1. **Assumption:** Rural citizens prefer chatting with an AI assistant.
* **Validation:** *Hypothesis / Unproven.* Trust is typically placed in local human health workers over automated interfaces.


2. **Assumption:** Target users possess and operate personal smartphones.
* **Validation:** *Partially False.* Shared household phones, feature phones, and low digital literacy create significant adoption barriers.


3. **Assumption:** Citizens can accurately describe medical symptoms in text/voice.
* **Validation:** *False.* Symptoms are often described vaguely (e.g., "stomach pain"), which reduces AI diagnostic accuracy.


4. **Assumption:** Information availability is the primary bottleneck.
* **Validation:** *Uncertain.* The true bottleneck is often physical access (distance, lack of transport, missing paperwork, or hospital staff shortages).



---

## 4. Technical & AI Necessity Evaluation

* **Health Scheme Guidance:**
* **AI Role:** Multilingual natural language understanding (NLU), voice translation, free-form Q&A, and document parsing.
* **Alternative:** Rule-based decision engines can handle deterministic eligibility lookups just as effectively.


* **Healthcare & Symptom Guidance:**
* **AI Role:** Triage assistance and initial risk assessment.
* **Risk Factor:** **High Safety Risk.** Misdiagnosis (e.g., classifying cardiac chest pain as indigestion) carries severe health consequences.
* **Requirement:** Must enforce strict **Human-in-the-Loop (HITL)** guardrails and conservative escalation protocols instead of direct autonomous diagnosis.



---

## 5. Evaluation Scorecard

| Parameter | Score (1–5) | Rationale |
| --- | --- | --- |
| **Pain Level** | **5** | Rural healthcare access and coverage are major critical issues. |
| **User Access** | **2** | Field testing and validating directly with rural end-users is difficult. |
| **Scope Clarity** | **1** | Overly broad; conflates multiple user personas and problem domains. |
| **Buildability** | **3** | High-level idea is too broad, though a narrow slice is buildable. |
| **Data Reality** | **3** | Health scheme rules are public; safe medical guidance data is harder to validate. |
| **The Edge** | **2** | Numerous health chatbots exist; distinct technological edge is unclear. |
| **Demo Moment** | **4** | Multilingual voice conversation presents well during pitches. |
| **Success Metric** | **2** | Hard to measure tangible health outcome improvements within a short timeframe. |

---

## 6. Key Recommendations & Next Steps

* **Narrow the Focus:** Choose **one primary user** (e.g., build an assistant specifically for ASHA workers to streamline field logging, rather than a generic app for citizens).
* **Decouple Features:** Separate health scheme navigation from medical symptom advice.
* **Validate the Bottleneck:** Conduct user research to confirm whether the primary friction point is **information gap** or **physical/administrative execution**.
