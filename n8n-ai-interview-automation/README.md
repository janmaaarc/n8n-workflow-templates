# AI Interview Automation

Automates candidate screening and AI-powered interviews using Oration AI. Handles interview scheduling, personalized email invitations via Brevo, multi-stage follow-up reminders, and comprehensive status tracking in Google Sheets.

> **Part of Recruitment Suite:** AI Interview Automation | [AI CV Assessment](../n8n-ai-cv-assessment/)

## What It Does

### Flow 1: Interview Scheduling
1. Runs every 6 hours via schedule trigger
2. Fetches candidates from Google Sheets
3. Filters for candidates who completed HR Select + Google Form
4. Applies 2-day cooling period (wait after form submission)
5. Creates personalized AI interview links via Oration API
6. Sends interview invitation emails via Brevo
7. Updates Google Sheets with interview link and status
8. Creates hyperlink reference to AI Interview sheet

### Flow 2: Follow-up Reminders
1. Runs every 12 hours via schedule trigger
2. Fetches candidates with pending interviews
3. Checks time since invitation sent
4. Sends tiered follow-up reminders:
   - **1st Follow-up:** 48 hours after initial invite
   - **2nd Follow-up:** 96 hours (4 days) after invite
   - **3rd Follow-up:** 144 hours (6 days) after invite
5. Updates follow-up status in sheets
6. Optional: Sends WhatsApp notifications via WATI

### Flow 3: Status Tracking
1. Monitors interview completion via Oration webhooks
2. Updates candidate status on completion
3. Records interview duration and call ID
4. Syncs status back to main Candidates sheet

## Pipeline Flow

```
HR Select Assessment
        ↓
    "HS Done"
        ↓
Google Form Submission
        ↓
"Google Form Filled"
        ↓
2-Day Waiting Period
        ↓
AI Interview Invitation
        ↓
┌───────────────────────────────────┐
│         Follow-up Timeline        │
├───────────────────────────────────┤
│ Day 0: Initial invitation sent    │
│ Day 2: 1st follow-up reminder     │
│ Day 4: 2nd follow-up reminder     │
│ Day 6: 3rd follow-up reminder     │
└───────────────────────────────────┘
        ↓
Interview Completed
        ↓
Status Updated in Sheets
```

## Required Credentials

- **Google Sheets OAuth2** - For candidate data management
- **Brevo (Sendinblue)** - SMTP API for email delivery
- **Oration AI** - API key and Workspace ID for AI interviews
- **WATI** (optional) - For WhatsApp notifications

## Environment Variables / Placeholders

```
YOUR_GOOGLE_SHEET_ID - Recruitment tracker spreadsheet
YOUR_ORATION_API_KEY - From Oration dashboard → Settings → API
YOUR_ORATION_WORKSPACE_ID - From Oration dashboard → Settings
YOUR_ORATION_AGENT_ID - Your configured interview bot agent ID
YOUR_BREVO_API_KEY - From Brevo account → SMTP & API
YOUR_SENDER_EMAIL - Verified sender email in Brevo
```

## Setup

1. Import `workflow.json` into n8n
2. Connect all credentials:
   - Google Sheets OAuth2
   - Brevo SMTP credentials
   - Oration API (via HTTP Request headers)
   - WATI API (optional)
3. Update all placeholder values listed above
4. Update job description in "Your Job Description" node
5. Customize email templates for your company branding
6. Create Google Sheets with required structure
7. Adjust schedule triggers as needed
8. Activate the workflow

## Google Sheet Structure

### Candidates Sheet

| Column | Type | Description |
|--------|------|-------------|
| Email | String | Candidate email (primary key) |
| First Name | String | Candidate first name |
| Last Name | String | Candidate last name |
| Timestamp | DateTime | Form submission date |
| HR Select Status | Enum | Must be "HS Done" to proceed |
| Google Form Status | Enum | Must be "Google Form Filled" |
| AI Interview Status | Enum | Updated by workflow |
| Link to AI Interview | Hyperlink | Links to AI Interview sheet row |

### AI Interview Sheet

| Column | Type | Description |
|--------|------|-------------|
| Candidate Email | String | Matching key to Candidates sheet |
| Candidate Name | String | Full name |
| Interview Link | URL | Generated Oration interview link |
| Interview Date | DateTime | When invitation was sent |
| Interview Follow Up Status | Enum | 1st/2nd/3rd Follow-up Sent |
| Interview Completed | Boolean | Whether interview finished |
| Duration (minutes) | Number | Interview length |
| Call ID | String | Oration call reference ID |
| Completion Date | DateTime | When interview completed |

## Email Templates

### Initial Invitation
The workflow sends personalized emails with:
- Candidate's first name
- Company name and role
- Direct interview link (one-click access)
- Expected duration
- Support contact

### Follow-up Reminders
Each follow-up includes:
- Friendly reminder tone
- Re-emphasis on interview link
- Updated urgency messaging
- Deadline awareness

## Eligibility Criteria

Candidates are scheduled for interviews when:

| Condition | Required Value |
|-----------|----------------|
| HR Select Status | "HS Done" |
| Google Form Status | "Google Form Filled" |
| Days since submission | ≥ 2 days |
| AI Interview Status | Empty or null |

## Customization

### Email Templates
Edit these nodes to customize messaging:
- "Generate Follow-up Email" - Follow-up reminder content
- "Construct Email1" - Initial invitation content

### Follow-up Timing
Edit "Filter and Check Follow-up Eligibility" node:
```javascript
// Default timing (in hours)
const followUpSchedule = {
  first: 48,   // 2 days
  second: 96,  // 4 days
  third: 144   // 6 days
};
```

### Eligibility Criteria
Edit "Check 2-Day Waiting Period2" node to:
- Change waiting period duration
- Add additional eligibility conditions
- Modify status requirements

### Job Description
Edit "Your Job Description" node to:
- Update role title and requirements
- Change company information
- Modify interview context

### Schedule Frequency
Modify trigger nodes to adjust:
- Interview scheduling frequency (default: 6 hours)
- Follow-up check frequency (default: 12 hours)

## Oration Integration

### Creating Interview Links
The workflow calls Oration API to:
```
POST /api/v1/interviews/create
{
  "workspaceId": "YOUR_WORKSPACE_ID",
  "agentId": "YOUR_AGENT_ID",
  "candidateEmail": "candidate@email.com",
  "candidateName": "John Doe",
  "jobDescription": "..."
}
```

### Receiving Completion Events
Configure Oration webhook to POST to your n8n:
```
POST /webhook/interview-completed
{
  "callId": "xxx",
  "candidateEmail": "candidate@email.com",
  "duration": 15,
  "completedAt": "2025-01-15T14:30:00Z"
}
```

## Integration with CV Assessment

This workflow complements [AI CV Assessment](../n8n-ai-cv-assessment/):

| CV Assessment | Interview Automation |
|---------------|---------------------|
| Document analysis | Live conversation |
| Skill extraction | Behavioral assessment |
| Background check | Communication evaluation |
| Batch processing | Real-time interaction |
| Pre-screening score | Interview performance |

Recommended workflow:
1. CV Assessment scores candidates
2. High-scoring candidates proceed to AI Interview
3. Combined scores inform final decisions

## Error Handling

The workflow handles:
- Duplicate invitations: Checks existing status before sending
- Invalid email: Logs error, continues with other candidates
- Oration API failure: Retries with backoff
- Email delivery failure: Logs for manual follow-up
- Missing required fields: Skips candidate, logs warning
