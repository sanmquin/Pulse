# Dataset Exports & Provenance Documentation

This document outlines the exported datasets, file storage locations, schemas, and analytical pipeline provenance across the YouTube Analytics & Market Research Platform.

---

## 📁 Storage Directories & Fallback Hierarchy

All notebooks in this repository execute with an adaptive storage hierarchy:
1. **Primary Colab / Drive Storage**: `/content/drive/MyDrive/YouTube_Analytics/`
2. **Local Fallback Directories**: `data/`, `analysis/`, `.`

---

## 📊 Exported Payloads & Datasets

### 1. Channel Video Analytics (`0.Youtube_channel_analytics.ipynb`)
- **CSV Output**: `{Channel_Name}_last_50_longform_videos.csv`
- **JSON Output**: `{Channel_Name}_last_50_longform_videos.json`
- **Description**: Stores titles, published dates, ISO 8601 durations, view counts, like counts, comment counts, engagement rates, and video URLs for the last 50 long-form videos (>60s duration).

### 2. Video Comments Extraction (`1.Youtube_video_comments.ipynb`)
- **CSV Output**: `{Channel_Name}_top_comments_10_recent_videos.csv`
- **JSON Output**: `{Channel_Name}_top_comments_10_recent_videos.json`
- **Description**: Contains top relevant/liked comments harvested across the 10 most recent long-form videos of the channel, including author profile URLs, text, and like counts.

### 3. Comment Author Subscriptions (`3.Youtube_comment_author_subscriptions.ipynb`)
- **Raw Subscriptions CSV**: `{Channel_Name}_comment_author_subscriptions.csv`
- **Raw Subscriptions JSON**: `{Channel_Name}_comment_author_subscriptions.json`
- **Top 100 Channels CSV**: `{Channel_Name}_top_100_subscribed_channels.csv`
- **Top 100 Channels JSON**: `{Channel_Name}_top_100_subscribed_channels.json`
- **Description**: Mined public subscriptions per unique comment author and aggregated frequency metrics for the Top 100 subscribed channels ($f(c) = \sum_{a} \mathbb{I}(c \in S_a)$).

### 4. Market Research LLM Summaries (`2.Youtube_comment_llm_summaries.ipynb`)
- **Markdown Report**: `{Channel_Name}_market_research_summary.md`
- **JSON Payload**: `{Channel_Name}_market_research_summary.json`
- **Description**: Qualitative market research report synthesized via Gemini 3.1 Flash-Lite, containing per-video summaries, customer pain points, feature requests, and global channel strategy recommendations.

### 5. Channel Subscription Clusters & Refinement (`4.Youtube_channel_subscription_clusters.ipynb`)
- **Subscribed Channel Recent Videos CSV**: `{Channel_Name}_subscribed_channels_recent_videos.csv`
- **Subscribed Channel Recent Videos JSON**: `{Channel_Name}_subscribed_channels_recent_videos.json`
- **Refined Cluster Summary Markdown**: `{Channel_Name}_refined_subscription_clusters.md`
- **Refined Cluster Summary JSON**: `{Channel_Name}_refined_subscription_clusters.json`
- **Description**:
  - Contains up to 50 recent long-form videos (>60s) fetched via YouTube Data API v3 (`UULF` playlist IDs) for every channel subscribed to by audience members with > 1 occurrence.
  - Contains textual vector embeddings and 8-cluster K-Means assignments.
  - Contains initial sequential LLM cluster descriptions (sorted largest to smallest by channel count, with prior descriptions passed as context) and the final holistic LLM refined conceptual clusters (including top 3 performing videos per channel).

### 6. Channel Relevance Filtering & Selection (`5.Youtube_channel_relevance_filtering.ipynb`) *(New)*
- **Top 10 Relevant Channels CSV**: `{Channel_Name}_top_10_relevant_channels.csv`
- **Top 10 Relevant Channels JSON**: `{Channel_Name}_top_10_relevant_channels.json`
- **Top 10 Relevant Channels Markdown**: `{Channel_Name}_top_10_relevant_channels.md`
- **Description**:
  - Contains the top 10 most relevant YouTube channels filtered through iterative LLM batch reduction ($10 \to 5$, shuffle until $\le 25$, final selection of top 10) based on target channel thematic guidelines (`context/channel_description.txt`).
  - Includes qualitative relevance scores, thematic alignment tags, detailed justifications, and top video sample titles per selected channel.

---

## 🛠️ Persistent Cache Files

- **`llm_generation_cache.json`**: Persistent disk-backed cache for Gemini LLM responses, stored in `/content/drive/MyDrive/YouTube_Analytics/` and mirrored locally to prevent redundant API queries across execution sessions.
