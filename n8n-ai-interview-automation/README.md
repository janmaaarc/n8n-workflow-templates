# n8n AI Interview Automation

Automates candidate screening and AI-powered interviews, sends follow-up reminders, and tracks everything in Google Sheets.

## What It Does

1. Triggered on schedule (every 6 hours for new candidates, every 12 hours for follow-ups)
2. Fetches candidates from Google Sheets who completed HR Select assessment + Google Form
3. Filters for candidates waiting 2+ days (cooling period)
4. Creates personalized AI interview links via Oration API
5. Sends interview invitation emails via Brevo
6. Tracks interview status in Google Sheets
7. **Follow-up Flow:** Sends 1st, 2nd, and 3rd reminders at 48h, 96h, and 144h
8. Updates candidate status throughout the pipeline
9. Optional: Sends WhatsApp notifications via WATI

## Required Credentials

- Google Sheets (OAuth2)
- Brevo (SMTP API)
- Oration AI (API key and Workspace ID)
- WATI (optional, for WhatsApp)

## Setup

1. Import `workflow.json` into n8n
2. Connect all credentials
3. Update placeholder values:
   - `YOUR_GOOGLE_SHEET_ID` - Your recruitment tracker sheet
   - `YOUR_ORATION_API_KEY` - From Oration dashboard
   - `YOUR_ORATION_WORKSPACE_ID` - From Oration dashboard
   - `YOUR_ORATION_AGENT_ID` - Your interview bot agent
   - `YOUR_BREVO_API_KEY` - From Brevo account
   - `YOUR_SENDER_EMAIL` - Verified sender in Brevo
4. Update job description in "Your Job Description" node
5. Adjust schedule triggers as needed

## Google Sheet Structure

### Candidates Sheet
| Column | Description |
|--------|-------------|
| Email | Candidate email (matching key) |
| First Name | Candidate name |
| Timestamp | Form submission date |
| HR Select Status | Must be "HS Done" |
| Google Form Status | Must be "Google Form Filled" |
| AI Interview Status | Updated by workflow |
| Link to AI Interview | Hyperlink to AI Interview sheet |

### AI Interview Sheet
| Column | Description |
|--------|-------------|
| Candidate Email | Matching key |
| Candidate Name | From form |
| Interview Link | Generated Oration link |
| Interview Date | When invite was sent |
| Interview Follow Up Status | 1st/2nd/3rd Follow-up Sent |
| Duration (minutes) | Populated after completion |
| Call ID | Oration call reference |

## Usage

The workflow runs automatically on schedule. Candidates are processed when:
- HR Select assessment is complete
- Google Form is filled
- 2+ days have passed since submission
- No interview has been scheduled yet

## Customization

- Edit email templates in "Generate Follow-up Email" and "Construct Email1" nodes
- Adjust follow-up timing in "Filter and Check Follow-up Eligibility" node (default: 48h, 96h, 144h)
- Modify eligibility criteria in "Check 2-Day Waiting Period2" node
- Update job description in "Your Job Description" node
