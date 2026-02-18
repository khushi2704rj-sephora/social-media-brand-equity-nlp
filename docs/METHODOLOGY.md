# Research Methodology

## Overview

This research employs a **mixed-methods computational approach** combining automated social media data collection, natural language processing (NLP), and sentiment analysis to assess the relationship between brand equity dimensions and consumer behaviour patterns expressed on social media platforms.

---

## Theoretical Framework

### Brand Equity Models

The research draws upon two foundational brand equity frameworks:

**Aaker's Brand Equity Model (1991):**
- Brand Awareness
- Brand Associations
- Perceived Quality
- Brand Loyalty
- Other Proprietary Brand Assets

**Keller's Customer-Based Brand Equity (CBBE) Model (1993):**
- Brand Salience (Identity)
- Brand Performance & Imagery (Meaning)
- Consumer Judgments & Feelings (Response)
- Brand Resonance (Relationships)

### Consumer Behaviour in Digital Context

Social media has fundamentally transformed how consumers interact with brands. This research operationalizes consumer behaviour through:
- **Purchase intent signals** — detected via keyword patterns (buy, purchase, worth, price)
- **Brand advocacy** — measured through recommendation language (recommend, suggest, best)
- **Emotional engagement** — captured via sentiment polarity and intensity scores
- **Community participation** — measured through engagement metrics (upvotes, comments, replies)

---

## Data Collection Strategy

### Multi-Platform Approach

| Platform | Method | Tool | Rationale |
|---|---|---|---|
| **Reddit** | Public JSON API | n8n HTTP Request | Authentic discussions in niche communities; unfiltered consumer opinions |
| **YouTube** | Data API v3 | n8n HTTP Request | Video comment threads reveal detailed consumer perspectives on brands |
| **Instagram** | Web Scraping | Apify Actor | Visual-first platform with high brand engagement; comment analysis |

### Why These Platforms?

1. **Reddit**: Known for candid, anonymous discussions — reduces social desirability bias. Subreddit communities (r/marketing, r/socialmedia, r/Entrepreneur, r/analytics) provide domain-specific discourse
2. **YouTube**: Long-form content generates in-depth viewer comments; comment threads enable discussion analysis
3. **Instagram**: Dominant platform for brand-consumer interaction; influencer marketing discourse

### Sampling Strategy

- **Temporal scope**: Data collected from January 2024 onwards
- **Keyword-driven sampling**: Queries target brand equity and consumer behaviour terminology
- **Relevance scoring**: Custom algorithm filters noise and retains research-relevant content
- **Deduplication**: Multi-stage deduplication ensures data integrity

---

## Sentiment Analysis Methodology

### Model Selection

**CardiffNLP Twitter-RoBERTa-base-sentiment** was selected based on:

1. **Domain appropriateness** — Pre-trained on ~58 million tweets, making it well-suited for informal social media language
2. **Benchmark performance** — State-of-the-art results on TweetEval sentiment evaluation
3. **Three-class classification** — Positive / Neutral / Negative aligns with brand equity measurement needs
4. **Confidence scoring** — Probability distribution across classes enables nuanced analysis

### Classification Schema

| Label | Interpretation | Brand Equity Implication |
|---|---|---|
| **Positive** | Favorable brand sentiment | Positive brand associations, satisfaction, advocacy |
| **Neutral** | Informational / factual | Brand awareness without strong valence |
| **Negative** | Unfavorable brand sentiment | Brand risk, dissatisfaction, potential churn |

### Text Preprocessing

Before sentiment analysis, text undergoes standardized preprocessing:

1. **URL removal** — Prevents model bias from link patterns
2. **Hashtag/mention stripping** — Removes platform-specific noise
3. **Whitespace normalization** — Collapses multiple spaces/newlines
4. **Length filtering** — Retains text between 20–500 characters
   - < 20: Insufficient context for reliable sentiment classification
   - > 500: Truncated to prevent model token limit issues

---

## Data Analysis Framework

### Quantitative Measures

| Metric | Source | Purpose |
|---|---|---|
| Sentiment polarity | HuggingFace RoBERTa | Primary dependent variable |
| Confidence score | Model probability output | Analysis reliability measure |
| Engagement score | Platform-specific (upvotes, likes) | Weighted sentiment importance |
| Comment volume | Data collection pipeline | Brand awareness proxy |
| Relevance score | Custom scoring algorithm | Content quality filter |

### Cross-Platform Analysis

The normalization layer standardizes data across platforms, enabling:
- Comparative sentiment analysis (Reddit vs YouTube vs Instagram)
- Platform-specific brand perception mapping
- Temporal trend analysis across platforms
- Engagement-weighted sentiment aggregation

---

## Ethical Considerations

- All data is collected from **publicly available** sources
- No personally identifiable information (PII) is stored beyond public usernames
- Reddit data uses the **public JSON endpoint** (no authentication required)
- YouTube data is accessed via the **official API** within terms of service
- Instagram data follows **Apify's responsible scraping** guidelines
- Research adheres to institutional ethics requirements

---

## Limitations

1. **Language bias**: Analysis limited to English-language content
2. **Platform selection bias**: Three platforms may not capture all brand discourse
3. **Model limitations**: RoBERTa may misclassify sarcasm, irony, or culturally nuanced expressions
4. **Temporal scope**: Findings represent a specific time window and may not generalize
5. **Keyword dependency**: Keyword-based sampling may miss relevant discussions using different terminology

---

## References

- Aaker, D. A. (1991). *Managing Brand Equity: Capitalizing on the Value of a Brand Name*. Free Press.
- Keller, K. L. (1993). Conceptualizing, measuring, and managing customer-based brand equity. *Journal of Marketing*, 57(1), 1–22.
- Barbieri, F., Camacho-Collados, J., Espinosa Anke, L., & Neves, L. (2020). TweetEval: Unified benchmark and comparative evaluation for tweet classification. *Findings of EMNLP 2020*.
- Loureiro, S. M. C., Guerreiro, J., & Ali, F. (2020). 20 years of research on virtual reality and augmented reality in tourism context: A text-mining approach. *Tourism Management*, 77, 104028.
