# AI CV Assessment

Analyzes candidate CVs and application data using DeepSeek AI, generates structured assessments with scores and recommendations, and updates Google Sheets.

## What It Does

1. Triggered on schedule (every 6 hours)
2. Fetches candidates with completed Google Forms
3. Filters for candidates not yet assessed (no Overall Score)
4. Downloads CV/Resume from Google Drive
5. Extracts text from PDF
6. Sends CV, form responses, and HR Select scores to DeepSeek AI
7. Receives structured JSON assessment
8. Updates Google Sheets with scores, recommendation, and summary

## Required Credentials

- DeepSeek API
- Google Sheets (OAuth2)
- Google Drive (OAuth2)

## Setup

1. Import `workflow.json` into n8n
2. Connect all credentials
3. Update placeholder values:
   - `YOUR_GOOGLE_SHEET_ID` - Your recruitment tracker sheet
   - `YOUR_CREDENTIAL_ID` - Your n8n credential IDs
4. Customize the AI prompt in "AI Agent1" node for your role requirements
5. Update sheet column mappings if your columns differ

## Assessment Output

| Field | Description |
|-------|-------------|
| Overall Score | 0-100 composite score |
| Recommendation | STRONG_YES, YES, NO, STRONG_NO |
| Assessment Summary | 40-word AI-generated summary |
| Job Longevity | Leadership score (0-10) |
| CV Integrity | Technical proficiency score (0-10) |
| Summary | Concatenated strengths, development areas, cognitive scores |

## Scoring Criteria

The AI evaluates candidates on:
- **Leadership** (0-10) - Distributed team management
- **Operational Excellence** (0-10) - Process optimization
- **Vendor Management** (0-10) - 3PL and partner oversight
- **Technical Proficiency** (0-10) - Tool expertise (Asana, Monday.com)
- **Quality Focus** (0-10) - Standards enforcement

## Google Sheet Structure

### Required Columns
| Column | Description |
|--------|-------------|
| Email | Candidate email (matching key) |
| Google Form Status | Must be "Google Form Filled" |
| Field | Google Drive file ID for CV |
| Overall Score | Populated by workflow |
| Recommendation | Populated by workflow |
| Summary | Populated by workflow |

### Optional Columns (Enhanced Assessment)
| Column | Description |
|--------|-------------|
| Talent Signal | HR Select score |
| CCAT Raw Score | Cognitive assessment |
| EPP Overall Assessment | Personality assessment |
| Full Name | Candidate name |
| PhoneNo | Contact number |

## Usage

The workflow runs automatically. Candidates are assessed when:
- Google Form is completed
- CV is uploaded (Field column has Drive file ID)
- No existing Overall Score

## Customization

- Modify AI prompt in "AI Agent1" node for different roles
- Adjust scoring output in "Format DeepSeek Assessment for Sheets"
- Change schedule in "Schedule Trigger" node
- Add form fields in "Prepare DeepSeek Assessment Data1"
