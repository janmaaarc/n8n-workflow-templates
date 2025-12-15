# AI Furniture Customizer

n8n workflow backend for an AI-powered furniture customization app. Users select upholstery colors/materials and Gemini AI generates the customized product image.

## Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/auth-google-sheets` | POST | User login/signup |
| `/ai-furniture-generator` | POST | Generate AI furniture image |
| `/save-design` | POST | Save design to gallery |
| `/get-client-designs` | POST | Fetch saved designs |

## Flow

1. User authenticates
2. Selects a furniture product
3. Picks upholstery collection + color
4. Gemini AI generates customized image
5. Image uploaded to FTP, logged to Sheets
6. User can save to gallery

## Required Credentials

- Google Sheets OAuth
- Gemini AI API
- FTP server
- Database API (for product data)

## Setup

1. Import `workflow.json` into n8n
2. Connect credentials
3. Update Google Sheet ID, FTP path, and API URLs
