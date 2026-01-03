# AI Furniture Customizer

Backend workflow for an AI-powered furniture customization application. Users select upholstery colors and materials, and Gemini AI generates customized product images with the specified changes.

## What It Does

### Flow 1: User Authentication
1. Receives login/signup request via webhook
2. Checks if user exists in Google Sheets
3. Creates new user record if not found
4. Updates login count and last login timestamp
5. Returns user profile data

### Flow 2: AI Furniture Generation
1. Receives customization request with product and color selections
2. Fetches product data from database API
3. Downloads source product image
4. Builds detailed AI prompt for customization
5. Sends image + prompt to Gemini AI
6. Gemini generates new image with specified materials/colors
7. Adds optional watermark to generated image
8. Uploads generated image to FTP server
9. Logs result to Google Sheets (Images sheet)
10. Returns base64 image to frontend for display

### Flow 3: Save Design
1. Receives save request from user
2. Validates user and design data
3. Stores design in Gallery sheet with metadata
4. Returns confirmation with design ID

### Flow 4: Get Client Designs
1. Receives request with optional product filter
2. Fetches designs from Gallery sheet
3. Filters by product ID if specified
4. Returns formatted design list (limit: 20)

## Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/auth-google-sheets` | POST | User login/signup |
| `/ai-furniture-generator` | POST | Generate AI furniture image |
| `/save-design` | POST | Save design to user gallery |
| `/get-client-designs` | POST | Fetch saved designs |

## Required Credentials

- **Google Sheets OAuth2** - For user data, image logs, and gallery
- **Gemini AI API Key** - For image generation
- **FTP Server** - For uploading generated images
- **Product Database API** (optional) - For fetching product catalog

## Environment Variables / Placeholders

Update these values in the workflow:

```
YOUR_GOOGLE_SHEET_ID - Google Sheet containing users, images, and gallery data
YOUR_GEMINI_API_KEY - API key from Google AI Studio
YOUR_PRODUCT_API_URL - Your product database API endpoint
YOUR_API_KEY - Authorization key for product API
YOUR_FTP_PATH - FTP upload destination path (e.g., /public_html/generated/)
YOUR_CDN_BASE_URL - Public URL prefix for uploaded images
YOUR_WATERMARK_URL - Optional watermark image URL
YOUR_CREDENTIAL_ID - n8n credential IDs for Google services
```

## Setup

1. Import `workflow.json` into n8n
2. Connect all credentials:
   - Google Sheets OAuth2
   - Gemini AI (via HTTP Request with API key)
   - FTP credentials
3. Update all placeholder values listed above
4. Create Google Sheets with required structure (see below)
5. Configure CORS headers if needed for your frontend domain
6. Activate the workflow

## Google Sheet Structure

### Sheet1 (Users)

| Column | Type | Description |
|--------|------|-------------|
| email | String | User email (primary key) |
| name | String | User display name |
| phone | String | Phone number |
| describe yourself | String | User bio/description |
| created_at | DateTime | Account creation timestamp |
| last_login | DateTime | Most recent login |
| login_count | Number | Total login count |

### Images Sheet

| Column | Type | Description |
|--------|------|-------------|
| Timestamp | DateTime | Generation timestamp |
| Product ID | String | Source product identifier |
| Product Name | String | Product display name |
| Collection Name | String | Upholstery collection name |
| Color | String | Selected color name |
| Color Hex Code | String | Hex color value (#RRGGBB) |
| Generated Image URL | URL | FTP URL of generated image |
| Filename | String | Generated filename |

### Gallery Sheet

| Column | Type | Description |
|--------|------|-------------|
| Timestamp | DateTime | Save timestamp |
| User Email | String | Owner's email address |
| Username | String | Owner's display name |
| Product ID | String | Product identifier |
| Product Name | String | Product display name |
| Collection | String | Upholstery collection |
| Color | String | Color name |
| Color Hex | String | Hex color value |
| Image URL | URL | Generated image URL |
| Design Name | String | User-given design name |

## API Request Examples

### User Authentication
```json
POST /auth-google-sheets
{
  "email": "user@example.com",
  "name": "John Doe",
  "phone": "+1234567890"
}
```

Response:
```json
{
  "success": true,
  "user": {
    "email": "user@example.com",
    "name": "John Doe",
    "isNewUser": false,
    "loginCount": 5
  }
}
```

### Generate AI Furniture Image
```json
POST /ai-furniture-generator
{
  "productId": "31481",
  "productName": "Alhambra Coffee Table",
  "baseImage": "https://example.com/products/31481.jpg",
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

Response:
```json
{
  "success": true,
  "imageUrl": "https://cdn.example.com/generated/31481_stone-grey_1704067200.png",
  "base64": "data:image/png;base64,..."
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
  "imageUrl": "https://cdn.example.com/generated/31481_stone-grey.png",
  "designName": "My Custom Table"
}
```

### Get User Designs
```json
POST /get-client-designs
{
  "userEmail": "user@example.com",
  "productId": "31481"  // Optional filter
}
```

## AI Prompt Features

The Gemini prompt is optimized for:
- **Square 1:1 output format** - Consistent aspect ratio
- **Complete furniture visibility** - No cropping of product
- **Professional product photography** - Studio-quality lighting
- **Clean neutral backgrounds** - Focus on the product
- **Material/color accuracy** - True-to-life representation
- **Realistic textures** - Proper fabric/leather rendering

## Customization

### Modify AI Prompt
Edit the "Build AI Prompt" Code node to:
- Change output format/aspect ratio
- Adjust lighting and background preferences
- Add material-specific instructions
- Include brand guidelines

### Adjust Image Settings
Modify the Gemini request body to:
- Change resolution
- Adjust quality settings
- Modify generation parameters

### Change Gallery Limit
Edit the "Format Designs Response" node to:
- Increase/decrease design limit (default: 20)
- Add pagination
- Change sort order

### Update CORS Headers
Modify webhook response nodes to:
- Add allowed origins for your frontend
- Configure allowed methods
- Set cache headers

## Error Handling

The workflow handles:
- Invalid product ID: Returns error with available products
- Image generation failure: Retries once, returns placeholder
- FTP upload failure: Logs error, returns base64 only
- User not found: Creates new user automatically
