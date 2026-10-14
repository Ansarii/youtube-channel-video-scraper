# YouTube Channel & Video Scraper: Search, Stats & Metadata

Collect YouTube video data at scale: **search results, a channel's uploads, playlists or specific videos**, each returned with the **exact view count, likes, publish date, length, tags, description, thumbnail and the channel's subscriber count**. One row per video, ready for CSV, JSON or Excel.

Use it for influencer and competitor research, trend and keyword tracking, content calendars, dataset building and AI pipelines. Pair it with the [YouTube Transcript Scraper](https://apify.com/neon_innovation_lab/youtube-transcript-scraper) when you also need what is said in the videos.

## What you get

| Field | Description |
|---|---|
| `videoId`, `url`, `title`, `description` | Video identity and text |
| `channelName`, `channelId`, `channelUrl`, `channelSubscribers` | Channel and its subscriber count |
| `publishDate`, `lengthSeconds` | When it was published and how long it is |
| `viewCount`, `likeCount` | Exact views, and likes when public |
| `category`, `tags`, `thumbnailUrl`, `hasCaptions`, `isLive` | Extra metadata |
| `sourceType`, `source`, `rank` | Which query, channel or playlist found it and its position |
| `status` | `ok`, `unavailable` or `error` |

```json
{
  "status": "ok",
  "videoId": "dQw4w9WgXcQ",
  "title": "Rick Astley - Never Gonna Give You Up",
  "channelName": "Rick Astley",
  "channelSubscribers": 4200000,
  "publishDate": "2009-10-25",
  "viewCount": 1600000000,
  "sourceType": "search",
  "source": "never gonna give you up",
  "rank": 1
}
```

(Shortened, illustrative values.)

## Input

| Field | Default | Notes |
|---|---|---|
| `searchQueries` | none | Search terms; choose the sort with `searchSort` (relevance, date, views, rating) |
| `channels` | none | Channel URLs, ids or `@handles`; returns uploads newest first |
| `playlists` | none | Playlist URLs or ids |
| `videos` | none | Individual video URLs or ids |
| `maxVideosPerSource` | `30` | Limit for each query, channel and playlist |
| `minViews` | `0` | Skip videos below this view count (skipped videos are free) |
| `includeDescription`, `includeChannelInfo` | `true` | Turn off to speed up and slim output |

```json
{ "searchQueries": ["MCP server tutorial"], "channels": ["@fireship"], "maxVideosPerSource": 50, "searchSort": "date", "minViews": 10000 }
```

At least one of `searchQueries`, `channels`, `playlists` or `videos` is required.

## Pricing (pay per event)

| Event | Price |
|---|---|
| Video scraped | $0.003 per video |
| Actor start | $0.00005 per run |

That is **$3 per 1,000 videos**; 200 videos cost about $0.60. Unavailable videos and videos skipped by `minViews` are not charged. Set a maximum cost per run in the Apify console.

## Why it is reliable

YouTube blocks most datacenter traffic. This Actor uses residential proxies by default, keeps one IP per video, rotates and retries on bot checks, then makes a slower second pass for anything throttled, so you get a status for every video instead of silent gaps.

## Limits

- Likes are only returned when the creator shows them. Subscriber counts are the rounded figures YouTube displays.
- Search and channel listings return what YouTube serves to anonymous visitors; very deep results may be capped by YouTube.
- Private, deleted and age-restricted videos return `unavailable`.
- Respect YouTube's terms and applicable law when using the data.

## FAQ

**Can I scrape a whole channel?** Yes: pass the channel and raise `maxVideosPerSource`.

**Can I find only popular videos?** Use `searchSort: "views"` and `minViews`.

**Do you return comments?** Not in this Actor. It focuses on video and channel metadata.

**Something broke?** Open an issue from the Issues tab with the run URL and your input.


## Run it

This repository documents the hosted Actor. Run it from the [Apify Store](https://apify.com/neon_innovation_lab/youtube-channel-video-scraper), or from code:

```python
from apify_client import ApifyClient
client = ApifyClient("<APIFY_TOKEN>")
run = client.actor("neon_innovation_lab/youtube-channel-video-scraper").call(run_input={})  # add the input fields from the table above
for item in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(item)
```

Built by [Neon Innovation Lab](https://neoninnovationlab.com/actors).
