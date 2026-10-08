# YouTube Channel Analytics & Market Research Notebooks

This repository contains Google Colab notebooks for analyzing YouTube channels, extracting video top comments using the **YouTube Data API v3**, mining comment author subscriptions to uncover cross-channel audience engagement, clustering common audience subscriptions, and synthesizing market research reports via **Gemini 3.1 Flash-Lite**, saving results directly to **Google Drive**.

---

## 📓 Notebooks Included

### 1. `0.Youtube_channel_analytics.ipynb`
Retrieves titles, metrics, and metadata for the last **50 long-form videos** of a specified channel, exports CSV/JSON files to Google Drive, and generates performance charts.

### 2. `1.Youtube_video_comments.ipynb`
Extracts top relevant/liked comments for the **10 most recent long-form videos** from the channel, leveraging **Google Drive caching** to avoid unnecessary API quota usage, and produces visual comment analytics.

### 3. `3.Youtube_comment_author_subscriptions.ipynb`
Extracts unique comment authors from exported video comments, queries their public subscriptions via **YouTube Data API v3** (`subscriptions.list`), aggregates the **Top 100 subscribed channels** among the audience, exports dual datasets to Google Drive (CSV/JSON), and outputs quantitative metrics alongside inline visual charts.

### 4. `2.Youtube_comment_llm_summaries.ipynb`
Synthesizes market research insights from the exported video comments using **Gemini 3.1 Flash-Lite** (`gemini-3.1-flash-lite`) via `google-genai` SDK. Outputs structured Markdown and JSON market research reports locally and directly to **Google Drive** with persistent generation caching and 3-attempt exponential backoff retry logic.

### 5. `4.Youtube_channel_subscription_clusters.ipynb` *(New)*
Segments cross-channel audience subscriptions into meaningful media consumption clusters:
- Imports comment author subscriptions and filters for channels with at least more than one occurrence.
- Queries YouTube Data API v3 (`playlistItems.list` on long-form `UULF` playlist IDs) to fetch up to **50 recent long-form videos** (>60s) per channel and exports the combined dataset to Google Drive.
- Generates textual embeddings over channel video titles, averages embeddings per channel, and partitions channels into **8 clusters** via K-Means.
- Generates sequential LLM cluster descriptions (sorted largest to smallest by channel count, including Title, Short Description, Lengthy Explanation with examples, and Top 10 performing videos per channel), passing prior cluster descriptions as context to prevent generic overlap.
- Performs a final LLM request to synthesize refined conceptual clusters beyond numerical ones, exporting formatted Markdown and JSON reports locally and to Google Drive.

---

## 🌟 Key Features

1. **API Quota Efficient**: Avoids expensive `search.list` endpoints (100 units quota per call). Instead, it uses `channels.list` (1 unit), `playlistItems.list` (1 unit per 50 videos), `videos.list` (1 unit per 50 videos), `commentThreads.list` with `order="relevance"`, and `subscriptions.list`.
2. **Comment Author Subscription Graph Mining**:
   - Isolates unique comment authors from top comment datasets.
   - Fetches public subscriptions per author while intercepting private subscription settings (`subscriptionForbidden` / 403 errors).
   - Ranks and aggregates the Top 100 channels subscribed to by comment authors to identify cross-channel audience affinity and competitive overlaps.
3. **LLM Qualitative Market Research & Cluster Synthesis**:
   - Leverages **Gemini 3.1 Flash-Lite** (`gemini-3.1-flash-lite`) via `google-genai` SDK for audience sentiment extraction, customer pain point detection, content requests, and executive channel strategy synthesis.
   - Implements persistent generation caching (`llm_generation_cache.json`) to avoid re-querying Gemini API when re-running notebooks.
   - Robust retry mechanics (up to 3 retries with exponential backoff) with explicit error handling.
4. **Google Drive Caching & Reuse**:
   - Notebooks automatically check Google Drive (`/content/drive/MyDrive/YouTube_Analytics/`) for cached datasets before executing requests.
5. **Shorts Exclusion via Playlist ID & Duration**:
   - Accesses YouTube's long-form uploads playlist by substituting the channel ID prefix `UC...` / `UU...` with `UULF...`.
   - Parses video ISO 8601 durations (`contentDetails.duration`) and filters out any video $\le$ 60 seconds to ensure strict long-form isolation.
6. **Google Drive Persistence**:
   - Mounts Google Drive (`google.colab.drive`) directly within Google Colab.
   - Saves formatted `.csv`, `.json`, and `.md` data files into `/content/drive/MyDrive/YouTube_Analytics/`.
7. **Comprehensive Visual Analytics**:
   - **Video Analytics**: Top viewed videos, view timeline, duration vs. view count, engagement rate distribution, upload frequency by day.
   - **Comment Analytics**: Top 10 most liked comments across videos, top comments collected per video, character length distribution, and likes vs. reply count scatter plot.
   - **Author Subscription Analytics**: Top 20 most subscribed channels among comment authors, public subscriptions per author distribution, and channel overlap log-frequency curves.
   - **LLM Synthesis & Clustering Analytics**: Comment sample volume per video, comment character distributions, LLM synthesis status, cluster size distributions, and 2D PCA embedding projections.

---

## 🚀 How to Run in Google Colab

1. **Open Google Colab**:
   - Upload any notebook directly to [Google Colab](https://colab.research.google.com/).

2. **Obtain YouTube Data API Key**:
   - Go to [Google Cloud Console](https://console.cloud.google.com/).
   - Create or select a project.
   - Enable **YouTube Data API v3**.
   - Create an API Key under **Credentials**.

3. **Configure API Key in Colab**:
   - *Option A (Recommended)*: Add your API key in Google Colab Secrets (Key icon on left sidebar in Colab) named `YOUTUBE_API_KEY` and `GOOGLE_API_KEY`.
   - *Option B*: Paste your key directly in the `CONFIG` dictionary in Section 1 of the notebook.

4. **Execute Cells**:
   - Click `Runtime` -> `Run all` or run each cell sequentially.
   - When prompted, grant permission to mount Google Drive.

---

## 📊 Output Files

The notebooks automatically export datasets into Google Drive (`/content/drive/MyDrive/YouTube_Analytics/`):

### Channel Video Analytics Output:
- `{Channel_Name}_last_50_longform_videos.csv`
- `{Channel_Name}_last_50_longform_videos.json`

### Top Comments Extractor Output:
- `{Channel_Name}_top_comments_10_recent_videos.csv`
- `{Channel_Name}_top_comments_10_recent_videos.json`

### Comment Author Subscriptions Output:
- `{Channel_Name}_comment_author_subscriptions.csv`
- `{Channel_Name}_comment_author_subscriptions.json`
- `{Channel_Name}_top_100_subscribed_channels.csv`
- `{Channel_Name}_top_100_subscribed_channels.json`

### Market Research LLM Summaries Output:
- `{Channel_Name}_market_research_summary.md`
- `{Channel_Name}_market_research_summary.json`

### Subscription Clustering Output:
- `{Channel_Name}_subscribed_channels_recent_videos.csv`
- `{Channel_Name}_subscribed_channels_recent_videos.json`
- `{Channel_Name}_refined_subscription_clusters.md`
- `{Channel_Name}_refined_subscription_clusters.json`
