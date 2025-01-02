
# Concurrent Web Crawling with Tornado: A Queue Example

Tornado's [`tornado.queues`](https://tornado-zh.readthedocs.io/zh/latest/queues.html#module-tornado.queues) module implements an asynchronous producer/consumer pattern using coroutines. This design is comparable to the thread-based implementation in Python's standard library [`queue`](https://docs.python.org/3.4/library/queue.html#module-queue).

## Understanding Tornado Queues

A coroutine yielding [`Queue.get`](https://tornado-zh.readthedocs.io/zh/latest/queues.html#tornado.queues.Queue.get) will pause execution until the queue contains a value. Similarly, if a queue has a maximum size, a coroutine yielding [`Queue.put`](https://tornado-zh.readthedocs.io/zh/latest/queues.html#tornado.queues.Queue.put) will pause until there is space available.

Each [`Queue`](https://tornado-zh.readthedocs.io/zh/latest/queues.html#tornado.queues.Queue) starts with a task count of zero. The [`put`](https://tornado-zh.readthedocs.io/zh/latest/queues.html#tornado.queues.Queue.put) method increases the count, while [`task_done`](https://tornado-zh.readthedocs.io/zh/latest/queues.html#tornado.queues.Queue.task_done) decreases it.

## A Practical Web Crawler Example

In the following example, a web crawler starts with a base URL in the queue. Each worker fetches a page, parses the links, adds new links to the queue, and then calls `task_done` to signal that the task is complete. Once all URLs have been fetched and the queue is empty, the main coroutine resumes and the process concludes.

---

**Stop wasting time on proxies and CAPTCHAs!** ScraperAPI’s simple API handles millions of web scraping requests, letting you focus on the data. Get structured data from Amazon, Google, Walmart, and more. **Start your free trial today!** 👉 [https://www.scraperapi.com/?fp_ref=coupons](https://www.scraperapi.com/?fp_ref=coupons)

---

### Example Code: Tornado Queue-Based Web Crawler

```python
import time
from datetime import timedelta

try:
    from HTMLParser import HTMLParser
    from urlparse import urljoin, urldefrag
except ImportError:
    from html.parser import HTMLParser
    from urllib.parse import urljoin, urldefrag

from tornado import httpclient, gen, ioloop, queues

base_url = 'http://www.tornadoweb.org/en/stable/'
concurrency = 10


@gen.coroutine
def get_links_from_url(url):
    """Download the page at `url` and parse it for links.

    Returned links have had the fragment after `#` removed, and have been made
    absolute so, e.g., the URL 'gen.html#tornado.gen.coroutine' becomes
    'http://www.tornadoweb.org/en/stable/gen.html'.
    """
    try:
        response = yield httpclient.AsyncHTTPClient().fetch(url)
        print('fetched %s' % url)

        html = response.body if isinstance(response.body, str) \
            else response.body.decode()
        urls = [urljoin(url, remove_fragment(new_url))
                for new_url in get_links(html)]
    except Exception as e:
        print('Exception: %s %s' % (e, url))
        raise gen.Return([])

    raise gen.Return(urls)


def remove_fragment(url):
    pure_url, frag = urldefrag(url)
    return pure_url


def get_links(html):
    class URLSeeker(HTMLParser):
        def __init__(self):
            HTMLParser.__init__(self)
            self.urls = []

        def handle_starttag(self, tag, attrs):
            href = dict(attrs).get('href')
            if href and tag == 'a':
                self.urls.append(href)

    url_seeker = URLSeeker()
    url_seeker.feed(html)
    return url_seeker.urls


@gen.coroutine
def main():
    q = queues.Queue()
    start = time.time()
    fetching, fetched = set(), set()

    @gen.coroutine
    def fetch_url():
        current_url = yield q.get()
        try:
            if current_url in fetching:
                return

            print('fetching %s' % current_url)
            fetching.add(current_url)
            urls = yield get_links_from_url(current_url)
            fetched.add(current_url)

            for new_url in urls:
                # Only follow links beneath the base URL
                if new_url.startswith(base_url):
                    yield q.put(new_url)

        finally:
            q.task_done()

    @gen.coroutine
    def worker():
        while True:
            yield fetch_url()

    q.put(base_url)

    # Start workers, then wait for the work queue to be empty.
    for _ in range(concurrency):
        worker()
    yield q.join(timeout=timedelta(seconds=300))
    assert fetching == fetched
    print('Done in %d seconds, fetched %s URLs.' % (
        time.time() - start, len(fetched)))


if __name__ == '__main__':
    import logging
    logging.basicConfig()
    io_loop = ioloop.IOLoop.current()
    io_loop.run_sync(main)
```

### Key Highlights of This Example

- **Concurrency:** Multiple workers fetch URLs in parallel, improving efficiency.
- **Queue Management:** Tasks are added and completed dynamically, allowing for adaptive crawling.
- **Resilient Parsing:** Links are normalized and deduplicated, ensuring a clean list of URLs to process.

## Why Use Tornado for Concurrent Crawling?

Tornado's asynchronous capabilities make it ideal for high-performance, non-blocking I/O operations such as web crawling. Key advantages include:

- **Built-in Queues:** Simplify the producer/consumer model for managing tasks.
- **Asynchronous Fetching:** Handles hundreds of requests without blocking the main thread.
- **Scalability:** Tornado's design scales well for high-concurrency applications.

---

**Don’t let scraping challenges slow you down!** Simplify your web scraping workflow with ScraperAPI. Manage proxies, CAPTCHAs, and more effortlessly. **Start your free trial now!** 👉 [https://www.scraperapi.com/?fp_ref=coupons](https://www.scraperapi.com/?fp_ref=coupons)

---
