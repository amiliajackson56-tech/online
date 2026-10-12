# News Site V4 — scalable RSS aggregation

This version keeps the static website and GitHub Actions workflow, but replaces the hard-coded list of six feeds with `feeds.json`. The collector creates combined category feeds under `feeds/`.

## How to add sources

1. Open `feeds.json`.
2. Add an entry to `sources` with a readable `name`, the publisher's RSS/Atom `url`, a `category`, and `enabled: true`.
3. Commit and push the change to GitHub.
4. In GitHub, open **Actions → Update news → Run workflow** to test immediately. Otherwise, it runs on the 15-minute schedule.

Example:

```json
{
  "name": "Example Publisher",
  "url": "https://example.com/feed.xml",
  "category": "world",
  "enabled": true
}
```

Supported categories: `nigeria`, `world`, `entertainment`, `sports`, `technology`, `science`, `business`, `gaming`, `fashion`, `other`. The `top` feed is generated automatically from all categories.

## Run locally

Requires Python 3.12+ and `feedparser`:

```bash
python -m pip install feedparser
python scripts/fetch_news.py
```

## Notes and limitations

- There is no fixed source count in the configuration. Practical throughput is limited by GitHub Actions runtime, network access, feed publisher rate limits, and the size of generated files. The collector processes up to 100 recent entries per source per run and outputs up to 5,000 items per generated feed.
- Feeds may be unavailable, invalid, blocked, or temporarily empty. A failed source is logged and does not stop other sources. If every configured source fails, the job fails instead of replacing feeds with empty results.
- Articles are deduplicated by canonical URL and normalized title across sources. This is intentionally conservative and may merge two stories with identical titles.
- RSS/Atom feeds may not provide an image or summary for every article. Images are used when supplied in the feed; otherwise the card can display without an image.
- The website links to original articles and displays metadata/short summaries rather than copying full article text. Respect publisher terms, robots policies, copyright, and feed/API terms.
- GitHub Actions schedules are not guaranteed to start at the exact minute. GitHub Pages may also take time to publish a commit.
- Keep the repository public/private as desired, but do not put API keys or secrets in `feeds.json`.


## DailyPulse responsive interface

The homepage now uses the DailyPulse brand and a responsive layout: stacked image-led cards on phones and a two-column article-card grid on desktop/tablet widths. The RSS category filters, refresh button, local generated feeds, original publisher links, and automatic refresh remain in place.
