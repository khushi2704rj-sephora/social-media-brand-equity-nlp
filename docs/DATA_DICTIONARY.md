# Data Dictionary

Complete schema documentation for all data fields across the social media sentiment analysis pipeline.

---

## Normalized Data Schema

After collection and normalization, all data (regardless of source platform) conforms to this unified schema:

| Field | Type | Description | Example |
|---|---|---|---|
| `id` | `string` | Unique identifier (platform-specific or generated) | `"abc123"` |
| `text` | `string` | Cleaned text content (20–500 chars) | `"This brand has really improved..."` |
| `author` | `string` | Username of the content creator | `"user_name"` |
| `created_at` | `ISO 8601` | Original post/comment timestamp | `"2024-06-15T10:30:00Z"` |
| `platform` | `string` | Source platform identifier | `"reddit"`, `"youtube"`, `"instagram"` |
| `engagement.score` | `number` | Platform engagement metric (upvotes/likes) | `42` |
| `engagement.comments` | `number` | Reply/comment count | `7` |
| `url` | `string` | Direct link to original content | `"https://reddit.com/r/..."` |
| `subreddit` | `string\|null` | Reddit subreddit (null for other platforms) | `"marketing"` |
| `videoId` | `string\|null` | YouTube video ID (null for other platforms) | `"dQw4w9WgXcQ"` |
| `researchTopic` | `string` | Research classification tag | `"Social Media Analytics..."` |
| `collectedAt` | `ISO 8601` | Data collection timestamp | `"2024-06-15T12:00:00Z"` |

---

## Sentiment Analysis Output Schema

After HuggingFace inference, each record is enriched with:

| Field | Type | Range | Description |
|---|---|---|---|
| `sentiment` | `string` | `POSITIVE`, `NEUTRAL`, `NEGATIVE` | Predicted sentiment class |
| `sentimentScore` | `float` | 0.0 – 1.0 | Confidence score of the predicted class |
| `positiveScore` | `float` | 0.0 – 1.0 | Probability of positive sentiment |
| `neutralScore` | `float` | 0.0 – 1.0 | Probability of neutral sentiment |
| `negativeScore` | `float` | 0.0 – 1.0 | Probability of negative sentiment |
| `analyzedAt` | `ISO 8601` | — | Sentiment analysis timestamp |

> **Note:** `positiveScore + neutralScore + negativeScore ≈ 1.0`

---

## Google Sheets Output Schema

The final output stored in Google Sheets contains:

| Column | Source Field | Description |
|---|---|---|
| `id` | `id` | Unique record identifier |
| `timestamp` | `analyzedAt` | When sentiment was analyzed |
| `platform` | `platform` | Source platform |
| `author` | `author` | Content author |
| `text` | `text` | Cleaned text content |
| `sentiment_label` | `sentiment` | Positive / Neutral / Negative |
| `sentiment_score` | `sentimentScore` | Confidence of prediction |
| `positive_score` | `positiveScore` | Positive probability |
| `neutral_score` | `neutralScore` | Neutral probability |
| `negative_score` | `negativeScore` | Negative probability |
| `engagement_score` | `engagement.score` | Likes / upvotes |
| `url` | `url` | Link to original content |
| `created_at` | `created_at` | Original post timestamp |

---

## YouTube Comment Collector Schema

The YouTube Comment Collector workflow uses a specialized schema optimized for research relevance:

| Field | Type | Description |
|---|---|---|
| `platform` | `string` | Always `"YouTube"` |
| `video_url` | `string` | Full YouTube video URL |
| `comment_id` | `string` | Unique YouTube comment identifier |
| `comment_text` | `string` | Raw comment text |
| `author_username` | `string` | Commenter's display name |
| `timestamp` | `ISO 8601` | Comment publish date |
| `likes` | `number` | Comment like count |
| `reply_count` | `number` | Number of replies to this comment |
| `video_title` | `string` | Title of the parent video |
| `channel_name` | `string` | Video channel name |
| `is_reply` | `boolean` | Whether this is a reply to another comment |
| `parent_comment_id` | `string\|undefined` | ID of parent comment (replies only) |
| `relevance_score` | `number` | Calculated relevance (0–100) |
| `engagement_signals` | `number` | Count of detected engagement types |
| `keyword_matches` | `number` | Count of matched research keywords |
| `matched_keywords` | `string[]` | List of matched keywords |

---

## Platform-Specific Raw Fields

### Reddit (Pre-Normalization)

| Field | Description |
|---|---|
| `title` | Post title |
| `selftext` | Post body text |
| `subreddit` | Source subreddit |
| `score` | Net upvotes (upvotes − downvotes) |
| `num_comments` | Total comment count |
| `created_utc` | Unix timestamp |
| `permalink` | Reddit relative URL |

### YouTube (Pre-Normalization)

| Field | Description |
|---|---|
| `videoId` | YouTube video identifier |
| `title` | Video title |
| `channelTitle` | Channel name |
| `publishedAt` | Video publish date |
| `textDisplay` | Comment text (HTML stripped) |
| `likeCount` | Comment likes |
| `totalReplyCount` | Replies to comment |

### Instagram (Apify Output)

| Field | Description |
|---|---|
| `text` | Comment text |
| `ownerUsername` | Commenter username |
| `timestamp` | Comment timestamp |
| `likesCount` | Comment likes |
| `postUrl` | Parent post URL |
