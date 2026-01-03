# n8n Workflow Templates

Production-ready n8n automation workflows for AI, CRM, marketing, and recruitment tasks.

## Workflows

| Workflow | Category | Description |
|----------|----------|-------------|
| [Facebook Ad Library Scraper](./facebook-ad-scraper/) | Marketing | Scrapes competitor ads from Facebook, analyzes with GPT-4, outputs to Google Sheets |
| [AI Furniture Customizer](./ai-furniture-customizer/) | AI / E-commerce | Generates AI furniture images with custom materials using Gemini |
| [AI Interview Automation](./n8n-ai-interview-automation/) | Recruitment | Automates candidate screening, AI interviews, and follow-up reminders |
| [AI CV Assessment](./n8n-ai-cv-assessment/) | Recruitment | Analyzes CVs with DeepSeek AI, generates scores and recommendations |
| [CRM Lead Management](./n8n-crm-lead-management/) | CRM / Sales | GoHighLevel automation with lead scoring, lifecycle management, GA4 + Meta tracking |
| [CRM Customer Onboarding](./n8n-crm-customer-onboarding/) | CRM / Success | Post-sale automation with health scoring, churn prevention, feedback collection |

## Quick Start

1. Download the `workflow.json` file from the workflow folder
2. In n8n: **Workflows** → **Import from File**
3. Update credentials and placeholder values (marked as `YOUR_*`)
4. Activate the workflow

## Tech Stack

- **n8n** - Workflow automation platform
- **JavaScript** - Custom logic in Code nodes
- **REST APIs** - HTTP integrations
- **AI Models** - OpenAI GPT-4, Google Gemini, DeepSeek
- **CRM** - GoHighLevel
- **Analytics** - Google Analytics 4, Meta Conversions API

## Structure

Each workflow folder contains:
- `workflow.json` - The n8n workflow (import this)
- `README.md` - Setup instructions and documentation

## Contributing

Feel free to fork and adapt these workflows for your use case.
