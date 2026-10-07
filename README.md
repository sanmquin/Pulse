# YouTube Channel Analytics & Market Research Notebooks

This repository contains Google Colab notebooks for analyzing YouTube channels, extracting video top comments using the **YouTube Data API v3**, and synthesizing market research reports via **Gemini 3.1 Flash-Lite**, saving results directly to **Google Drive**.

---

## 📓 Notebooks Included

### 1. `youtube_channel_analytics.ipynb`
Retrieves titles, metrics, and metadata for the last **50 long-form videos** of a specified channel, exports CSV/JSON files to Google Drive, and generates performance charts.

### 2. `youtube_video_comments.ipynb`
Extracts top relevant/liked comments for the **10 most recent long-form videos** from the channel, leveraging **Google Drive caching** to avoid unnecessary API quota usage, and produces visual comment analytics.

### 3. `youtube_comment_llm_summaries.ipynb` *(New)*
Synthesizes market research insights from the exported video comments using **Gemini 3.1 Flash-Lite** (`gemini-3.1-flash-lite`) via `google-genai` SDK. Outputs structured Markdown and JSON market research reports locally and directly to **Google Drive** with persistent generation caching and 3-attempt exponential backoff retry logic.

---

## 🌟 Key Features

1. **API Quota Efficient**: Avoids expensive `search.list` endpoints (100 units quota per call). Instead, it uses `channels.list` (1 unit), `playlistItems.list` (1 unit per 50 videos), `videos.list` (1 unit per 50 videos), and `commentThreads.list` with `order="relevance"`.
2. **LLM Qualitative Market Research Synthesis**:
   - Leverages **Gemini 3.1 Flash-Lite** (`gemini-3.1-flash-lite`) via `google-genai` SDK for audience sentiment extraction, customer pain point detection, content requests, and executive channel strategy synthesis.
   - Implements persistent generation caching (`llm_generation_cache.json`) to avoid re-querying Gemini API when re-running notebooks.
   - Robust retry mechanics (up to 3 retries with exponential backoff) with explicit error handling.
3. **Google Drive Caching & Reuse**:
   - `youtube_video_comments.ipynb` and `youtube_comment_llm_summaries.ipynb` automatically check Google Drive (`/content/drive/MyDrive/YouTube_Analytics/`) for cached datasets before executing requests.
4. **Shorts Exclusion via Playlist ID & Duration**:
   - Accesses YouTube's long-form uploads playlist by substituting the channel ID prefix `UC...` / `UU...` with `UULF...`.
   - Parses video ISO 8601 durations (`contentDetails.duration`) and filters out any video $\le$ 60 seconds to ensure strict long-form isolation.
5. **Google Drive Persistence**:
   - Mounts Google Drive (`google.colab.drive`) directly within Google Colab.
   - Saves formatted `.csv`, `.json`, and `.md` data files into `/content/drive/MyDrive/YouTube_Analytics/`.
6. **Comprehensive Visual Analytics**:
   - **Video Analytics**: Top viewed videos, view timeline, duration vs. view count, engagement rate distribution, upload frequency by day.
   - **Comment Analytics**: Top 10 most liked comments across videos, top comments collected per video, character length distribution, and likes vs. reply count scatter plot.
   - **LLM Synthesis Analytics**: Comment sample volume per video, comment character distributions, and LLM synthesis status.

---

## 🚀 How to Run in Google Colab

1. **Open Google Colab**:
   - Upload either notebook (`youtube_channel_analytics.ipynb` or `youtube_video_comments.ipynb`) directly to [Google Colab](https://colab.research.google.com/).

2. **Obtain YouTube Data API Key**:
   - Go to [Google Cloud Console](https://console.cloud.google.com/).
   - Create or select a project.
   - Enable **YouTube Data API v3**.
   - Create an API Key under **Credentials**.

3. **Configure API Key in Colab**:
   - *Option A (Recommended)*: Add your API key in Google Colab Secrets (Key icon on left sidebar in Colab) named `YOUTUBE_API_KEY`.
   - *Option B*: Paste your key directly in the `CONFIG` dictionary in Section 1 of the notebook:
     ```python
     CONFIG = {
         "YOUTUBE_API_KEY": "YOUR_YOUTUBE_API_KEY_HERE",
         "CHANNEL_IDENTIFIER": "@josemillanastrologohumanista",
         "TARGET_VIDEO_COUNT": 10,
         "MAX_COMMENTS_PER_VIDEO": 50,
         "DRIVE_OUTPUT_DIR": "/content/drive/MyDrive/YouTube_Analytics",
         "MOUNT_DRIVE": True,
         "USE_CACHE": True
     }
     ```

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

### Market Research LLM Summaries Output:
- `{Channel_Name}_market_research_summary.md`
- `{Channel_Name}_market_research_summary.json`

### Collected Comment Dataset Attributes:
- `video_id` & `video_title` & `video_publish_date`
- `comment_id`
- `author_name` & `author_channel_url` & `author_profile_image`
- `comment_text` (HTML/Formatted) & `comment_text_clean` (Plain Text)
- `like_count` & `reply_count`
- `published_at` & `updated_at` & `comment_date`
- `char_length` & `word_count`
