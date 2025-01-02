
# Step-by-Step Guide to Building a Web Scraper with Java

## Introduction

**This article will walk you through building a simple web scraper from scratch using Java!**

![Web Scraper Example](https://ask.qcloudimg.com/http-save/yehe-1342517/coccmcyl12.jpeg)

### Objective

The goal is to scrape images from a website and download them to your local machine.

Through this guide, you’ll review the following concepts:

- Creating a project in **IDEA**
- Importing JAR libraries in **IDEA**
- Basics of web scraping
- Using **Jsoup**
- Working with **File** and **FileOutputStream**
- Using **ArrayList**
- Iterating with **foreach**

---

> **Stop wasting time on proxies and CAPTCHAs!**  
> ScraperAPI simplifies web scraping by handling millions of requests for you. Collect data from Amazon, Google, Walmart, and more with ease.  
> **Start your free trial today 👉 [https://www.scraperapi.com/?fp_ref=coupons](https://www.scraperapi.com/?fp_ref=coupons)**  

---

## Using Jsoup for HTML Parsing

The HTML parser for this project is **Jsoup**. It can directly parse URLs or HTML text content and provides a user-friendly API for extracting and manipulating data using DOM, CSS, or jQuery-like methods.

---

## Step 1: Front-End Analysis

1. Open the target website in Chrome or another browser. Press `F12` to enter debug mode and analyze the webpage structure.  
   (In this example, we are analyzing the "Creative" → "Yosemite" section.)
   
   ![Step 1](https://ask.qcloudimg.com/http-save/yehe-1342517/tys2mrc1j9.jpeg)

2. Identify the structure of the elements containing the images. Each image has a structure similar to the red box below.

   ![Step 2](https://ask.qcloudimg.com/http-save/yehe-1342517/ymmz7o6dho.jpeg)

3. Extract the image links. Further analysis reveals that the image links are located in the red box below.

   ![Step 3](https://ask.qcloudimg.com/http-save/yehe-1342517/fttt4w4kzl.jpeg)

4. Copy the URL into your browser to test it. (The browser may immediately download the image upon visiting the URL.)

   ![Step 4](https://ask.qcloudimg.com/http-save/yehe-1342517/pyqywnhba8.jpeg)

5. With the front-end analysis complete, you’re ready to start coding in Java!

---

## Step 2: Web Scraping Strategy

- Send a **GET** request to the website using Java to fetch the **HTML** document.
- Use **Jsoup** to parse the HTML and find `<a>` tags with the class `item lazy`. The child node (`<img>`) of these tags will contain the target image links.
- Extract the image URLs into an **ArrayList**.
- Iterate through the list and download each image to your local machine.

---

## Step 3: Coding the Web Scraper

### 1. Create a New Project in IDEA

- Set up a new Java project in IntelliJ IDEA.

![New Project](https://ask.qcloudimg.com/http-save/yehe-1342517/fmycmx7smc.jpeg)

---

### 2. Test the GET Request

- Write a simple test to ensure the GET request is working. If there’s an error, check for non-encoded characters in the URL.

![GET Test](https://ask.qcloudimg.com/http-save/yehe-1342517/ndvzoumccp.jpeg)

---

### 3. Parse HTML and Extract Image URLs

- Use **Jsoup** to find elements with the class `item lazy` and retrieve their child nodes (`<img>` tags).
- Extract the image URLs into an **ArrayList**.

![Parse HTML](https://ask.qcloudimg.com/http-save/yehe-1342517/76z438j1q3.jpeg)

---

### 4. Test Downloading a Single Image

- Download a single image using **Jsoup** and save it as `demo.jpg`.

![Image Download](https://ask.qcloudimg.com/http-save/yehe-1342517/jkqnnxfdi4.jpeg)

---

### 5. Create a Folder for Storing Images

- Create a directory in your local project to save the images.

![Create Folder](https://ask.qcloudimg.com/http-save/yehe-1342517/rbw7kixvn9.jpeg)

---

### 6. Download All Images

- Loop through the **ArrayList** of image URLs and download each image. Name the files numerically (e.g., `1.jpg`, `2.jpg`, etc.).

![Download All Images](https://ask.qcloudimg.com/http-save/yehe-1342517/07e39e09jv.jpeg)

---

## Final Output

- Once the program is complete, you’ll have a folder full of images downloaded from the website.

---

## Appendix 1: Jsoup Overview

### Jsoup (HTML Parser)

**Key Features:**

- DOM manipulation
- CSS and jQuery-like selectors
- Clean and easy-to-use API

**Basic Usage Examples:**

```java
// Parse HTML and extract content
Document doc = Jsoup.parse(html);

// Parse a fragment of HTML
Document doc = Jsoup.parseBodyFragment(html);
Element body = doc.body();

// Load a Document from a URL
Document doc = Jsoup.connect("http://example.com")
    .data("query", "Java")
    .userAgent("Mozilla")
    .cookie("auth", "token")
    .timeout(3000)
    .post();
String title = doc.title();
```

---

## Appendix 2: Complete Code

Below is the complete source code for the scraper:

```java
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;

import java.io.File;
import java.io.FileOutputStream;
import java.io.InputStream;
import java.net.URL;
import java.util.ArrayList;

public class ImageScraper {

    public static void main(String[] args) {
        try {
            String url = "http://example.com";
            Document doc = Jsoup.connect(url).get();
            Elements images = doc.select("a.item.lazy > img");
            
            ArrayList<String> imageUrls = new ArrayList<>();
            for (Element img : images) {
                String imageUrl = img.attr("src");
                imageUrls.add(imageUrl);
            }

            File folder = new File("images");
            if (!folder.exists()) {
                folder.mkdir();
            }

            int count = 1;
            for (String imageUrl : imageUrls) {
                try (InputStream in = new URL(imageUrl).openStream();
                     FileOutputStream out = new FileOutputStream(new File(folder, count + ".jpg"))) {
                    byte[] buffer = new byte[1024];
                    int bytesRead;
                    while ((bytesRead = in.read(buffer)) != -1) {
                        out.write(buffer, 0, bytesRead);
                    }
                    count++;
                }
            }
            System.out.println("Downloaded " + (count - 1) + " images.");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

---

> **Simplify your web scraping projects with ScraperAPI!**  
> Handle millions of requests effortlessly and collect data without the hassle.  
> **Try it now 👉 [https://www.scraperapi.com/?fp_ref=coupons](https://www.scraperapi.com/?fp_ref=coupons)**  
