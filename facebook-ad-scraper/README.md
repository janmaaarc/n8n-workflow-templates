# Facebook Ad Library Scraper

Scrapes competitor ads from Facebook Ad Library using Apify, analyzes them with AI (GPT-4 for text/strategy, GPT-4o for images, Whisper for video transcription), and outputs structured results to Google Sheets.

## What It Does

### Flow: Complete Ad Analysis Pipeline
1. Triggered via Slack message containing Facebook Ad Library URL
2. Scrapes up to 500 ads using Apify Facebook Ad Library scraper
3. Filters for multi-instance ads (reused creatives = proven performers)
4. Sorts ads by start date (oldest/longest running first)
5. Separates processing for video and image ads
6. **For Videos:**
   - Downloads video file
   - Converts format using CloudConvert if needed
   - Uploads to Google Drive for storage
   - Transcribes audio using OpenAI Whisper
   - Analyzes transcript + visuals with GPT-4
7. **For Images:**
   - Downloads image
   - Analyzes visuals and copy with GPT-4o (vision)
8. Outputs comprehensive analysis to Google Sheets
9. Sends completion notification to Slack with summary

## Why Multi-Instance Filtering?

Ads that appear multiple times indicate:
- **Proven performance** - Advertiser reusing successful creative
- **Tested messaging** - Copy that resonates with audience
- **Worth studying** - Higher ROI on analysis time

Oldest/longest-running ads suggest:
- **Sustainable performance** - Not just flash-in-pan success
- **Evergreen content** - Messaging that continues to work
- **Core brand strategy** - Represents fundamental positioning

## AI Analysis Output

The GPT-4 analysis extracts:

| Insight | Description |
|---------|-------------|
| Hook | Opening attention-grabber (first 3 seconds for video, headline for image) |
| Value Proposition | Core offer and benefit statement |
| Call to Action | CTA text and button type |
| Target Audience | Inferred demographic/psychographic indicators |
| Persuasion Techniques | Tactics used (scarcity, social proof, authority, etc.) |
| Emotional Triggers | Emotions the ad attempts to evoke |
| Production Quality | Low/Medium/High with justification |
| Key Messages | Bullet points of main selling points |
| Recommended Swipe Elements | What to adapt for your own campaigns |

## Required Credentials

- **Apify** - For Facebook Ad Library scraping
- **OpenAI** - GPT-4, GPT-4o (vision), and Whisper APIs
- **Google Sheets OAuth2** - For output storage
- **Google Drive OAuth2** - For video file storage
- **Slack** - For trigger and notifications
- **CloudConvert** - For video format conversion

## Environment Variables / Placeholders

```
YOUR_GOOGLE_SHEET_ID - Output spreadsheet ID
YOUR_GOOGLE_DRIVE_FOLDER_ID - Folder for video uploads
YOUR_SLACK_CHANNEL_ID - Channel for notifications
YOUR_APIFY_ACTOR_ID - Facebook Ad Library scraper actor ID
```

## Setup

1. Import `workflow.json` into n8n
2. Connect all credentials:
   - Apify API token
   - OpenAI API key
   - Google Sheets OAuth2
   - Google Drive OAuth2
   - Slack Bot token
   - CloudConvert API key
3. Update all placeholder values
4. Create output Google Sheet with required columns
5. Test with a Facebook Ad Library URL
6. Activate the workflow

## Usage

Send a Slack message mentioning the bot with a Facebook Ad Library URL:

```
@adbot https://www.facebook.com/ads/library/?active_status=active&ad_type=all&country=US&q=competitor_name
```

Or with filters:
```
@adbot https://www.facebook.com/ads/library/?active_status=active&ad_type=all&country=US&media_type=video&q=brand_name
```

## Google Sheet Output

### Ads Sheet

| Column | Type | Description |
|--------|------|-------------|
| Ad ID | String | Facebook ad identifier |
| Advertiser | String | Page/business name |
| Ad Text | String | Primary text content |
| Headline | String | Ad headline |
| Description | String | Ad description |
| CTA | String | Call-to-action button text |
| Start Date | Date | When ad started running |
| End Date | Date | When ad stopped (if applicable) |
| Media Type | Enum | video / image |
| Media URL | URL | Direct link to creative |
| Landing Page | URL | Destination URL |
| Instances | Number | Times creative was reused |
| Days Running | Number | Calculated run duration |
| AI Analysis | JSON | Full GPT-4 analysis |
| Hook | String | Extracted hook/attention-grabber |
| Value Prop | String | Main value proposition |
| Target Audience | String | Inferred audience |
| Persuasion Tactics | String | Techniques identified |
| Transcript | String | Whisper transcription (videos only) |
| Drive Link | URL | Google Drive link (videos only) |

## Processing Flow

```
Slack Trigger (Ad Library URL)
    ↓
Apify Scraper (up to 500 ads)
    ↓
Filter Multi-Instance Ads
    ↓
Sort by Start Date (oldest first)
    ↓
Split: Videos vs Images
    ↓
┌─────────────────┬─────────────────┐
│     Videos      │     Images      │
├─────────────────┼─────────────────┤
│ Download Video  │ Download Image  │
│ CloudConvert    │                 │
│ Upload to Drive │                 │
│ Whisper Transcr │                 │
│ GPT-4 Analysis  │ GPT-4o Vision   │
└─────────────────┴─────────────────┘
    ↓
Merge Results
    ↓
Write to Google Sheets
    ↓
Slack Notification
```

## Customization

### Modify Ad Limit
Edit the Apify actor settings to change:
- Maximum ads to scrape (default: 500)
- Date range filters
- Country/region filters

### Adjust Filtering Criteria
Edit the "Filter Multi-Instance Ads" Code node to:
- Change minimum instance threshold
- Add/remove filtering conditions
- Modify sorting logic

### Customize GPT-4 Prompts
Edit the analysis nodes to:
- Add industry-specific analysis
- Include competitor comparison
- Focus on specific ad elements
- Change output format

### Modify Notification Format
Edit the Slack message nodes to:
- Include more/fewer details
- Add charts or summaries
- Change channel routing

## Rate Limiting & Costs

Be aware of API costs and limits:

| Service | Consideration |
|---------|---------------|
| Apify | Credits per scrape, ~$5 per 1000 ads |
| OpenAI GPT-4 | $0.03/1K input, $0.06/1K output tokens |
| OpenAI GPT-4o | Vision pricing applies |
| Whisper | $0.006 per minute of audio |
| CloudConvert | Credits per conversion |

Recommendation: Start with smaller scrapes to estimate costs.

## Error Handling

The workflow handles:
- Invalid URL format: Returns error message to Slack
- No ads found: Notifies user, suggests different query
- Video download failure: Skips video, logs error
- Transcription failure: Continues without transcript
- Rate limiting: Implements delays between API calls
