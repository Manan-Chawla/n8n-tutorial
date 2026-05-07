# AI Market Catch Using n8n

> An Agentic Workflow Automation Project using n8n, Baserow, GNews API, and Gemini AI

---

# 📌 Project Overview

## What This Project Showcases

AI Market Catch Using n8n is a complete demonstration of modern Agentic Workflow Automation using low-code tools and AI integration.

This project showcases:

- **Interoperability**
  - Connecting multiple platforms together:
    - Baserow → Database Layer
    - GNews API → News Intelligence
    - Gemini AI → AI Processing
    - n8n → Automation Orchestrator
  - All systems communicate seamlessly using structured JSON.

- **Localhost Automation Power**
  - Demonstrates how enterprise-grade automation systems can run directly from a local machine using n8n.
  - No expensive cloud infrastructure required.

- **Smart Feedback Loops**
  - Uses n8n's Loop Over Items node to process large datasets safely.
  - Prevents API rate-limit crashes using Wait Nodes.

- **Creative AI Engineering**
  - Converts raw internet news into:
    - Structured CRM intelligence
    - Personalized sales insights
    - Beautiful HTML reports

---

# ❗ Problem This Project Solves

## 1. Information Overload

Sales and marketing professionals spend hours manually searching:

- Company updates
- Product launches
- Market activity
- Funding news

This automation removes that repetitive research work.

---

## 2. Dead CRM Data

Traditional CRMs are usually static lists of:

- Names
- Emails
- Company domains

This project transforms the CRM into a live intelligence system by constantly updating company insights from real-world news.

---

## 3. Cold Outreach Problem

Writing personalized outreach emails is difficult.

This project automatically generates:

- AI-based sales angles
- Personalized opening lines
- Context-aware conversation starters

---

# 🚀 How This Helps People

## For Sales Teams

Provides a real reason to contact leads.

Instead of:

> "Hi, just checking in..."

Users can say:

> "I noticed your company recently launched a new AI initiative in Jaipur..."

This dramatically improves engagement.

---

## For Founders & Entrepreneurs

Acts like a 24/7 AI research assistant.

Solo founders can monitor:

- Competitors
- Industry movements
- Startup launches
- AI trends

without hiring a research team.

---

## For Researchers

Automates collection and organization of:

- Market trends
- Industry updates
- Competitor activities
- Business intelligence

into a clean dashboard/reporting format.

---

# 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| n8n | Workflow Automation |
| Baserow | Database Management |
| GNews API | News Fetching |
| Gemini AI | AI Summarization & Sales Insights |
| HTML Generator | Report Generation |

---

# 📋 Prerequisites

Before building the workflow, setup the database first.

---

# STEP 1 — Create Database in Baserow

Go to:

```txt
https://baserow.io
```

## Create Database

1. Click on `+`
2. Create a new database
3. Name it:

```txt
Accounts
```

---

# STEP 2 — Create Table Structure

Create a table with the following columns:

| Column Name | Type |
|---|---|
| Company Name | Single Line Text |
| Domain | URL |
| Latest News Summary | Long Text |
| Sales Angle | Long Text |
| Last Checked | Date |

---

# STEP 3 — Import CSV Data

1. Click on the `⋮` (Three Dots)
2. Select:

```txt
Import Data
```

3. Choose:

```txt
Import from CSV
```

4. Upload the CSV file

> The GitHub CSV URL will be added later.

---

# ⚠️ Important Note

Baserow automatically creates two default rows.

Therefore:

- Your imported records may start from index `3`.

---

# ⚙️ Building the Automation Workflow

---

# A. Trigger Node

## Add Node

Click:

```txt
+
```

Search:

```txt
When clicking "Execute Workflow"
```

Select the node.

---

# B. Baserow Node — Fetch Records

## Add Node

Search:

```txt
Baserow
```

Select:

```txt
Get Many Rows
```

---

## Configure Node

| Field | Value |
|---|---|
| Credential | Connect Baserow Account |
| Resource | Row |
| Operation | Get Many |
| Database Name | Accounts |
| Table Name | Table |
| Limit | 50 |

---

# C. Loop Node

## Add Node

Search:

```txt
Loop Over Items
```

---

## Configure

| Field | Value |
|---|---|
| Batch Size | 1 |

---

## Important

The loop creates two outputs:

- First Output → Main Workflow
- Second Output → Replace Me

You only need the first output.

---

# D. HTTP Request Node — Fetch News

## Add Node

Search:

```txt
HTTP Request
```

---

## Configure Node

| Field | Value |
|---|---|
| Method | GET |
| Authentication | None |

---

## URL

```txt
https://gnews.io/api/v4/search
```

---

## Query Parameters

| Parameter | Value |
|---|---|
| q | `{{ $json["Company Name"] }}` |
| lang | en |
| country | in |
| max | 10 |
| apikey | YOUR_GNEWS_API_KEY |

---

## Settings

Go to:

```txt
Settings → Execute Once
```

Enable it.

---

# E. Gemini Node — AI Analysis

## Add Node

Search:

```txt
Gemini
```

Select:

```txt
Message a Model
```

---

## Configure Node

| Field | Value |
|---|---|
| Resource | Text |
| Operation | Message a Model |
| Model | Gemini 2.5 Flash |

---

# Gemini Prompt

```txt
You are a Professional CRM Data Analyst.

Task:
Analyze the provided news articles for the company
{{ $node["Loop Over Items"].json["Company Name"] }}.

Data:
{{ $node["HTTP Request"].json["articles"] }}

Requirements:
1. Summarize the most relevant business news from the last 24 hours into one short professional sentence.
2. Create a "Sales Angle" — a personalized opening line for a sales email that mentions this news.
3. If no relevant news is found, set both values to:
"No major updates found."

CRITICAL:
Output ONLY a valid JSON object.
Do not include markdown formatting.
Do not include explanations.

Output Format:

{
  "summary": "The summary text here",
  "angle": "The sales opening line here"
}
```

---

## Important Setting

Enable:

```txt
Simplify Output
```

---

# F. Baserow Node — Update CRM

## Add Node

Search:

```txt
Baserow
```

Select:

```txt
Update a Row
```

---

## Configure Node

| Field | Value |
|---|---|
| Credential | Same as GET |
| Resource | Row |
| Operation | Update |
| Database Name | Accounts |
| Table Name | Table |

---

## Row ID

```txt
{{ $node["Loop Over Items"].json["id"] }}
```

---

## Data Mapping

Map fields:

| Baserow Column | Gemini Output |
|---|---|
| Latest News Summary | summary |
| Sales Angle | angle |
| Last Checked | Current Date |

---

# G. Wait Node

## Add Node

Search:

```txt
Wait
```

---

## Configure

| Field | Value |
|---|---|
| Resume | After Time Interval |
| Wait Amount | 2 |
| Wait Unit | Seconds |

---

# Why Wait Node is Important

This prevents:

- API throttling
- Gemini overload
- GNews API rate-limit issues

especially while processing multiple companies.

---

# H. HTML Node — Generate Report

## Add Node

Search:

```txt
HTML
```

Select:

```txt
Generate HTML Template
```

---

## Configure Node

| Field | Value |
|---|---|
| Operation | Generate HTML Template |

---

## HTML Code

> HTML template code will be attached separately.

---

# 🔄 Final Workflow Structure

```txt
Trigger Node
    ↓
Baserow (Get Rows)
    ↓
Loop Over Items
    ↓
HTTP Request (GNews)
    ↓
Gemini AI
    ↓
Baserow (Update Rows)
    ↓
Wait Node
    ↓
HTML Generator
```

---

# 📊 Workflow Features

## Automated News Intelligence

- Fetches latest company news automatically
- Real-time market monitoring

---

## AI-Based Summarization

Gemini AI converts raw news into:

- CRM-ready summaries
- Business insights
- Personalized sales angles

---

## CRM Enrichment

Updates database automatically with:

- Latest activity
- AI-generated outreach context
- Timestamped records

---

## Scalable Architecture

Supports:

- 20+
- 50+
- 100+

companies safely using batch loops and waits.

---

# 🔐 API Services Required

| Service | Required |
|---|---|
| Baserow Account | Yes |
| GNews API Key | Yes |
| Gemini API Key | Yes |
| n8n Instance | Yes |

---

# 📈 Future Improvements

Possible upgrades:

- PDF Report Generation
- Email Automation
- Slack/Discord Alerts
- CRM Integration
- Lead Scoring System
- AI Trend Analysis
- Multi-country News Tracking

---

# 🎯 Final Outcome

This project transforms a traditional CRM into an:

# AI-Powered Live Market Intelligence System

capable of:

- Monitoring companies
- Understanding market activity
- Generating outreach ideas
- Updating databases automatically
- Producing visual reports

all with minimal human intervention.

---

# 🧠 Key Learning Outcomes

By building this project, users learn:

- API Integration
- Workflow Automation
- AI Prompt Engineering
- CRM Enrichment
- JSON Handling
- Loop Automation
- Rate Limit Management
- HTML Report Generation
- Real-world AI Automation Architecture

---

# 📌 Project Name

# AI Market Catch Using n8n

---

# 👨‍💻 Built Using

- n8n
- Baserow
- Gemini AI
- GNews API
- HTML Templates

---

# 📄 License

This project is intended for:

- Educational purposes
- Portfolio showcasing
- Automation learning
- CRM intelligence experimentation

---
