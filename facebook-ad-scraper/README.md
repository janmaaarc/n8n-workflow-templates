# Facebook Ad Library Scraper

Scrapes competitor ads from Facebook Ad Library, analyzes them with AI (GPT-4 + Whisper), and saves results to Google Sheets.

## What It Does

1. Triggered via Slack message with Facebook Ad Library URL
2. Scrapes up to 500 ads using Apify
3. Filters for multi-instance ads (reused creatives = proven performers)
4. Sorts by oldest/longest running ads
5. Separates video and image ads
6. **Videos:** Uploads to Google Drive, transcribes with Whisper, analyzes with GPT-4
7. **Images:** Analyzes visuals with GPT-4o
8. Outputs summary and analysis to Google Sheets
9. Sends completion notification to Slack

## Required Credentials

- Apify (for Facebook Ad Library scraping)
- OpenAI (GPT-4, GPT-4o, Whisper)
- Google Sheets (OAuth2)
- Google Drive (OAuth2)
- Slack (for trigger and notifications)
- CloudConvert (for video processing)

## Setup

1. Import `workflow.json` into n8n
2. Connect all credentials
3. Update placeholder values:
   - `YOUR_GOOGLE_SHEET_ID` - Output spreadsheet
   - `YOUR_GOOGLE_DRIVE_FOLDER_ID` - Video upload folder
   - `YOUR_SLACK_CHANNEL_ID` - Notification channel
   - `YOUR_APIFY_ACTOR_ID` - Facebook Ad Library scraper actor
4. Test with a Facebook Ad Library URL

## Usage

Send a Slack message mentioning the bot with a Facebook Ad Library URL:

```
@bot https://www.facebook.com/ads/library/?active_status=active&ad_type=all&country=US&q=competitor_name
```

## Google Sheet Output

### Ads Sheet
| Column | Description |
|--------|-------------|
| Ad ID | Facebook ad identifier |
| Advertiser | Page name |
| Ad Text | Primary text content |
| Start Date | When ad started running |
| Media Type | video / image |
| Media URL | Link to creative |
| Instances | Number of times creative was reused |
| AI Analysis | GPT-4 analysis of the ad |
| Transcript | Whisper transcription (videos only) |

## How It Works

### Filtering Logic
- Only processes ads with multiple instances (reused = successful)
- Prioritizes oldest/longest-running ads (proven performers)
- Separates processing for video vs image content

### AI Analysis
The workflow analyzes each ad for:
- Hook/attention grabber
- Value proposition
- Call to action
- Target audience indicators
- Persuasion techniques
- Estimated production quality

## Customization

- Modify ad limit in Apify actor settings (default: 500)
- Adjust filtering criteria in "Filter Multi-Instance Ads" node
- Edit GPT-4 prompts in analysis nodes for different insights
- Change notification format in Slack message nodes
