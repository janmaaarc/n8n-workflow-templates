# n8n Workflow Templates

Production-ready n8n automation workflows for CRM, AI-powered recruitment, marketing automation, and custom integrations. Each workflow is fully documented with setup instructions, webhook endpoints, and customization guides.

## Workflow Categories

### CRM & Sales Automation (GoHighLevel)

A complete CRM automation suite for GoHighLevel covering the entire customer lifecycle from lead capture to retention.

| Workflow | Description | Key Features |
|----------|-------------|--------------|
| [CRM Lead Management](./n8n-crm-lead-management/) | Intelligent lead scoring and lifecycle management | Lead scoring engine, lifecycle stages, GA4 + Meta tracking, Slack alerts |
| [CRM Sales Pipeline](./n8n-crm-sales-pipeline/) | Deal management and sales forecasting | Stage automation, stalled detection, weighted forecasting, activity tracking |
| [CRM Customer Onboarding](./n8n-crm-customer-onboarding/) | Post-sale retention and health monitoring | Health scoring, churn prevention, milestone tracking, feedback collection |

**CRM Suite Flow:**
```
Lead Management → Sales Pipeline → Customer Onboarding
   (Pre-sale)      (Active deals)     (Post-sale)
        ↓               ↓                  ↓
  Lead Scoring    Deal Tracking     Health Scoring
  MQL/SQL Stages  Stage Tasks       Churn Prevention
  Nurture Flows   Forecasting       Feedback Loops
```

### AI-Powered Recruitment

Automated candidate screening and interview workflows using AI for CV analysis and live interviews.

| Workflow | Description | Key Features |
|----------|-------------|--------------|
| [AI Interview Automation](./n8n-ai-interview-automation/) | Automated AI-powered candidate interviews | Oration AI integration, Brevo emails, follow-up sequences, status tracking |
| [AI CV Assessment](./n8n-ai-cv-assessment/) | Intelligent CV analysis and scoring | DeepSeek AI analysis, structured scoring, recommendation engine |

**Recruitment Suite Flow:**
```
Google Form → HR Select → CV Assessment → AI Interview → Final Decision
                              ↓               ↓
                        DeepSeek AI      Oration AI
                        Scoring 0-100    Live Interview
```

### AI & Marketing Automation

Creative automation and competitor analysis using AI.

| Workflow | Description | Key Features |
|----------|-------------|--------------|
| [AI Furniture Customizer](./ai-furniture-customizer/) | AI-powered product image generation | Gemini AI images, user auth, gallery system, FTP uploads |
| [Facebook Ad Library Scraper](./facebook-ad-scraper/) | Competitor ad analysis with AI | Apify scraping, GPT-4 analysis, Whisper transcription, Sheets output |

## Quick Start

### 1. Import Workflow
```
n8n → Workflows → Import from File → Select workflow.json
```

### 2. Configure Credentials
Each workflow requires specific credentials. Common ones include:
- **GoHighLevel** - HTTP Header Auth with API key
- **Google Sheets/Drive** - OAuth2
- **Slack** - Bot token
- **OpenAI** - API key

### 3. Update Placeholders
All sensitive values are marked with `YOUR_*` prefix:
```
YOUR_GOOGLE_SHEET_ID
YOUR_API_KEY
YOUR_CREDENTIAL_ID
YOUR_WEBHOOK_URL
```

### 4. Activate
Enable the workflow and test with sample data.

## Tech Stack

| Technology | Purpose | Used In |
|------------|---------|---------|
| **n8n** | Workflow automation platform | All workflows |
| **JavaScript** | Custom logic in Code nodes | All workflows |
| **GoHighLevel** | CRM platform | CRM Suite |
| **Google Sheets** | Data storage and tracking | All workflows |
| **Google Drive** | File storage | CV Assessment, Ad Scraper |
| **Slack** | Team notifications | CRM Suite, Ad Scraper |
| **OpenAI GPT-4** | AI text analysis | Ad Scraper |
| **OpenAI GPT-4o** | AI vision analysis | Ad Scraper |
| **OpenAI Whisper** | Audio transcription | Ad Scraper |
| **Google Gemini** | AI image generation | Furniture Customizer |
| **DeepSeek** | AI CV analysis | CV Assessment |
| **Oration AI** | AI interviews | Interview Automation |
| **Brevo** | Email delivery | Interview Automation |
| **Apify** | Web scraping | Ad Scraper |
| **GA4 + Meta** | Analytics tracking | Lead Management |

## Repository Structure

```
n8n-workflow-templates/
├── README.md                          # This file
│
├── n8n-crm-lead-management/           # CRM: Lead scoring & lifecycle
│   ├── workflow.json
│   └── README.md
│
├── n8n-crm-sales-pipeline/            # CRM: Deal management
│   ├── workflow.json
│   └── README.md
│
├── n8n-crm-customer-onboarding/       # CRM: Retention & health
│   ├── workflow.json
│   └── README.md
│
├── n8n-ai-interview-automation/       # Recruitment: AI interviews
│   ├── workflow.json
│   └── README.md
│
├── n8n-ai-cv-assessment/              # Recruitment: CV analysis
│   ├── workflow.json
│   └── README.md
│
├── ai-furniture-customizer/           # AI: Image generation
│   ├── workflow.json
│   └── README.md
│
└── facebook-ad-scraper/               # Marketing: Ad analysis
    ├── workflow.json
    └── README.md
```

## Workflow Details

### CRM Lead Management
- **Triggers:** Webhook (new leads), Schedule (hourly re-scoring), Webhook (activities)
- **Integrations:** GoHighLevel, Slack, GA4, Meta Pixel
- **Key Logic:** Multi-factor lead scoring, lifecycle stage automation, score decay

### CRM Sales Pipeline
- **Triggers:** Webhook (stage changes), Schedule (stalled detection), Cron (weekly forecast)
- **Integrations:** GoHighLevel, Slack
- **Key Logic:** Stage-based tasks, weighted pipeline, engagement scoring

### CRM Customer Onboarding
- **Triggers:** Webhook (new customers), Schedule (health monitoring), Daily (feedback)
- **Integrations:** GoHighLevel, Slack
- **Key Logic:** Health scoring, churn risk detection, milestone tracking

### AI Interview Automation
- **Triggers:** Schedule (6h for interviews, 12h for follow-ups)
- **Integrations:** Google Sheets, Oration AI, Brevo, WATI
- **Key Logic:** Eligibility filtering, tiered follow-ups, status synchronization

### AI CV Assessment
- **Triggers:** Schedule (every 6 hours)
- **Integrations:** Google Sheets, Google Drive, DeepSeek AI
- **Key Logic:** PDF extraction, multi-criteria scoring, recommendation engine

### AI Furniture Customizer
- **Triggers:** Webhook endpoints (auth, generate, save, get)
- **Integrations:** Google Sheets, Gemini AI, FTP
- **Key Logic:** User management, AI image generation, gallery system

### Facebook Ad Scraper
- **Triggers:** Slack message with Ad Library URL
- **Integrations:** Apify, OpenAI, Google Sheets/Drive, Slack, CloudConvert
- **Key Logic:** Multi-instance filtering, video/image separation, parallel AI analysis

## Common Patterns

### Webhook-based Workflows
```
Webhook Trigger → Validate Input → Process Data → Update CRM → Notify Team
```

### Schedule-based Workflows
```
Schedule Trigger → Fetch Data → Filter/Score → Update Records → Alert if needed
```

### AI Analysis Workflows
```
Get Content → Send to AI → Parse Response → Store Results → Notify
```

## Customization Guide

Each workflow README includes a **Customization** section. Common customizations:

| What | How |
|------|-----|
| Change scoring weights | Edit Code node with scoring logic |
| Modify thresholds | Update variables in filter/switch nodes |
| Add integrations | Insert new HTTP Request or app nodes |
| Change schedules | Modify Schedule/Cron trigger nodes |
| Update notifications | Edit Slack/email message nodes |

## Best Practices

1. **Test in staging first** - Clone workflows before modifying
2. **Use environment variables** - Store sensitive values in n8n settings
3. **Monitor executions** - Check n8n logs for errors
4. **Version control** - Export workflow.json after changes
5. **Document changes** - Update README when customizing

## Contributing

Feel free to fork and adapt these workflows for your use case. If you create something useful, consider sharing it back!

## Support

For issues or questions:
1. Check the individual workflow README
2. Review n8n documentation at [docs.n8n.io](https://docs.n8n.io)
3. Open an issue in this repository
