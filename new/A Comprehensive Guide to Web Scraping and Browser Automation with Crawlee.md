
# A Comprehensive Guide to Web Scraping and Browser Automation with Crawlee

Crawlee is your ultimate solution for web scraping and crawling, offering end-to-end support to **build reliable scrapers quickly and effectively**.

> 🚀 Crawlee for Python is now available for early adopters!

## Why Choose Crawlee?

Crawlee empowers you to create human-like crawlers that bypass modern bot detection systems, even with default settings. It provides tools to crawl the web for links, scrape data, and store it persistently in machine-readable formats. Its rich configuration options allow you to tweak almost every aspect of your crawler to suit your project's unique requirements.

---

**Stop wasting time on proxies and CAPTCHAs!** ScraperAPI’s simple API handles millions of web scraping requests, letting you focus on the data. Get structured data from Amazon, Google, Walmart, and more. **Start your free trial today!** 👉 [https://www.scraperapi.com/?fp_ref=coupons](https://www.scraperapi.com/?fp_ref=coupons)

---

Crawlee is available in both Python and TypeScript implementations. While this guide focuses on Crawlee for Python, the TypeScript version is equally robust and can be explored on [GitHub](https://github.com/apify/crawlee).

### Getting Started with Crawlee

Crawlee is distributed via PyPI. The core functionality is included in the base package, and additional features are available as optional extras. To install Crawlee with all features, run the following:

```bash
pip install 'crawlee[all]'
```

Then install the necessary dependencies for [Playwright](https://playwright.dev/):

```bash
playwright install
```

To verify the installation, run:

```bash
python -c 'import crawlee; print(crawlee.__version__)'
```

For detailed instructions, visit the [Setting Up](https://crawlee.dev/python/docs/introduction/setting-up) section in Crawlee's documentation.

## Fast Setup with Crawlee CLI

The quickest way to get started with Crawlee is by using the CLI. Ensure you have [Pipx](https://pipx.pypa.io/) installed, then run the following command to create a new crawler project:

```bash
pipx run crawlee create my-crawler
```

If Crawlee is already installed, you can simply use:

```bash
crawlee create my-crawler
```

## Practical Examples

Crawlee supports multiple types of crawlers to suit various web scraping scenarios:

1. **[BeautifulSoupCrawler](https://crawlee.dev/python/api/class/BeautifulSoupCrawler):** Ideal for extracting data from HTML content without executing JavaScript. This crawler uses `HttpxHttpClient` for HTTP communication and `BeautifulSoup` for parsing.

   To use it, ensure Crawlee is installed with the `beautifulsoup` extra:

   ```bash
   pip install 'crawlee[beautifulsoup]'
   ```

2. **[PlaywrightCrawler](https://crawlee.dev/python/api/class/PlaywrightCrawler):** Uses a headless browser to handle JavaScript-heavy websites. Built on Playwright, it excels in scenarios requiring interaction with JavaScript-driven content.

   To use it, ensure Crawlee is installed with the `playwright` extra:

   ```bash
   pip install 'crawlee[playwright]'
   ```

### PlaywrightCrawler Example

Here’s an example of setting up a crawler with Playwright:

```python
import asyncio
from crawlee.playwright_crawler import PlaywrightCrawler, PlaywrightCrawlingContext

async def main():
    crawler = PlaywrightCrawler(max_requests_per_crawl=10)

    @crawler.router.default_handler
    async def request_handler(context: PlaywrightCrawlingContext):
        context.log.info(f'Processing {context.request.url} ...')
        data = {'url': context.request.url, 'title': await context.page.title()}
        await context.push_data(data)
        await context.enqueue_links()

    await crawler.run(['https://crawlee.dev'])

if __name__ == '__main__':
    asyncio.run(main())
```

For more use cases, explore the [Examples](https://crawlee.dev/python/docs/examples) section in the documentation.

## Advantages of Crawlee Over Traditional Libraries

- **Unified Interface:** Combines HTTP and headless browser crawling.
- **Asyncio-Based:** Delivers high performance and compatibility with modern Python libraries.
- **Automatic Parallel Crawling:** Utilizes available system resources efficiently.
- **Proxy Rotation and Session Management:** Prevents IP bans and improves scraping reliability.
- **Type Hints:** Enhances development experience and reduces bugs.
- **Flexible Integration:** Easily integrates with other Python applications.

## Crawlee vs. Scrapy

- **Modern Technology Stack:** Crawlee leverages `asyncio` for better performance.
- **Simplified Development:** Type hints and modern Python features improve the developer experience.
- **State Persistence:** Saves scraping progress, preventing data loss during interruptions.
- **Organized Data Storage:** Supports multiple data types and storage options.

## Running on the Apify Platform

While Crawlee is open-source and can run anywhere, it integrates seamlessly with the [Apify platform](https://apify.com) for cloud-based deployment. Learn more about setting up Crawlee on Apify [here](https://docs.apify.com/sdk/python/).

## Get Started Today

Whether you’re building a simple scraper or a complex crawler, Crawlee provides the tools you need to succeed. For bug reports or issues, visit [GitHub Issues](https://github.com/apify/crawlee-python/issues), or join our [Discord server](https://discord.com/invite/jyEM2PRvMU) for community support.

**Ready to enhance your web scraping projects?** Get started with Crawlee today!

---

**Don’t let scraping obstacles slow you down!** ScraperAPI simplifies web scraping by handling proxies, CAPTCHAs, and more. Focus on what matters—data collection. Start your free trial now! 👉 [https://www.scraperapi.com/?fp_ref=coupons](https://www.scraperapi.com/?fp_ref=coupons)

---
