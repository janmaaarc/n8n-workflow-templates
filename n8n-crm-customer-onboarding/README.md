# CRM Customer Onboarding & Retention

Post-sale automation for GoHighLevel: customer onboarding sequences, health scoring, churn prevention, and feedback collection.

> **Part of CRM Suite:** [Lead Management](../n8n-crm-lead-management/) → [Sales Pipeline](../n8n-crm-sales-pipeline/) → Customer Onboarding

## What It Does

### Flow 1: New Customer Onboarding
1. Receives new customer via webhook (after deal closed)
2. Segments customer (enterprise/professional/standard)
3. Updates contact with customer fields and tags
4. Triggers onboarding email sequence in GHL
5. Creates kickoff call task for enterprise customers
6. Notifies CS team via Slack for high-value accounts

### Flow 2: Customer Health Monitoring
1. Runs every 6 hours via schedule trigger
2. Fetches all active customers from GoHighLevel
3. Calculates health scores based on:
   - Activity recency (decay for inactive customers)
   - Email engagement
   - Onboarding completion
   - Support ticket volume
4. Tags customers by risk level (healthy/medium/high)
5. Creates urgent tasks for high-risk customers
6. Triggers re-engagement campaigns automatically
7. Sends Slack alerts for churn risks

### Flow 3: Feedback & Testimonial Collection
1. Runs daily at 10am
2. Fetches customers with health score 70+
3. Filters by tenure and previous requests
4. Routes to appropriate workflow:
   - 14+ days: NPS survey
   - 30+ days: Testimonial request
   - 60+ days: G2/review request
5. Tags contacts to prevent duplicate requests

### Flow 4: Onboarding Milestone Tracking
1. Receives milestone events via webhook
2. Awards health points for completed milestones
3. Updates onboarding status
4. Notifies team when onboarding completes

## Customer Health Score Model

| Factor | Impact |
|--------|--------|
| **Activity (Decay)** | |
| 7+ days inactive | -5 |
| 14+ days inactive | -15 |
| 30+ days inactive | -30 |
| **Email Engagement** | |
| Clicked emails | +10 |
| No opens (3+ sent) | -20 |
| **Onboarding** | |
| Completed | +15 |
| Incomplete after 14 days | -10 |
| **Support** | |
| 1-2 open tickets | -10 |
| 3+ open tickets | -25 |

## Onboarding Milestone Points

| Milestone | Points |
|-----------|--------|
| first_goal_achieved | +25 |
| first_action | +20 |
| first_login | +15 |
| tutorial_completed | +15 |
| integration_connected | +15 |
| account_setup | +10 |
| profile_complete | +10 |
| team_invited | +10 |

## Risk Levels

| Health Score | Risk Level | Actions |
|--------------|------------|---------|
| 70-100 | Healthy | Eligible for feedback requests |
| 40-69 | Medium Risk | Tagged for monitoring |
| 0-39 | High Risk | Task + Slack alert + re-engagement |

## Required Credentials

- **GoHighLevel API** - HTTP Header Auth with API key
- **Slack API** - For team notifications

## Environment Variables

Set these in n8n:

```
GHL_APP_URL=https://app.gohighlevel.com
GHL_ONBOARDING_WORKFLOW_ID=your_onboarding_workflow_id
GHL_REENGAGEMENT_WORKFLOW_ID=your_reengagement_workflow_id
GHL_NPS_WORKFLOW_ID=your_nps_workflow_id
GHL_TESTIMONIAL_WORKFLOW_ID=your_testimonial_workflow_id
GHL_REVIEW_WORKFLOW_ID=your_review_workflow_id
CUSTOMER_SUCCESS_MANAGER_ID=user_id_for_task_assignment
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
   - `customer_segment` (dropdown: standard, professional, enterprise)
   - `health_score` (number)
   - `risk_level` (dropdown: healthy, medium-risk, high-risk)
   - `onboarding_status` (dropdown: started, in-progress, completed)
   - `onboarding_started_at` (date)
   - `priority` (dropdown: normal, medium, high)
   - `last_health_check` (date)
   - `days_since_activity` (number)
   - `last_milestone` (text)
   - `last_milestone_at` (date)
   - `open_support_tickets` (number)
6. Create GHL workflows for:
   - Onboarding email sequence
   - Re-engagement campaign
   - NPS survey
   - Testimonial request
   - Review request
7. Activate the workflow

## Webhook Endpoints

### New Customer Webhook
```
POST /webhook/new-customer
```

Expected payload:
```json
{
  "id": "ghl_contact_id",
  "contact_id": "ghl_contact_id",
  "email": "customer@company.com",
  "first_name": "John",
  "last_name": "Doe",
  "company": "Acme Inc",
  "plan": "professional",
  "deal_value": 5000
}
```

### Onboarding Milestone Webhook
```
POST /webhook/onboarding-milestone
```

Expected payload:
```json
{
  "contact_id": "ghl_contact_id",
  "email": "customer@company.com",
  "milestone": "first_login",
  "completed_milestones": ["account_setup", "first_login"]
}
```

Valid milestone values:
- `account_setup`
- `first_login`
- `profile_complete`
- `first_action`
- `integration_connected`
- `team_invited`
- `tutorial_completed`
- `first_goal_achieved`

## GoHighLevel Custom Fields

| Field Key | Type | Options |
|-----------|------|---------|
| customer_segment | Dropdown | standard, professional, enterprise |
| health_score | Number | - |
| risk_level | Dropdown | healthy, medium-risk, high-risk |
| onboarding_status | Dropdown | started, in-progress, completed |
| onboarding_started_at | Date | - |
| priority | Dropdown | normal, medium, high |
| last_health_check | Date | - |
| days_since_activity | Number | - |
| last_milestone | Text | - |
| last_milestone_at | Date | - |
| open_support_tickets | Number | - |

## Tags Applied

### Customer Segment
- `new-customer`
- `segment-standard` / `segment-professional` / `segment-enterprise`

### Onboarding
- `onboarding-active`
- `onboarding-complete`
- `milestone-{milestone_name}` (e.g., `milestone-first_login`)

### Risk & Activity
- `inactive-14d` - No activity in 14+ days
- `inactive-30d` - No activity in 30+ days
- `churn-risk-medium` - Health score 40-69
- `churn-risk-high` - Health score below 40

### Feedback
- `feedback-requested` - NPS survey sent
- `testimonial-requested` - Testimonial request sent
- `review-requested` - G2/review request sent

## Customization

### Adjust Health Score Weights
Edit the "Calculate Health Scores" Code node to modify point values.

### Change Risk Thresholds
Modify the score thresholds in "Calculate Health Scores" node:
- High risk: < 40 (default)
- Medium risk: 40-59 (default)
- Healthy: 60+ (default)

### Modify Milestone Points
Edit the `milestonePoints` object in "Process Milestone" node.

### Adjust Feedback Timing
Edit the "Filter Feedback Eligible" node:
- NPS: 14+ days (default)
- Testimonial: 30+ days (default)
- Review: 60+ days (default)

### Change Health Check Frequency
Modify the "Customer Health Check" schedule trigger interval.

## Integration with Lead Management Workflow

This workflow complements the [CRM Lead Management](../n8n-crm-lead-management/) workflow:

| Lead Management | Customer Onboarding |
|-----------------|---------------------|
| Pre-sale focus | Post-sale focus |
| Lead scoring | Health scoring |
| MQL/SQL stages | Onboarding stages |
| Nurture sequences | Retention campaigns |
| GA4/Meta tracking | Feedback collection |

Connect them by triggering the "New Customer Webhook" when a deal closes in the lead management pipeline.
