# Recursive Crawler#

**Recursive Web Crawler** — BFS traversal for deep web content.

## Overview#

Crawls web resources recursively using breadth-first search algorithm.

## Features#

- **BFS traversal** — breadth-first search#
- **Depth limiting** — configurable crawl depth#
- **Domain filtering** — stay within domain boundaries#
- **Content extraction** — extract text, links, metadata#
- **Rate limiting** — respect robots.txt and rate limits#
- **Duplicate detection** — avoid re-crawling content#

## Configuration#

Environment variables:
- `CRAWL_MAX_DEPTH` — maximum crawl depth (default: `3`)#
- `CRAWL_MAX_PAGES` — maximum pages per crawl (default: `1000`)#
- `CRAWL_DELAY` — delay between requests (default: `1.0` seconds)#
- `CRAWL_USER_AGENT` — user agent string#

## API Endpoints#

- `POST /deepintel/recursive-crawler/crawl` — start recursive crawl#
- `GET /deepintel/recursive-crawler/status/{id}` — check crawl status#
- `GET /deepintel/recursive-crawler/results/{id}` — get crawl results#
- `DELETE /deepintel/recursive-crawler/{id}` — cancel crawl#

## Usage#

```python
from cosmicsec_deepintel import RecursiveCrawler#

crawler = RecursiveCrawler()
results = crawler.crawl("https://example.com", max_depth=2)
```
