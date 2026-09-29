# Real Estate Lead Qualification Automation

An **n8n automation workflow** that automatically receives real estate inquiries, calculates a lead score using rule-based logic, classifies leads into **High, Medium, or Low priority**, and triggers the appropriate follow-up actions.

> **No AI is used in this workflow.**
> The lead qualification process is entirely based on predefined business rules and scoring criteria.

---

## Overview

Real estate companies receive many inquiries, but not every lead has the same level of buying intent.

This workflow automates the initial qualification process by:

1. Receiving a lead from a **Tally Form**
2. Processing and normalizing the submitted data
3. Calculating a lead score using predefined rules
4. Classifying the lead based on its score
5. Sending an email confirmation to the lead
6. Notifying the support/sales team for high-priority leads
7. Storing the qualified lead in **Google Sheets**

---

## Workflow Architecture

```text
Tally Form
    │
    ▼
Webhook
    │
    ▼
Edit Fields
    │
    ▼
Python Code
(Data Processing + Lead Scoring)
    │
    ▼
Switch
    │
    ├───────────────┬────────────────┐
    ▼               ▼                ▼
  High            Medium            Low
    │               │                │
    ├── Email       ├── Email        ├── Email
    │               │                │
    ├── Telegram    │               │
    │               │                │
    └── Google      └── Google       └── Google
        Sheets          Sheets           Sheets
```

---

## Lead Scoring System

The workflow calculates a score based on several lead attributes.

### Property Type

| Property Type | Score |
| ------------- | ----: |
| Apartment     |   +10 |
| Villa         |   +15 |
| Commercial    |   +10 |

### Buy or Rent

| Choice | Score |
| ------ | ----: |
| Buy    |   +15 |
| Rent   |    +5 |

### New or Resale

| Choice | Score |
| ------ | ----: |
| New    |   +10 |
| Resale |   +10 |

### First Property Purchase

| Choice | Score |
| ------ | ----: |
| Yes    |    +5 |
| No     |   +10 |

### Timeline

| Timeline       | Score |
| -------------- | ----: |
| Immediately    |   +20 |
| Within 1 month |   +15 |
| 1–3 months     |   +10 |
| 3–6 months     |    +5 |
| +6 months      |    +0 |

### Budget Range

| Budget              | Score |
| ------------------- | ----: |
| Above 1,000,000     |   +20 |
| 700,000 – 1,000,000 |   +15 |
| 400,000 – 700,000   |   +10 |
| 200,000 – 400,000   |    +5 |
| Below 200,000       |    +0 |

---

## Lead Classification

After calculating the total score, the workflow uses a **Switch node** to determine the lead priority.

```text
Score >= 75  → High
Score >= 50  → Medium
Score < 50   → Low
```

### High Priority

For high-priority leads, the workflow:

* Sends a confirmation email to the lead
* Sends a Telegram notification to the support/sales team
* Stores the lead in Google Sheets
* Marks the lead status as `High`

### Medium Priority

For medium-priority leads, the workflow:

* Sends a confirmation email
* Stores the lead in Google Sheets
* Marks the lead status as `Medium`

### Low Priority

For low-priority leads, the workflow:

* Sends a confirmation email
* Stores the lead in Google Sheets
* Marks the lead status as `Low`

---

## Technologies & Tools

* **n8n** — Workflow automation
* **Tally** — Lead collection form
* **Python** — Data processing and scoring logic
* **Google Sheets** — Lead storage
* **Gmail** — Automated email communication
* **Telegram** — Internal high-priority lead notification
* **Webhooks** — Event-driven workflow triggering

---

## n8n Nodes Used

| Node           | Purpose                                             |
| -------------- | --------------------------------------------------- |
| Webhook        | Receives the Tally form submission                  |
| Edit Fields    | Extracts the required form data                     |
| Code in Python | Converts form options and calculates the lead score |
| Switch         | Routes leads based on their score                   |
| Gmail          | Sends confirmation emails                           |
| Telegram       | Notifies the support/sales team                     |
| Google Sheets  | Stores qualified leads                              |

---

## Data Processing

Tally sends structured form data containing field IDs, labels, values, and option IDs.

The Python Code node:

1. Extracts the submitted fields
2. Converts multiple-choice option IDs into readable text
3. Maps the form responses to their corresponding labels
4. Applies the predefined scoring rules
5. Calculates the final lead score
6. Adds the score to the workflow output

Example output:

```json
{
  "Property Type": "Villa",
  "Buy or Rent": "Buy",
  "Timeline": "Immediately",
  "Budget Range": "Above 1.000.000",
  "Lead Score": 85
}
```

---

## Key Automation Concept

The main idea behind this project is to replace a repetitive manual qualification process with a deterministic automation workflow.

Instead of a salesperson manually reviewing every submitted form:

```text
Lead submits form
        ↓
Automation processes the data
        ↓
Lead receives a score
        ↓
Lead is classified
        ↓
Appropriate actions are triggered
```

This allows the sales team to focus their attention according to predefined business rules.

---

## Setup

### 1. Import the Workflow

Download the workflow JSON from this repository and import it into your n8n instance.

### 2. Configure Tally

Create a Tally form containing the required fields used by the scoring logic.

### 3. Configure Webhook

Connect the Tally form submission to the n8n Webhook endpoint.

### 4. Configure Credentials

Set up the required credentials in n8n for:

* Gmail
* Telegram
* Google Sheets

### 5. Configure Google Sheets

Create the required sheets for:

* High-priority leads
* Medium-priority leads
* Low-priority leads

### 6. Activate the Workflow

After testing the workflow with sample submissions, activate it in n8n.

---

## Example Use Case

A potential customer submits a real estate inquiry:

```text
Property Type: Villa
Buy or Rent: Buy
New or Resale: New
First Property Purchase: No
Timeline: Immediately
Budget: Above 1,000,000
```

The workflow calculates the score automatically and routes the lead according to the configured thresholds.

For a high-priority lead, the sales/support team receives an internal Telegram notification while the customer receives an automated confirmation email.

---

## Security Note

The workflow JSON included in this repository is intended as an automation template.

Before deploying it in a production environment:

* Configure your own n8n credentials
* Use your own Google Sheet
* Use your own Telegram configuration
* Configure your own webhook endpoint
* Never commit API keys, access tokens, passwords, or other secrets to GitHub

---

## Future Improvements

Possible extensions include:

* CRM integration
* Automatic sales-team assignment
* Lead tracking dashboard
* Follow-up reminders
* Duplicate lead detection
* Advanced scoring configuration
* Integration with additional communication channels

---

## Project Purpose

This project was built as a practical exercise in **workflow automation, rule-based decision making, data transformation, and business process automation using n8n**.

A key constraint of the project was to implement the qualification logic **without using AI**, which required designing the scoring and routing logic explicitly using deterministic rules.

---

## Author

**Mohamed Megahed**

AI Automation Developer | n8n Workflow Developer

* GitHub: [mohamed-me9ahed](https://github.com/mohamed-me9ahed)
* Portfolio: [Portfolio](https://mohamed-me9ahed.github.io/portfolio/)

---

## License

This project is provided for educational and portfolio purposes.
