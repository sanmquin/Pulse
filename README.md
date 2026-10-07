# YouTube Long-Form Videos Analytics Notebook

This repository contains a Google Colab notebook (`youtube_channel_analytics.ipynb`) that retrieves titles, metrics, and metadata for the last **50 long-form videos** of a specified YouTube channel, saves the data to **Google Drive** in CSV and JSON formats, and produces visual analytical charts.

---

## 🌟 Key Features

1. **API Quota Efficient**: Avoids expensive `search.list` endpoints (100 units quota per call). Instead, it uses `channels.list` (1 unit), `playlistItems.list` (1 unit per 50 videos), and `videos.list` (1 unit per 50 videos).
2. **Shorts Exclusion via Playlist ID & Duration**:
   - Accesses YouTube's long-form uploads playlist by substituting the channel ID prefix `UC...` / `UU...` with `UULF...`.
   - Parses video ISO 8601 durations (`contentDetails.duration`) and filters out any video $\le$ 60 seconds to ensure strict long-form isolation.
3. **Google Drive Persistence**:
   - Mounts Google Drive (`google.colab.drive`) directly within Google Colab.
   - Saves formatted `.csv` and structured `.json` data files into `/content/drive/MyDrive/YouTube_Analytics/`.
4. **Comprehensive Data Visualizations**:
   - **Chart 1**: Top 10 Most Viewed Videos (Horizontal Bar Chart)
   - **Chart 2**: Views Over Time / Upload Timeline (Line Chart)
   - **Chart 3**: Video Duration vs. View Count & Engagement (Scatter Plot)
   - **Chart 4**: Engagement Rate % Distribution across top videos
   - **Chart 5**: Upload Frequency by Day of Week

---

## 🚀 How to Run in Google Colab

1. **Open Google Colab**:
   - Upload `youtube_channel_analytics.ipynb` directly to [Google Colab](https://colab.research.google.com/).

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
         "CHANNEL_IDENTIFIER": "@MKBHD",
         "TARGET_VIDEO_COUNT": 50,
         "DRIVE_OUTPUT_DIR": "/content/drive/MyDrive/YouTube_Analytics",
         "MOUNT_DRIVE": True
     }
     ```

4. **Execute Cells**:
   - Click `Runtime` -> `Run all` or run each cell sequentially.
   - When prompted, grant permission to mount Google Drive.

---

## 📊 Output Files

The notebook automatically exports:
- `{Channel_Name}_last_50_longform_videos.csv`
- `{Channel_Name}_last_50_longform_videos.json`

### Dataset Attributes Collected:
- `video_id` & `url`
- `title`
- `published_at` & `publish_date` & `publish_day_of_week` & `publish_hour_utc`
- `duration_iso`, `duration_seconds`, `duration_formatted`
- `view_count`, `like_count`, `comment_count`
- `likes_per_1k_views`, `comments_per_1k_views`, `engagement_rate_%`
- `thumbnail_url`
