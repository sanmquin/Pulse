# Development Log: YouTube Analytics & Market Research Platform

## 📋 Research & Engineering Progress Log

### Module 4: Comment Author Subscription Mining & Audience Affinity Analysis (`youtube_comment_author_subscriptions.ipynb`)
- **Objective**: Quantitative graph mining of comment author subscription profiles to discover cross-channel audience overlaps, competitor channels, and niche interest clusters.
- **Input Dependencies**: Top comment datasets (`{Channel_Name}_top_comments_10_recent_videos.json`) exported by `youtube_video_comments.ipynb` in `/content/drive/MyDrive/YouTube_Analytics/` or local fallback paths (`data/`, `analysis/`, `.`).
- **Methodology & Mathematical Formulation**:
  1. *Author ID Isolation*: Extracts unique channel IDs $A = \{a_1, a_2, \dots, a_N\}$ from author channel profile URLs.
  2. *Subscription Mining*: Queries `subscriptions.list(part="snippet", channelId=a)` for each unique author. Robsutly handles private subscription settings (`subscriptionForbidden` 403 errors).
  3. *Channel Frequency Aggregation*: Computes channel overlap frequency across public authors $A_{public}$:
     $$f(c) = \sum_{a \in A_{public}} \mathbb{I}(c \in S_a)$$
     Ranks and isolates the Top 100 channels ($K = 100$).
- **Exported Datasets**:
  - Raw Subscriptions: `{Channel_Name}_comment_author_subscriptions.csv` & `.json`
  - Aggregated Top 100 Channels: `{Channel_Name}_top_100_subscribed_channels.csv` & `.json`
- **Quantitative Metrics & Reporting**: Reports total comments analyzed, total comment authors, unique filtered authors, public/private author breakdown, total subscriptions fetched, and total unique channels discovered.
