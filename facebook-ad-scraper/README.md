# Facebook Ad Library Scraper

Scrapes competitor ads from Facebook Ad Library, analyzes them with AI, and saves results to Google Sheets.

## What It Does

1. Triggered via Slack message with Facebook Ad Library URL
2. Scrapes up to 500 ads using Apify
3. Filters for multi-instance ads (reused creatives = proven performers)
4. Sorts by oldest/longest running
5. Separates video and image ads
6. **Videos:** Uploads to Google Drive, transcribes with Whisper, analyzes with GPT-4
7. **Images:** Analyzes visuals with GPT-4o
8. Outputs summary and analysis to Google Sheets
9. Sends completion notification to Slack

## Required Credentials

- Apify
- OpenAI (GPT-4, Whisper)
- Google Sheets
- Google Drive
- Slack
- CloudConvert

## Setup

1. Import `workflow.json` into n8n
2. Connect all credentials
3. Update Google Sheet ID and folder ID
4. Update Slack channel ID
5. Test with a Facebook Ad Library URL

## Usage

Send a Slack message with a Facebook Ad Library URL:
```
@bot https://www.facebook.com/ads/library/?id=123456789
```
