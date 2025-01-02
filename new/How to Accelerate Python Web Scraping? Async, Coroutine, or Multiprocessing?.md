
# How to Accelerate Python Web Scraping? Async, Coroutine, or Multiprocessing?

## Introduction



Recently, in the **Python Readers Circle** community, a popular question came up:

> *How can I accelerate a Python scraper to download 10,000 images efficiently and reduce runtime?*

This is a critical question not only in web scraping but also in Python programming as a whole. Today, we’ll explore practical solutions to accelerate your scraping tasks and make them run significantly faster.

---

> **Stop wasting time on slow scraping methods!**  
> With **ScraperAPI**, handle millions of requests, bypass CAPTCHAs and proxies effortlessly. Focus on collecting the data while ScraperAPI takes care of the technical challenges.  
> **Start your free trial 👉 [https://www.scraperapi.com/?fp_ref=coupons](https://www.scraperapi.com/?fp_ref=coupons)**  

---

## 1. Why Is Your Scraper Slow?

Let’s analyze why your current scraping code might be running slowly. Below is an example of sequential execution:

```python
import office

for i in range(1, 18):
    url = 'https://www.python-office.com/api/img-cdn/test/spider/{}.jpg'.format(str(i))
    office.image.down4img(url, output_name=str(i))
```

While the code looks simple and clean, there is a significant problem: **sequential execution**. To better understand this, consider the following analogy:

Imagine you have 10 washing machines, each with clothes of varying dirtiness. Some require heavy washing, while others need quick cleaning. You can process them in two ways:

1. **Sequential Processing:** Start one washing machine, wait until it finishes, then start the next one. This ensures a perfect order but takes longer.
2. **Parallel Processing:** Start all washing machines simultaneously and record whichever finishes first. This approach uses time more efficiently.

Clearly, **parallel processing** is faster because it utilizes resources simultaneously. Similarly, in web scraping, downloading images sequentially (waiting for one request to finish before starting another) is inefficient.

---

## 2. The Solution: Asynchronous Code

To speed things up, we can implement the second approach: **parallel execution**. In Python, this can be achieved using **asyncio** for asynchronous tasks. Here’s how you can modify the code:

### Asynchronous Code Example

```python
import asyncio
from aiohttp import ClientSession
import aiofiles

tasks = []
url = "https://www.python-office.com/api/img-cdn/test/spider/{}.jpg"

async def download_image(url, i='image', filetype='jpg'):
    async with ClientSession() as session:
        async with session.get(url) as response:
            if response.status == 200:
                content = await response.read()
                async with aiofiles.open(f"{i}.{filetype}", 'wb') as output_file:
                    await output_file.write(content)
                print(f"Downloaded successfully: {i}.{filetype}")

def run():
    for i in range(1, 18):
        task = asyncio.ensure_future(download_image(url.format(i), str(i)))
        tasks.append(task)

def main():
    loop = asyncio.get_event_loop()
    run()
    loop.run_until_complete(asyncio.wait(tasks))

if __name__ == '__main__':
    main()
```

### Key Libraries Used:
1. **`asyncio`:** Enables asynchronous coroutines for faster execution.
2. **`aiohttp`:** Replaces `requests` for asynchronous HTTP requests.
3. **`aiofiles`:** Asynchronously writes image files to disk.

This method speeds up the process by overlapping the time required to fetch images and write them to disk.

---

## 3. What About Multiprocessing?

While multiprocessing can also improve performance, its advantages are more pronounced in **CPU-intensive tasks**. Since web scraping involves **I/O-bound tasks** (e.g., making network requests), multiprocessing offers only marginal benefits compared to asynchronous programming.

### Multiprocessing Example

```python
import multiprocessing
from multiprocessing import Pool
import requests

url = "https://www.python-office.com/api/img-cdn/test/spider/{}.jpg"

def download_image(url, output_name, filetype):
    response = requests.get(url, stream=True)
    with open(f"{output_name}.{filetype}", 'wb') as output_file:
        for chunk in response.iter_content(1024):
            output_file.write(chunk)
    print(f"Downloaded: {output_name}.{filetype}")

def main():
    print(f"Main process starts: pid={os.getpid()}")
    pool = Pool(multiprocessing.cpu_count())

    for i in range(1, 18):
        pool.apply_async(download_image, args=(url.format(str(i)), str(i), 'jpg'))
    
    pool.close()
    pool.join()
    print("All downloads completed.")

if __name__ == '__main__':
    main()
```

### Key Differences:
- **Asyncio** is better suited for network-bound tasks, such as scraping.
- **Multiprocessing** is ideal for computationally heavy tasks, such as image processing.

---

## 4. Which Method Should You Use?

| **Scenario**                | **Recommended Method**      |
|-----------------------------|-----------------------------|
| Network-bound (e.g., scraping, downloading images) | **Asyncio with aiohttp** |
| CPU-bound (e.g., image processing, data transformation) | **Multiprocessing**      |

---

## Conclusion

To accelerate Python web scraping:

1. Use **asyncio** for non-blocking, asynchronous requests to maximize efficiency.
2. Use **multiprocessing** only when computationally heavy tasks dominate your workflow.
3. Always optimize I/O operations to minimize bottlenecks.

---

> **Simplify your scraping projects with ScraperAPI!**  
> ScraperAPI handles CAPTCHAs, proxies, and request throttling for you, so you can focus on building better scrapers.  
> **Start scraping smarter today 👉 [https://www.scraperapi.com/?fp_ref=coupons](https://www.scraperapi.com/?fp_ref=coupons)**  
