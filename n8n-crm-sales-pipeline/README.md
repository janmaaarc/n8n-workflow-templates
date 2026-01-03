# CRM Sales Pipeline Automation

Deal management automation for GoHighLevel: stage-based task creation, stalled deal detection, engagement scoring, and weekly forecasting.

> **Part of CRM Suite:** [Lead Management](../n8n-crm-lead-management/) → Sales Pipeline → [Customer Onboarding](../n8n-crm-customer-onboarding/)

## What It Does

### Flow 1: Deal Stage Changes
1. Receives stage change events via webhook
2. Creates stage-specific tasks with due dates
3. Updates deal fields (weighted value, priority, days in pipeline)
4. Notifies manager for proposal/negotiation stages
5. Routes closed-won deals to customer onboarding
6. Creates 90-day follow-up tasks for closed-lost deals
7. Celebrates wins in Slack

### Flow 2: Stalled Deal Detection
1. Runs every 6 hours via schedule trigger
2. Fetches all open deals from pipeline
3. Identifies deals exceeding stage thresholds
4. Creates urgent tasks for stalled deals
5. Sends Slack alert with pipeline summary
6. Calculates pipeline metrics (total value, weighted value, avg days)

### Flow 3: Weekly Sales Forecast
1. Runs every Monday at 9am
2. Calculates weighted pipeline value by stage
3. Generates forecast by rep, stage, and timeline
4. Identifies top 10 deals by value
5. Sends comprehensive forecast report to Slack

### Flow 4: Deal Activity Tracking
1. Receives activity events via webhook
2. Calculates engagement score based on activity type
3. Updates deal with activity count and last activity
4. Tracks activity history for pipeline velocity

## Stage Configuration

| Stage | Task Due | Auto-Tasks | Manager Notify |
|-------|----------|------------|----------------|
| New | 1 day | Yes | No |
| Discovery | 2 days | Yes | No |
| Proposal | 3 days | Yes | Yes |
| Negotiation | 5 days | Yes | Yes |
| Closed-Won | - | No | Celebrate |
| Closed-Lost | - | No | 90-day follow-up |

## Stalled Deal Thresholds

| Stage | Days Before Stalled |
|-------|---------------------|
| New | 3 days |
| Discovery | 5 days |
| Proposal | 7 days |
| Negotiation | 10 days |

## Pipeline Weighting

| Stage | Weight | Example: $100k Deal |
|-------|--------|---------------------|
| New | 10% | $10,000 weighted |
| Discovery | 20% | $20,000 weighted |
| Proposal | 50% | $50,000 weighted |
| Negotiation | 75% | $75,000 weighted |
| Closed-Won | 100% | $100,000 weighted |

## Activity Engagement Scores

| Activity | Points |
|----------|--------|
| Contract signed | +50 |
| Contract sent | +40 |
| Meeting completed | +35 |
| Proposal sent | +30 |
| Meeting scheduled | +25 |
| Call answered | +20 |
| Proposal viewed | +20 |
| Email replied | +15 |
| Call made | +10 |
| Email opened | +5 |
| Email sent | +2 |

## Required Credentials

- **GoHighLevel API** - HTTP Header Auth with API key
- **Slack API** - For notifications

## Environment Variables

Set these in n8n:

```
GHL_PIPELINE_ID=your_pipeline_id
GHL_APP_URL=https://app.gohighlevel.com
ONBOARDING_WEBHOOK_URL=your_onboarding_workflow_webhook_url
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
   - `stage_entered_at` (date)
   - `days_in_pipeline` (number)
   - `weighted_value` (number)
   - `deal_priority` (dropdown: normal, medium, high)
   - `engagement_score` (number)
   - `last_activity` (date)
   - `last_activity_type` (text)
   - `activity_count` (number)
   - `expected_close_date` (date)
   - `closed_at` (date)
   - `days_to_close` (number)
   - `loss_reason` (dropdown or text)
6. Configure GHL webhook to send stage change events to n8n
7. Activate the workflow

## Webhook Endpoints

### Deal Stage Change Webhook
```
POST /webhook/deal-stage-change
```

Expected payload:
```json
{
  "id": "opportunity_id",
  "title": "Acme Corp - Enterprise Plan",
  "contact_id": "contact_id",
  "email": "buyer@acme.com",
  "contact_name": "John Doe",
  "company": "Acme Corp",
  "monetary_value": 50000,
  "previous_stage": "discovery",
  "current_stage": "proposal",
  "assigned_to": "user_id",
  "source": "website",
  "created_at": "2025-01-15T10:00:00Z",
  "customField": {
    "plan": "enterprise"
  }
}
```

### Deal Activity Webhook
```
POST /webhook/deal-activity
```

Expected payload:
```json
{
  "deal_id": "opportunity_id",
  "contact_id": "contact_id",
  "activity_type": "meeting_completed",
  "notes": "Discovery call completed, moving to proposal"
}
```

Valid activity types:
- `email_sent`, `email_opened`, `email_replied`
- `call_made`, `call_answered`
- `meeting_scheduled`, `meeting_completed`
- `proposal_sent`, `proposal_viewed`
- `contract_sent`, `contract_signed`

## GoHighLevel Custom Fields

| Field Key | Type | Options |
|-----------|------|---------|
| stage_entered_at | Date | - |
| days_in_pipeline | Number | - |
| weighted_value | Number | - |
| deal_priority | Dropdown | normal, medium, high |
| engagement_score | Number | - |
| last_activity | Date | - |
| last_activity_type | Text | - |
| activity_count | Number | - |
| expected_close_date | Date | - |
| closed_at | Date | - |
| days_to_close | Number | - |
| loss_reason | Dropdown | price, timing, competitor, no-budget, no-decision, other |

## Slack Notifications

The workflow sends notifications to these channels:
- `#sales-pipeline` - Stage updates, stalled alerts, forecasts
- `#sales-wins` - Deal won celebrations

## Customization

### Adjust Stage Thresholds
Edit the `stalledThresholds` object in "Analyze Pipeline" node.

### Change Task Due Dates
Modify the `stageConfig` object in "Process Stage Change" node.

### Modify Pipeline Weights
Update the `stageWeights` object in both "Process Stage Change" and "Analyze Pipeline" nodes.

### Adjust Activity Scores
Edit the `activityScores` object in "Process Activity" node.

### Change Forecast Schedule
Modify the "Weekly Forecast" cron expression (default: Monday 9am).

### Add Custom Stages
Extend the `stageConfig` and `stageWeights` objects with your custom stages.

## Integration with Other CRM Workflows

This workflow connects with:
- **[CRM Lead Management](../n8n-crm-lead-management/)** - Receives qualified leads as new deals
- **[CRM Customer Onboarding](../n8n-crm-customer-onboarding/)** - Triggers onboarding on closed-won

```
Lead Management → Sales Pipeline → Customer Onboarding
  (Pre-sale)       (Active deals)    (Post-sale)
```

Configure `ONBOARDING_WEBHOOK_URL` to point to your Customer Onboarding workflow's webhook endpoint.
