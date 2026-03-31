# B2B Outreach Automation — AI-Powered Cold Email Pipeline

An end-to-end automated B2B outreach system built with **n8n**, **Qwen AI**, and **Instantly.ai**. The system reads leads from a Google Sheet, generates a personalized cold email for each contact using AI, and pushes every lead into an Instantly.ai campaign automatically.

---

## How It Works

```
Google Sheets (CSV) → n8n → Qwen AI → Instantly.ai Campaign
```

1. **Google Sheet import** — Fetches the lead CSV from a public Google Sheets URL
2. **CSV Parser** — Parses raw CSV into structured JSON (name, company, email, industry, country, keywords)
3. **Loop Over Items** — Processes each lead one by one
4. **Qwen AI** — Generates a personalized cold email per lead using company-specific data from Apollo keywords
5. **Code node** — Extracts email body, SHORT_REF, and CORE_ACTIVITY with regex; validates email presence
6. **Add lead to campaign** — Pushes each lead to Instantly.ai v2 API with personalization and custom variables

---

## Features

- AI-generated personalized emails per lead (only the first 1-2 lines change, the rest stays consistent for deliverability)
- Extracts `SHORT_REF` and `CORE_ACTIVITY` metadata per lead for use in follow-up sequences
- Email validation with descriptive error messages
- Duplicate protection via Instantly.ai
- Custom variables passed to Instantly for use in follow-up email templates
- Clean separation between lead sourcing, AI generation, and email delivery

---

## Tech Stack

| Tool | Role |
|------|------|
| n8n | Workflow orchestration |
| Qwen AI (Alibaba DashScope) | Email personalization |
| Instantly.ai | Email sending, warmup, sequences, tracking |
| Apollo.io | Lead sourcing and export |
| Google Sheets | Lead data hosting |

---

## Setup Instructions

### 1. Import the workflow
- Open n8n and create a new workflow
- Click the three dots menu → Import from JSON
- Paste the contents of `workflow.json`

### 2. Configure credentials

**Qwen AI:**
- In the `Qwen Ai` node, replace `YOUR_QWEN_API_KEY` with your Alibaba DashScope API key
- Get your key at: https://dashscope.aliyuncs.com

**Instantly.ai:**
- In n8n, go to Credentials → Add new → Instantly API
- Paste your Instantly API key (found in Instantly → Settings → Integrations → API)
- Assign the credential to the `Add lead to campaign` node

### 3. Set up Google Sheets
- Export your leads from Apollo.io as CSV
- Upload the CSV to Google Sheets
- Go to File → Share → Publish to web → CSV format
- Copy the published CSV URL and paste it into the `Google Sheet import` node URL field

### 4. Set your campaign ID
- In Instantly, open your campaign → three dots → Share
- Copy the ID from the URL after `?id=`
- Paste it into the `campaign` field in the `Add lead to campaign` node

### 5. Update the AI prompt
- In the `Qwen Ai` node, update the system prompt to reflect your business, product description, and email signature

### 6. Run the workflow
- Click Execute Workflow
- Monitor each node for green checkmarks
- Check Instantly dashboard to confirm leads are created

---

## Instantly Sequence Variables

Use these variables in your Instantly email sequence:

| Variable | Description |
|----------|-------------|
| `{{personalization}}` | AI-generated personalized email (use in Email 1) |
| `{{firstName}}` | Lead's first name |
| `{{short_ref}}` | Short phrase about the lead's company focus (use in follow-ups) |
| `{{company_name}}` | Lead's company name |

---

## Lead CSV Format

The workflow expects a CSV exported from Apollo.io with these columns:

```
First Name, Last Name, Title, Company Name, Email, Industry, Country, Keywords
```

---

## Notes

- The Instantly free plan supports 2 email accounts. Upgrade for higher volume.
- Run email warmup for 2-3 weeks before launching the campaign.
- Configure SPF, DKIM, and DMARC on your sending domain for best deliverability.
- Do not run the workflow twice with the same leads — Instantly handles deduplication but unnecessary API calls waste credits.

---

## Author

**Mohamed Chehata** — AI Automation Developer  
Freelancing on Upwork | [Freeness.tn](https://freeness.tn)  
Specializing in n8n workflows, AI agent pipelines, and B2B automation systems.
