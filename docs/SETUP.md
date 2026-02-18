# Setup Guide

Complete setup instructions for replicating the social media sentiment analysis pipeline.

## Prerequisites

| Tool | Version | Purpose |
|---|---|---|
| [n8n](https://n8n.io/) | ≥ 1.0 | Workflow automation engine |
| [Node.js](https://nodejs.org/) | ≥ 18 | n8n runtime |
| Google Account | — | Sheets storage & Gmail alerts |
| [Apify Account](https://apify.com/) | Free tier | Instagram data collection |

---

## 1. Install n8n

### Option A: npm (Recommended)

```bash
npm install n8n -g
n8n start
```

### Option B: Docker

```bash
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  docker.n8n.io/n8nio/n8n
```

Access the n8n editor at `http://localhost:5678`.

---

## 2. YouTube Data API v3

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project (or select existing)
3. Navigate to **APIs & Services** → **Library**
4. Search for **YouTube Data API v3** → Click **Enable**
5. Go to **APIs & Services** → **Credentials**
6. Click **Create Credentials** → **API Key**
7. Copy the API key
8. *(Recommended)* Restrict the key to YouTube Data API v3 only

**Quota:** Default is 10,000 units/day. Each video search costs 100 units.

---

## 3. HuggingFace API Token

1. Create a free account at [huggingface.co](https://huggingface.co/)
2. Go to [Settings → Access Tokens](https://huggingface.co/settings/tokens)
3. Create a new token with `read` access
4. In n8n, create a **Header Auth** credential:
   - **Name**: `Authorization`
   - **Value**: `Bearer YOUR_HUGGINGFACE_TOKEN`

**Model used:** [`cardiffnlp/twitter-roberta-base-sentiment`](https://huggingface.co/cardiffnlp/twitter-roberta-base-sentiment)

---

## 4. Google Sheets OAuth2

1. In Google Cloud Console, enable the **Google Sheets API**
2. Go to **APIs & Services** → **Credentials**
3. Click **Create Credentials** → **OAuth 2.0 Client ID**
4. Application type: **Web application**
5. Add authorized redirect URI: `http://localhost:5678/rest/oauth2-credential/callback`
6. Copy Client ID and Client Secret
7. In n8n, go to **Credentials** → **Add Credential** → **Google Sheets OAuth2**
8. Paste Client ID and Client Secret → Click **Connect**

---

## 5. Gmail OAuth2

*(Required only for Workflow 1 — sentiment alert emails)*

1. In Google Cloud Console, enable the **Gmail API**
2. Use the same OAuth2 client or create a new one
3. Add authorized redirect URI: `http://localhost:5678/rest/oauth2-credential/callback`
4. In n8n, go to **Credentials** → **Add Credential** → **Gmail OAuth2**
5. Paste Client ID and Client Secret → Click **Connect**

---

## 6. Apify (Instagram Data Collection)

1. Create a free account at [apify.com](https://apify.com/)
2. Go to [Account → Integrations](https://console.apify.com/account/integrations)
3. Copy your API token
4. Search for the **Instagram Comment Scraper** actor in Apify Store
5. Configure with your target posts/hashtags related to brand equity
6. Export results as CSV/JSON for analysis

---

## 7. Import Workflows into n8n

```bash
# Navigate to the workflows directory
cd workflows/

# Import via n8n CLI (optional)
n8n import:workflow --input=reddit-youtube-sentiment-analysis.json
n8n import:workflow --input=youtube-comment-collector.json
```

Or use the n8n UI: **Workflows** → **Import from File** → Select the JSON file.

---

## 8. Configure Credentials in Workflows

After importing, open each workflow and update the credential placeholders:

| Placeholder | Where to Find |
|---|---|
| `YOUR_YOUTUBE_API_KEY` | Google Cloud Console → Credentials |
| `YOUR_HUGGINGFACE_CREDENTIAL_ID` | n8n Credentials panel |
| `YOUR_GOOGLE_SHEET_ID` | URL of your Google Sheet (`/d/SHEET_ID/`) |
| `YOUR_GOOGLE_SHEETS_CREDENTIAL_ID` | n8n Credentials panel |
| `YOUR_GMAIL_CREDENTIAL_ID` | n8n Credentials panel |
| `YOUR_ALERT_EMAIL` | Your email address for alerts |

---

## ✅ Verification

Once everything is configured:

1. **Test Workflow 2 first** (YouTube Comment Collector) — it uses manual trigger
2. Check that comments appear in Google Sheets
3. **Activate Workflow 1** (Sentiment Analysis) — runs automatically every 6 hours
4. Monitor the n8n execution log for errors

## 🔧 Troubleshooting

| Issue | Solution |
|---|---|
| YouTube API quota exceeded | Wait 24 hours or [request quota increase](https://console.cloud.google.com/apis/api/youtube.googleapis.com/quotas) |
| HuggingFace model loading | First request may take 20-30s while model loads. The workflow includes retry logic. |
| Reddit 429 errors | The public JSON endpoint has rate limits. Increase the schedule interval. |
| Google Sheets permission denied | Ensure the OAuth2 account has edit access to the target spreadsheet |
