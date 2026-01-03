# n8n Workflow Templates

Production-ready n8n automation workflows for AI, CRM, marketing, and recruitment tasks.

## Workflows

### CRM & Sales (GoHighLevel)

| Workflow | Description |
|----------|-------------|
| [CRM Lead Management](./n8n-crm-lead-management/) | Lead scoring, lifecycle management, GA4 + Meta tracking |
| [CRM Sales Pipeline](./n8n-crm-sales-pipeline/) | Deal stages, stalled detection, engagement scoring, forecasting |
| [CRM Customer Onboarding](./n8n-crm-customer-onboarding/) | Health scoring, churn prevention, feedback collection |

```
Lead Management → Sales Pipeline → Customer Onboarding
   (Pre-sale)      (Active deals)     (Post-sale)
```

### AI & Automation

| Workflow | Description |
|----------|-------------|
| [AI Furniture Customizer](./ai-furniture-customizer/) | Generates AI furniture images with custom materials using Gemini |
| [Facebook Ad Library Scraper](./facebook-ad-scraper/) | Scrapes competitor ads, analyzes with GPT-4, outputs to Sheets |

### Recruitment

| Workflow | Description |
|----------|-------------|
| [AI Interview Automation](./n8n-ai-interview-automation/) | Automates screening, AI interviews, and follow-up reminders |
| [AI CV Assessment](./n8n-ai-cv-assessment/) | Analyzes CVs with DeepSeek AI, generates scores and recommendations |

## Quick Start

1. Download the `workflow.json` file from the workflow folder
2. In n8n: **Workflows** → **Import from File**
3. Update credentials and placeholder values (marked as `YOUR_*`)
4. Activate the workflow

## Tech Stack

| Technology | Usage |
|------------|-------|
| n8n | Workflow automation platform |
| JavaScript | Custom logic in Code nodes |
| GoHighLevel | CRM integration |
| OpenAI GPT-4 | AI analysis and generation |
| Google Gemini | Image generation |
| DeepSeek | CV assessment |
| GA4 + Meta | Analytics tracking |

## Structure

Each workflow folder contains:
```
workflow-name/
├── workflow.json    # Import this into n8n
└── README.md        # Setup instructions
```

## Contributing

Feel free to fork and adapt these workflows for your use case.
