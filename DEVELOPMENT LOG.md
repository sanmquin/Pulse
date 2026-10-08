# Development Log: YouTube Analytics & Market Research Platform

## 📋 Research & Engineering Progress Log

### Module 4: Comment Author Subscription Mining & Audience Affinity Analysis (`3.Youtube_comment_author_subscriptions.ipynb`)
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

### Module 5: Subscribed Channel Video Mining, Embedding Vectorization, and LLM Cluster Refinement (`4.Youtube_channel_subscription_clusters.ipynb`)
- **Objective**: Machine learning vectorization and LLM market research synthesis to segment audience subscriptions into 8 distinct media consumption clusters.
- **Input Dependencies**: Aggregated Top 100 channels dataset (`{Channel_Name}_top_100_subscribed_channels.json` or `.csv`) exported by Module 4 in `/content/drive/MyDrive/YouTube_Analytics/` or local fallback paths.
- **Methodology & Mathematical Formulation**:
  1. *Occurrence Filtering*: Filters for channels with subscription occurrences $> 1$ ($f(c) > 1$).
  2. *Long-Form Video Mining*: Queries `playlistItems.list` on channel `UULF` playlist IDs to fetch up to 50 recent long-form videos ($>60$ seconds duration).
  3. *Channel Embedding Vectorization*: Extracts video titles per channel, computes textual feature embeddings, and calculates the mean channel vector $v_c = \frac{1}{N_c} \sum_{i=1}^{N_c} e(t_{c,i})$.
  4. *K-Means Clustering*: Partitions channel vectors into 8 numerical clusters ($K=8$).
  5. *Sequential LLM Cluster Synthesis*: Orders clusters from largest to smallest by channel count. Calls `gemini-3.1-flash-lite` iteratively, injecting previously generated cluster descriptions as context to prevent generic descriptions (including Title, Short Description, Lengthy Explanation with examples, and Top 10 performing videos per channel).
  6. *Holistic LLM Refinement*: Submits all initial cluster descriptions to a final LLM request to synthesize refined, higher-level conceptual clusters.
- **Exported Datasets**:
  - Subscribed Channel Recent Videos: `{Channel_Name}_subscribed_channels_recent_videos.csv` & `.json`
  - Refined Cluster Summary Report: `{Channel_Name}_refined_subscription_clusters.md` & `.json`
- **Quantitative Metrics & Reporting**: Reports total channels filtered, total recent long-form videos collected, cluster size distributions, and 2D PCA embedding projections.
