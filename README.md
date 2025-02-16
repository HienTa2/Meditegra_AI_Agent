# Meditegra AI Email Automation

## Overview
Meditegra AI Email Automation is an automated workflow designed to handle incoming emails related to medical billing inquiries. The system processes emails, determines whether they are client inquiries, and generates structured responses using OpenAI. The workflow integrates with Airtable for lead management and Gmail for sending email drafts.

![image](https://github.com/user-attachments/assets/f1656f57-99b6-4c3e-86c1-810561e3c52d)


## Workflow Structure
The automation consists of the following components:

### 1. **Gmail Trigger**
- Captures incoming emails to `meditegra@gmail.com`.
- Extracts key fields such as sender email, subject, and body.

### 2. **Edit Fields**
- Manually modifies extracted fields if necessary.

### 3. **AI Agent (Tools Agent)**
- Uses an AI model to determine if the email is from a potential client.
- Returns a structured JSON output with fields:
  - `isClient` (boolean) – Indicates whether the email is from a client.
  - `reasoning` (string) – Justification for classification.
  - `lead_name` (string) – Extracted name of the lead.
  - `lead_company` (string) – Extracted company name (if applicable).

### 4. **Structured Output Parser**
- Parses AI output into a well-defined schema for downstream processes.

### 5. **IF Condition Check**
- Checks `isClient` status to determine the next action:
  - If `true`, store lead details in Airtable and respond to the email.
  - If `false`, take no further action.

### 6. **Airtable Integration**
- Stores lead details (name, company, email, and reasoning) in an Airtable base for follow-ups.

### 7. **OpenAI Email Drafting**
- Uses OpenAI's GPT model to generate professional responses.
- Email responses:
  - Must be in **HTML format**.
  - Should include pricing structure and terms of service.
  - Must only mention **market rates** and exclude internal costs.
  - Must be sent only once per inquiry.

### 8. **Final IF Condition (Prevent Resends)**
- Ensures responses are not duplicated for the same inquiry.

### 9. **Gmail Draft Creation**
- Creates a draft reply in Gmail with the AI-generated response.
- Uses extracted email fields to set up the subject and recipient.

## OpenAI Email Prompt
The AI model is instructed as follows:

```
You work for a medical billing company called **Meditegra**. Your role is to respond to inquiries about our billing services by drafting a professional and friendly email.

Carefully review the context of the inquiry and write a detailed response. Only return the body of the email in **HTML format**. Do **not** include a header or closing, only the body content. The email should clearly outline **our pricing structure** and the **terms of service**. Make sure to only include **market rates** in your response—**do not** mention or reference internal vendor costs.
Do not include things like ```html.
Make sure to include all the below Pricing information.
Make sure to only send email once and not resend again the same email.

## Pricing Information for Meditegra

## General Terms
- **All-Inclusive Monthly License Fees** per rendering provider with a **12-month commitment**.  
- Unlimited users (non-licensed professionals/staff members) and unlimited locations.  
- Includes maintenance, upgrades, implementation, training, and support.  

## One-Time Enrollment Fee
- **$500** per practice (all options)

## PM Standalone (Non-Physician Roles: Lab, DME, etc.)
- **$190** per clinician/month for 100 claims/ERAs
- **$275** per clinician/month for 400 claims/ERAs
- **$715** per clinician/month for over 400 claims, charged at $0.55 per claim

## PM Standalone (Physicians and Supervising Providers)
- **$110** per provider/month for supervising provider license
- **$110** per provider/month for part-time providers (50 claims/ERAs)
- **$190** per provider/month for part-time providers (100 claims/ERAs)
- **$275** per provider/month for full-time providers (400 claims/ERAs)
- **$330** per provider/month for full-time providers with unlimited claims/ERAs

## EHR Standalone
- **$110** per provider/month for supervising provider license
- **$330** per provider/month for part-time providers (100 encounters)
- **$385** per provider/month for full-time providers (400 encounters)
- **$440** per provider/month for full-time providers with unlimited encounters
- **$495** per provider/month for full-time providers with unlimited encounters and premium features (Patient Portal, Text Reminders)

## PM+EHR Combined (for Nurse Practitioners and Physician Assistants)
- **$330** per clinician/month for part-time roles (100 claims/ERAs)
- **$385** per clinician/month for part-time roles with premium features
- **$440** per clinician/month for full-time roles (400 claims/ERAs)
- **$495** per clinician/month for full-time roles with premium features
- **$550** per clinician/month for full-time roles with unlimited claims/ERAs and premium features

## PM+EHR Combined (for MD, DPM, etc.)
- **$110** per provider/month for supervising provider license
- **$420** per provider/month for part-time providers (100 claims/ERAs)
- **$475** per provider/month for part-time providers with premium features
- **$550** per provider/month for full-time providers (400 claims/ERAs)
- **$605** per provider/month for full-time providers with premium features
- **$660** per provider/month for full-time providers with unlimited claims/ERAs and premium features

## Optional Services (Charged Per Provider)
- **$22** per provider/month for PM Denial Defender
- **$55** per provider/month for EHR Premium Patient Portal
- **$11** per provider/month for EHR Text Reminders (requires premium portal)
- **$11** per provider/month for EHR Electronic Prescription of Controlled Substances (EPCS)
- **$20** per provider/month for EHR Immunization Registry Interface
- **$1,000** one-time fee for "Download My Patient Records"

## Optional Services (Charged Per Practice)
- **$80** per practice/month for EHR Premium Document Management (includes 1,000 fax pages)
- **$0.88** per patient statement (self-printing and mailing locally is free)
- **$2.00** per claim/encounter overages on limited plans

## Interface Options
- **$60** per month for PM HL7 interface to third-party EHR or LIS (one-time setup: $1,600)
- **$60** per month for EHR HL7 interface to third-party PM/billing software (one-time setup: $1,600)
- **$50** one-time fee for SFTP ANSI Claim Daily Drop
- **$50** one-time fee for SFTP ERA Splitter

## Data Migration Options
- **$500** for Demographic Data Upload Service
- **$5,000** for "Download My Patient Records" (per practice + $2,000 per provider)

---

All our services come with an **all-inclusive monthly license fee** per provider, which includes maintenance, upgrades, implementation, training, and full support. We operate on a **12-month commitment** unless stated otherwise, ensuring you receive consistent and high-quality service.


```

## Pricing Information (Used in AI Responses)
The AI-generated responses include the following structured pricing details:

### **General Terms**
- **All-Inclusive Monthly License Fees** per rendering provider with a **12-month commitment**.
- Unlimited users (non-licensed professionals/staff members) and unlimited locations.
- Includes maintenance, upgrades, implementation, training, and support.

### **Service Packages**
- **PM Standalone (Non-Physician Roles)**: $190–$715 per clinician/month based on claims.
- **PM Standalone (Physicians and Supervising Providers)**: $110–$330 per provider/month.
- **EHR Standalone**: $110–$495 per provider/month.
- **PM+EHR Combined** (for Nurse Practitioners & Physician Assistants): $330–$550 per clinician/month.
- **PM+EHR Combined** (for MD, DPM, etc.): $110–$660 per provider/month.
- **Optional Services**: Priced per provider/practice for features like Denial Defender, EHR Premium Portal, text reminders, HL7 integration, and data migration.

## Deployment & Usage
1. Ensure the workflow is **activated**.
2. Configure Gmail API to allow automated email access.
3. Set up **Airtable API** credentials.
4. Run a test workflow using **sample emails**.
5. Review drafted responses before sending manually.

## Future Improvements
- Implement additional NLP features to enhance lead extraction.
- Improve client detection using advanced sentiment analysis.
- Automate email sending (beyond drafts) with user approval.

---
This automation significantly **reduces response time**, improves **lead tracking**, and ensures **consistent email communication** with prospective clients.

