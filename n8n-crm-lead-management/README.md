# n8n CRM Lead Management & Automation

Complete CRM automation system for GoHighLevel with intelligent lead scoring, lifecycle management, pipeline automation, and multi-channel tracking (GA4 + Meta).

## What It Does

### Flow 1: New Lead Processing
1. Receives new leads via webhook (from forms, landing pages, ads)
2. Scores leads based on source, engagement, budget, and timeline
3. Creates contact in GoHighLevel with custom fields and tags
4. Creates pipeline opportunity with monetary value
5. Routes high-score leads (80+) to sales team with urgent task
6. Routes lower-score leads to nurture sequences
7. Sends Slack alerts for hot leads
8. Tracks conversion events to GA4 and Meta Pixel

### Flow 2: Hourly Lead Re-scoring
1. Runs every hour via schedule trigger
2. Fetches all leads from GoHighLevel
3. Applies score decay for inactive leads (14+ and 30+ days)
4. Boosts scores for email engagement
5. Updates lifecycle stages based on new scores
6. Tags stale leads for re-engagement campaigns

### Flow 3: Activity-Based Scoring
1. Receives contact activity events via webhook
2. Calculates score increases based on activity type
3. Updates contact scores and lifecycle stages in real-time
4. Removes stale tags when contacts re-engage
5. Notifies sales team on lifecycle stage upgrades

## Lead Scoring Model

| Factor | Points |
|--------|--------|
| **Source** | |
| Referral | +35 |
| Google Ads | +30 |
| Meta Ads / Email Campaign | +25 |
| Organic | +20 |
| Direct | +15 |
| **Engagement** | |
| Downloaded resource | +25 |
| Watched video | +20 |
| 5+ pages viewed | +20 |
| 3+ minutes on site | +15 |
| **Business Fit** | |
| Enterprise company | +30 |
| Mid-market company | +20 |
| SMB | +10 |
| Budget > $10,000 | +25 |
| **Timeline** | |
| Immediate | +30 |
| 30 days | +20 |
| 90 days | +10 |
| **Activity Events** | |
| Meeting scheduled | +40 |
| Proposal viewed | +35 |
| Call completed | +30 |
| Form submitted | +25 |
| Reply received | +20 |
| Email clicked | +15 |
| Email opened | +5 |
| Page viewed | +3 |

## Lifecycle Stages

| Score | Stage | Assignment |
|-------|-------|------------|
| 80+ | Sales Qualified | Sales Team |
| 50-79 | Marketing Qualified | SDR Team |
| 25-49 | Lead | Nurture Sequence |
| 0-24 | Subscriber | Email List |

## Required Credentials

- GoHighLevel API (HTTP Header Auth with API key)
- Slack API (for team notifications)
- Google Analytics 4 (Measurement Protocol)
- Meta Conversions API (for pixel tracking)

## Environment Variables

Set these in n8n:

```
GHL_PIPELINE_ID=your_pipeline_id
GHL_STAGE_NEW=your_new_stage_id
GHL_NURTURE_WORKFLOW_ID=your_workflow_id
GHL_APP_URL=https://app.gohighlevel.com
SALES_TEAM_USER_ID=user_id_for_task_assignment
GA4_MEASUREMENT_ID=G-XXXXXXXXXX
GA4_API_SECRET=your_ga4_secret
META_PIXEL_ID=your_pixel_id
META_ACCESS_TOKEN=your_meta_token
```

## Setup

1. Import `workflow.json` into n8n
2. Configure GoHighLevel credentials:
   - Create HTTP Header Auth credential
   - Header Name: `Authorization`
   - Header Value: `Bearer YOUR_GHL_API_KEY`
3. Configure Slack credentials
4. Set all environment variables
5. Create custom fields in GoHighLevel:
   - `lead_score` (number)
   - `lifecycle_stage` (dropdown)
   - `pipeline_stage` (dropdown)
   - `assigned_to` (text)
   - `budget` (number)
   - `timeline` (dropdown)
   - `company_size` (dropdown)
   - `last_activity` (date)
   - `last_activity_type` (text)
   - `last_rescored` (date)
6. Create pipeline stages in GoHighLevel matching your workflow
7. Activate the workflow

## Webhook Endpoints

After activation, you'll have these webhooks:

### New Lead Webhook
```
POST /webhook/new-lead
```

Expected payload:
```json
{
  "email": "lead@company.com",
  "phone": "+1234567890",
  "first_name": "John",
  "last_name": "Doe",
  "company": "Acme Inc",
  "source": "google_ads",
  "pages_viewed": 7,
  "time_on_site": 240,
  "downloaded_resource": true,
  "watched_video": false,
  "company_size": "mid-market",
  "budget": "15000",
  "timeline": "30-days",
  "client_id": "GA1.2.xxxxx"
}
```

### Contact Activity Webhook
```
POST /webhook/contact-activity
```

Expected payload:
```json
{
  "contact_id": "ghl_contact_id",
  "email": "lead@company.com",
  "activity_type": "email_clicked",
  "form_name": "demo-request",
  "page_url": "/pricing"
}
```

## GoHighLevel Custom Fields

Create these custom fields in your GHL location:

| Field Key | Type | Options |
|-----------|------|---------|
| lead_score | Number | - |
| lifecycle_stage | Dropdown | subscriber, lead, marketing-qualified, sales-qualified |
| pipeline_stage | Dropdown | new, engaged, qualified, meeting-booked, proposal-sent |
| assigned_to | Text | - |
| budget | Number | - |
| timeline | Dropdown | immediate, 30-days, 90-days, 6-months |
| company_size | Dropdown | smb, mid-market, enterprise |
| last_activity | Date | - |
| last_activity_type | Text | - |
| last_rescored | Date | - |

## Tags Applied

The workflow automatically applies these tags:

- `high-engagement` - 5+ pages viewed
- `engaged-visitor` - 3+ minutes on site
- `content-engaged` - Downloaded a resource
- `video-viewer` - Watched a video
- `enterprise` / `mid-market` / `smb` - Company size
- `high-budget` - Budget > $10,000
- `hot-lead` - Immediate timeline
- `warm-lead` - 30-day timeline
- `nurture` - 90-day timeline
- `priority-high` / `priority-medium` / `priority-low` - Based on score
- `stale-lead` - No activity in 30+ days
- `cooling-off` - No activity in 14-30 days
- `demo-requested` - Submitted demo request form
- `pricing-viewed` - Viewed pricing page

## Customization

### Adjust Scoring Weights
Edit the "Lead Scoring Engine" and "Process Activity" Code nodes to modify point values.

### Change Lifecycle Thresholds
Modify the score thresholds in:
- "Lead Scoring Engine" node (initial scoring)
- "Calculate Updates" node (activity-based updates)
- "Re-score Stale Leads" node (decay logic)

### Add More Activity Types
Extend the `activityScores` object in "Process Activity" node.

### Modify Slack Notifications
Edit the "Slack Sales Alert" and "Notify Stage Change" nodes.

### Adjust Re-scoring Schedule
Change the "Hourly Lead Audit" schedule trigger interval.

## Integration Points

This workflow integrates with:
- **GoHighLevel** - Primary CRM
- **Slack** - Team notifications
- **Google Analytics 4** - Lead event tracking
- **Meta Conversions API** - Facebook/Instagram pixel
- **Any webhook source** - Forms, landing pages, chatbots
