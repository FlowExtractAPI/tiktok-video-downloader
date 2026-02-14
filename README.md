# TikTok Video Downloader

Download TikTok videos without watermark. Extract direct download links from TikTok URLs with full metadata including views, likes, shares, and comments.

## Features

- 🎬 **Extract Video URLs** - Get direct playable video links from TikTok
- 📊 **Full Metadata** - Views, likes, shares, comments, duration, quality
- 💾 **Optional Download** - Store videos in Apify Key-Value Store with persistent links
- 🔄 **Resumable Runs** - State persistence for interrupted runs
- 🎯 **FPS Selection** - Choose between 30 or 60 FPS quality
- 🔁 **Auto Retry** - Automatic retry with exponential backoff for failed requests
- 🌐 **Proxy Support** - Built-in proxy configuration for better success rates

## Input

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `videoUrls` | array | ✅ | - | TikTok video URLs to process |
| `preferredFps` | string | ❌ | `"60"` | Video quality: `"30"` or `"60"` FPS |
| `downloadVideo` | boolean | ❌ | `false` | Store videos on Apify servers |
| `proxyConfig` | object | ❌ | - | Proxy configuration |

### Example Input

```json
{
    "videoUrls": [
        { "url": "https://www.tiktok.com/@username/video/1234567890" },
        { "url": "https://vm.tiktok.com/ABC123" }
    ],
    "preferredFps": "60",
    "downloadVideo": true
}
```

## 📊 Sample Output Structure

![Sample Output](https://raw.githubusercontent.com/DZ-ABDLHAKIM/tiktok-video-downloader/refs/heads/main/tiktok-scraper-sample-output.png)


Each video produces a dataset item with the following structure:

```json
{
    "videoUrl": "https://www.tiktok.com/@user/video/123",
    "videoId": "1234567890",
    "directUrl": "https://v16-webapp.tiktok.com/...",
    "title": "Video description text",
    "author": "Creator Name",
    "authorId": "123456789",
    "Cover": "https://p16-sign.tiktok.com/...",
    "duration": 15,
    "viewCount": 100000,
    "likeCount": 5000,
    "shareCount": 200,
    "commentCount": 150,
    "width": 576,
    "height": 1024,
    "quality": "540p",
    "fileSize": "1234567",
    "extractedAt": "2024-01-15T10:30:00.000Z",
    "download": {
        "available": true,
        "url": "https://api.apify.com/v2/key-value-stores/.../records/...",
        "format": "mp4",
        "status": "completed",
        "fileSizeHuman": "1.5 MB"
    },
    "processingTime": 2500,
    "success": true
}
```

## Dataset Views

The Actor provides four pre-configured dataset views:

1. **Overview** - Quick summary with video ID, title, author, and status
2. **Statistics** - Engagement metrics (views, likes, shares, comments)
3. **Downloads** - Download links and file information
4. **Errors** - Failed extractions with error details

## State Persistence & Resumability

This Actor supports **resumable runs**. If a run is interrupted (migration, timeout, abort), it will automatically resume from where it left off on the next run with the same input.

State is automatically saved:
- Every 3 processed videos
- On Actor migration events
- On Actor abort events
- Before Actor exit

To disable state persistence, set `enableStatePersistence: false` in input.

## Proxy Configuration

For better success rates, especially with high-volume extractions:

```json
{
    "proxyConfig": {
        "useApifyProxy": true,
        "apifyProxyGroups": ["RESIDENTIAL"],
        "apifyProxyCountry": "US"
    }
}
```

## Error Handling

The Actor implements intelligent retry logic:

| Error Type | Retryable | Behavior |
|------------|-----------|----------|
| Network timeout | ✅ | Exponential backoff |
| Rate limiting (429) | ✅ | Extended backoff |
| Server errors (5xx) | ✅ | Standard backoff |
| Video not found (404) | ❌ | Immediate fail |
| Private/restricted video | ❌ | Immediate fail |
| Invalid URL | ❌ | Immediate fail |

## Run Summary

After completion, a summary is saved to the Key-Value Store under the key `RUN_SUMMARY`:

```json
{
    "total": 10,
    "processed": 10,
    "successful": 9,
    "failed": 1,
    "skipped": 0,
    "retried": 2,
    "downloads": {
        "enabled": true,
        "successful": 9,
        "failed": 0
    },
    "resumed": false,
    "completedAt": "2024-01-15T10:35:00.000Z"
}
```

## Limitations

- TikTok direct URLs may expire (use `downloadVideo: true` for persistent links)
- Some region-restricted videos may not be accessible
- Private videos cannot be extracted

---

## 🤝 Support & Resources

- 🌐 **Website**: [flowextractapi.com](https://flowextractapi.com)
- 📧 **Email**: [flowextractapi@outlook.com](mailto:flowextractapi@outlook.com)
- 🙋 **Apify Profile**: [dz_omar](https://apify.com/dz_omar?fpr=smcx63)
- 💬 **GitHub Issues**: [FlowExtractAPI](https://github.com/FlowExtractAPI)

### Social Media

- 💼 **LinkedIn**: [flowextract-api](https://www.linkedin.com/in/flowextract-api/)
- 🐦 **Twitter**: [@FlowExtractAPI](https://x.com/@FlowExtractAPI)
- 📱 **Facebook**: [flowextractapi](https://www.facebook.com/flowextractapi)

### Related Actors
- **[Universal File Downloader](https://apify.com/dz_omar/universal-downloader?fpr=smcx63)**: Powers the intelligent download system
- **TikTok Profile Scraper**: Extract user profiles and video lists
- **Social Media Analytics**: Comprehensive social media data extraction

---
