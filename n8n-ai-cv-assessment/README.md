# AI CV Assessment

Automated candidate CV analysis using DeepSeek AI. Extracts CVs from Google Drive, analyzes them against job requirements, generates structured assessments with scores and recommendations, and updates Google Sheets.

> **Part of Recruitment Suite:** [AI Interview Automation](../n8n-ai-interview-automation/) | AI CV Assessment

## What It Does

### Flow: Automated CV Assessment
1. Triggered on schedule (every 6 hours)
2. Fetches candidates from Google Sheets with completed Google Forms
3. Filters for candidates not yet assessed (no Overall Score)
4. Downloads CV/Resume from Google Drive using file ID
5. Extracts text content from PDF
6. Combines CV text with form responses and HR Select scores
7. Sends combined data to DeepSeek AI for analysis
8. Receives structured JSON assessment response
9. Parses and formats AI response
10. Updates Google Sheets with scores, recommendation, and summary

## Assessment Output

The AI generates the following fields:

| Field | Type | Description |
|-------|------|-------------|
| Overall Score | 0-100 | Composite score based on all criteria |
| Recommendation | Enum | STRONG_YES, YES, NO, STRONG_NO |
| Assessment Summary | Text | 40-word AI-generated summary |
| Job Longevity | 0-10 | Leadership and commitment indicators |
| CV Integrity | 0-10 | Technical proficiency and accuracy |
| Summary | Text | Concatenated strengths, development areas, cognitive scores |

## Scoring Criteria

The AI evaluates candidates on these dimensions:

| Category | Score Range | What It Measures |
|----------|-------------|------------------|
| Leadership | 0-10 | Distributed team management, decision making |
| Operational Excellence | 0-10 | Process optimization, efficiency improvements |
| Vendor Management | 0-10 | 3PL and partner oversight experience |
| Technical Proficiency | 0-10 | Tool expertise (Asana, Monday.com, etc.) |
| Quality Focus | 0-10 | Standards enforcement, attention to detail |

## Required Credentials

- **DeepSeek API** - For AI-powered CV analysis
- **Google Sheets OAuth2** - For reading/writing candidate data
- **Google Drive OAuth2** - For downloading CV files

## Setup

1. Import `workflow.json` into n8n
2. Connect all credentials:
   - DeepSeek API credential
   - Google Sheets OAuth2
   - Google Drive OAuth2
3. Update placeholder values:
   - `YOUR_GOOGLE_SHEET_ID` - Your recruitment tracker spreadsheet
   - `YOUR_CREDENTIAL_ID` - Your n8n credential IDs for Google services
4. Customize the AI prompt in "AI Agent1" node for your specific role requirements
5. Update sheet column mappings if your columns differ from expected structure
6. Activate the workflow

## Google Sheet Structure

### Required Columns

| Column | Description | Used For |
|--------|-------------|----------|
| Email | Candidate email address | Matching key across sheets |
| Google Form Status | Form completion status | Must be "Google Form Filled" to process |
| Field | Google Drive file ID | Links to uploaded CV document |
| Overall Score | Assessment score (0-100) | Populated by workflow |
| Recommendation | AI recommendation | Populated by workflow |
| Summary | Assessment summary | Populated by workflow |

### Optional Columns (Enhanced Assessment)

| Column | Description | Source |
|--------|-------------|--------|
| Talent Signal | HR Select overall score | HR Select assessment |
| CCAT Raw Score | Cognitive assessment score | CCAT test |
| EPP Overall Assessment | Personality assessment | EPP test |
| Full Name | Candidate name | Form submission |
| PhoneNo | Contact number | Form submission |

## How It Works

### Trigger Conditions
Candidates are assessed when all conditions are met:
- Google Form Status = "Google Form Filled"
- Field column contains a valid Drive file ID
- Overall Score column is empty (not yet assessed)

### Processing Flow
```
Schedule Trigger (6h)
    ↓
Fetch Candidates from Sheets
    ↓
Filter: Form Filled + No Score
    ↓
Download CV from Drive
    ↓
Extract PDF Text
    ↓
Combine with Form Data + HR Scores
    ↓
Send to DeepSeek AI
    ↓
Parse JSON Response
    ↓
Update Google Sheets
```

### AI Prompt Structure
The AI receives:
- Full CV text content
- Google Form responses
- HR Select assessment scores (if available)
- CCAT cognitive scores (if available)
- EPP personality assessment (if available)

## Customization

### Modify AI Prompt
Edit the "AI Agent1" Code node to customize:
- Role-specific requirements
- Scoring criteria weights
- Required skills and experience
- Industry-specific terminology

### Adjust Scoring Output
Edit the "Format DeepSeek Assessment for Sheets" node to:
- Map different output fields
- Change column names
- Add or remove assessment dimensions

### Change Schedule
Modify the "Schedule Trigger" node to adjust:
- Run frequency (default: every 6 hours)
- Specific times of day
- Day of week restrictions

### Add Form Fields
Edit the "Prepare DeepSeek Assessment Data1" node to:
- Include additional form responses
- Add custom assessment criteria
- Integrate other data sources

## Error Handling

The workflow handles these scenarios:
- Missing CV file: Skips candidate, logs error
- PDF extraction failure: Attempts text extraction, falls back gracefully
- AI API timeout: Retries with exponential backoff
- Invalid response format: Logs error, continues with next candidate

## Integration with Interview Automation

This workflow complements the [AI Interview Automation](../n8n-ai-interview-automation/) workflow:

| CV Assessment | Interview Automation |
|---------------|---------------------|
| Pre-screening | Active interviewing |
| Document analysis | Live AI conversation |
| Skill extraction | Behavioral assessment |
| Batch processing | Real-time interaction |

Use CV Assessment scores to prioritize candidates for AI interviews.
