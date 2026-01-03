# AI Furniture Customizer

n8n workflow backend for an AI-powered furniture customization app. Users select upholstery colors/materials and Gemini AI generates customized product images.

## What It Does

1. User authenticates via webhook (login/signup with Google Sheets as user store)
2. Fetches product data from database API
3. User selects furniture product and customization options (top material, base color)
4. Downloads source product image
5. Sends image + customization prompt to Gemini AI
6. Gemini generates new image with specified materials/colors
7. Uploads generated image to FTP server
8. Logs result to Google Sheets
9. Returns base64 image to frontend for display
10. Users can save designs to gallery

## Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/auth-google-sheets` | POST | User login/signup |
| `/ai-furniture-generator` | POST | Generate AI furniture image |
| `/save-design` | POST | Save design to gallery |
| `/get-client-designs` | POST | Fetch saved designs |

## Required Credentials

- Google Sheets OAuth2
- Gemini AI API Key
- FTP Server
- Product Database API (optional)

## Setup

1. Import `workflow.json` into n8n
2. Connect all credentials
3. Update placeholder values:
   - `YOUR_GOOGLE_SHEET_ID` - Google Sheet for users, images, and gallery
   - `YOUR_GEMINI_API_KEY` - From Google AI Studio
   - `YOUR_PRODUCT_API_URL` - Your product database endpoint
   - `YOUR_API_KEY` - Product API authorization
   - `YOUR_FTP_PATH` - Upload destination path
   - `YOUR_CDN_BASE_URL` - Public URL for uploaded images
   - `YOUR_WATERMARK_URL` - Optional watermark image URL
   - `YOUR_CREDENTIAL_ID` - n8n credential IDs

## Google Sheet Structure

### Sheet1 (Users)
| Column | Description |
|--------|-------------|
| email | User email (primary key) |
| name | User display name |
| phone | Phone number |
| describe yourself | User description |
| created_at | Account creation date |
| last_login | Last login timestamp |
| login_count | Total logins |

### Images Sheet
| Column | Description |
|--------|-------------|
| Timestamp | Generation time |
| Product ID | Source product ID |
| Product Name | Product name |
| Collection Name | Upholstery collection |
| Color | Selected color name |
| Color Hex Code | Hex color value |
| Generated Image URL | FTP URL of generated image |
| Filename | Generated filename |

### Gallery Sheet
| Column | Description |
|--------|-------------|
| Timestamp | Save time |
| User Email | Owner email |
| Username | Owner name |
| Product ID | Product ID |
| Product Name | Product name |
| Collection | Upholstery collection |
| Color | Color name |
| Color Hex | Hex value |
| Image URL | Generated image URL |
| Design Name | User-given name |

## API Request Examples

### Generate Image
```json
POST /ai-furniture-generator
{
  "productId": "31481",
  "productName": "Alhambra Coffee Table",
  "baseImage": "https://example.com/product.jpg",
  "size": "W.130 X D.80 X H.34 cm",
  "top": {
    "name": "Stone-Grey",
    "color": "Stone-Grey"
  },
  "base": {
    "name": "C23 Dark Blue",
    "color": "C23 Dark Blue"
  }
}
```

### Save Design
```json
POST /save-design
{
  "userEmail": "user@example.com",
  "userName": "John Doe",
  "productId": "31481",
  "productName": "Alhambra Coffee Table",
  "collection": "Leather",
  "color": "Stone-Grey",
  "colorHex": "#8B8680",
  "imageUrl": "https://cdn.example.com/image.png",
  "designName": "My Custom Table"
}
```

### Get Designs
```json
POST /get-client-designs
{
  "productId": "31481"  // Optional filter
}
```

## Customization

- Modify AI prompt in "Build AI Prompt" node for different furniture types
- Adjust image generation settings in Gemini request body
- Change gallery limit in "Format Designs Response" node (default: 20)
- Update CORS headers if needed for your frontend domain

## AI Prompt Features

The Gemini prompt is optimized for:
- Square 1:1 output format
- Complete furniture visibility (no cropping)
- Professional product photography style
- Clean neutral backgrounds
- Material/color accuracy
