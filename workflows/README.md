# Workflow Documentation

This directory contains the **n8n workflow automation files** used for multi-platform social media data collection and sentiment analysis in this research project.

## 📋 Workflows

### 1. `reddit-youtube-sentiment-analysis.json`

**Multi-Platform Sentiment Analysis Pipeline**

An automated, scheduled pipeline that collects social media data from Reddit and YouTube, performs AI-powered sentiment analysis, and stores structured results.

| Property | Details |
|---|---|
| **Trigger** | Scheduled every 6 hours |
| **Data Sources** | Reddit (r/marketing, r/socialmedia, r/Entrepreneur, r/analytics), YouTube (search + comments) |
| **AI Model** | [CardiffNLP Twitter-RoBERTa-base-sentiment](https://huggingface.co/cardiffnlp/twitter-roberta-base-sentiment) |
| **Output** | Google Sheets + Gmail alerts for high-confidence negative sentiment |
| **Nodes** | 17 nodes |

**Pipeline Flow:**

```
Schedule Trigger (6h)
    ├── Get Reddit Posts → Parse → Deduplicate ─┐
    │                                            ├── Merge → Deduplicate → Normalize
    └── Search YouTube → Extract IDs →           │
        Get Comments → Parse → Deduplicate ──────┘
                                                          ↓
                                              Split Into Batches (10)
                                                          ↓
                                          HuggingFace Sentiment Analysis
                                                          ↓
                                              Parse Sentiment Results
                                                          ↓
                                          Filter High-Confidence Negative
                                             ├── Store in Google Sheets (all)
                                             └── Gmail Alert (negative only)
```

**Key Features:**
- Text normalization (URL/hashtag/mention removal, length filtering 20–500 chars)
- Batch processing with rate limit management
- Three-class sentiment scoring (Positive / Neutral / Negative) with confidence scores
- Automated alerting for high-confidence negative sentiment (>70%)
- Deduplication at multiple pipeline stages

---

### 2. `youtube-comment-collector.json`

**YouTube Brand Equity Comment Collector**

A targeted workflow for collecting and scoring YouTube comments specifically related to brand equity and consumer behaviour research.

| Property | Details |
|---|---|
| **Trigger** | Manual start |
| **Data Source** | YouTube Data API v3 (search + comment threads) |
| **Target** | 150 relevant comments per run |
| **Scoring** | Custom relevance scoring algorithm |
| **Output** | Google Sheets |
| **Nodes** | 9 nodes |

**Pipeline Flow:**

```
Manual Start → Config → Prepare Searches → Search YouTube
    → Extract Video IDs → Fetch Comment Threads (with replies)
    → Parse Comments → Filter & Score → Deduplicate → Google Sheets
```

**Search Queries:**
- `social media brand equity consumer behavior`
- `Instagram marketing case study`
- `TikTok influencer brand loyalty`
- `consumer behavior digital marketing`
- `brand perception social media`

**Relevance Scoring Algorithm:**
| Signal | Points |
|---|---|
| Contains question (?) | +25 |
| Buy/purchase intent keywords | +20 |
| Opinion expressions (think, feel, love, hate...) | +20 |
| Engagement signals (help, recommend, suggest...) | +20 |
| Each matched research keyword | +5 |
| **Maximum score** | **100** |

---

## 🚀 How to Import

1. Open your n8n instance
2. Navigate to **Workflows** → **Import from File**
3. Select the desired `.json` file
4. **Configure credentials** (see [Setup Guide](../docs/SETUP.md)):
   - YouTube Data API v3 key
   - HuggingFace API token (Workflow 1 only)
   - Google Sheets OAuth2
   - Gmail OAuth2 (Workflow 1 only)
5. Replace all `YOUR_*` placeholders with your actual credentials
6. Activate the workflow

## ⚠️ API Quota Notes

- **YouTube Data API**: Default quota is 10,000 units/day. Each search = 100 units, each comment fetch ≈ 1 unit
- **HuggingFace Inference API**: Free tier allows ~30,000 characters/month. Batch processing with delays is implemented
- **Reddit JSON endpoint**: No API key required; uses public `.json` endpoint with rate limiting via User-Agent header
