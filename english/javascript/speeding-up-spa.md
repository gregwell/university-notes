# Speeding-up SPA

**Created:** 19.03.2021, **last updated:** 21.03.2021

The process of speeding-up SPA may be divided into two parts: monitoring & improving. 

### Table of contents:

**1. Monitoring**

- [1. Browser plug-ins](#1-browser-plug-ins-eg-chrome-devtools-or-yslow)
- [2. Synthetic testing](#2-synthetic-testing-eg-webpagetest-catchpoint)
- [3. Real user monitoring (RUM)](#3-real-user-monitoring-rum-eg-google-analytics)

**2. Improving:**

- [1. Eliminate unnecessary downloads](#1-eliminate-unnecessary-downloads)
- [2. Compress data](#2-compress-data)
- [3. Lazily render below-the-fold content (rendering phase)](#3-lazily-render-below-the-fold-content-rendering-phase)
- [4. Lazy data fetching (transition phase)](#4-lazy-data-fetching-transition-phase)
- [5. Cache the static content](#5-cache-the-static-content)
- [6. Use WebSockets where appropriate (i.e. for highly interactive apps)](#6-use-websockets-where-appropriate-ie-for-highly-interactive-apps)
- [7. Employ JSONP/CORS to bypass the same-origin policy](#7-employ-jsonpcors-to-bypass-the-same-origin-policy)
- [8. Use a content delivery network (CDN)](#8-use-a-content-delivery-network-cdn)
- [9. Optimize images](#9-optimize-images)

## 1. Monitoring

- Data collected from monitoring methods can generally be divided into lab data (browser plug-ins, synthetic tests) and field data (RUM). Both of them are necessary for good performance testing.

### 1. Browser plug-ins e.g. Chrome DevTools or YSlow

- give waterfall chart, allow to identify major performance issues
- allow you to track down performance bottlenecks
- use during development stage
- example tools:
    - **Lighthouse (Chrome DevTools)**
        - has audits for performance, accessibility, progressive web apps, SEO and more. (gives you personalized advice)

### 2. Synthetic testing, e.g. WebPageTest, Catchpoint

- help diagnose and solve shorter-term performance problems
- ensure that the site properties and critical user transactions are always performing properly
- use when making a large change, experiment or once a month
- example tools:
    - **WebPageTest**
        - allows you to compare performance of one or more pages in controlled lab environment
        - allows you to deep dive into performance stats and test performance on real devices
        - Lighthouse can also be run on WebPageTest

### 3. Real user monitoring (RUM) e.g. Google Analytics

- relies on JavaScript APIs in the browser to gather statistics on how sites perform for real users
- help with understanding long-term trends
- use once a week
- Two specific APIs measure how fast documents and resources load for users by capturing high-resolution timings
    - **Navigation Timing API**
        - collects performance metrics for HTML documents
    - **Resource Timing API**
        - collects performance metrics for sheets, scripts, images etc.
    - it's relatively easy to get data from these APIs, and that data is critical for measuring loading performance for actual users.
    - the hard part is making sense of the data they provide
    - both of them store performance entries in a performance entry buffer (a list accessible by JavaScript)
    - both of them are a part of JavaScript
    - According to [this article](https://engineering.linkedin.com/blog/2017/02/measuring-and-optimizing-performance-of-single-page-applications) these apis fail to capture JavaScript execution times in SPA, libraries that use these apis were called *traditional.* This is because single-page applications spend a significant amount of time on the browser doing JavaScript execution after the main HTML/JS/CSS are downloaded
- **User Timing API**
    - If you need to measure something cutom - say the time it took to load a product image, the standards (Navigation, Resource Timing APIs) are not helpful.
    - With the User Timing API, the spec provides a high precision timestamp

## 2. Improving

### 1. Eliminate unnecessary downloads

- measure the performance of each asset: its value and its technical performance
- determine if the resources are providing sufficient values

### 2. Compress data

- **Minification**: preprocessing & context-specific optimizations
    - build an inventory of the different content types and consider what kinds of content-specific optimizations can be applied to reduce their size
    - once you figure out what they are, automate these optimizations by adding them to your build and release processes to ensure that the optimizations are applied.
- **Text compression with GZIP**
    - performs best on text-based assets: CSS, JavaScript, HTML.
    - All modern browsers support GZIP compression and will automatically request it.
    - Your server must be configured to enable GZIP compression.
    - Some CDNs require special care to ensure that GZIP is enabled.
- **Combining both methods**
    1. Apply content-specific optimizations first: CSS, JS, and HTML minifiers.
    2. Apply GZIP to compress the minified output.
- Most web servers compress content on your behalf, and you just need to verify that the server is correctly configured to compress all the content types that benefit from GZIP compression.

### 3. Lazily render below-the-fold content (rendering phase)

- rendering only those parts of the page that are visible to the user at the moment
- useful if the SPA spends a lot of time in the render phase

### 4. Lazy data fetching (transition phase)

- defer loading the data until you really need it
- use one high-level call to fetch the data you require for the First Meaningful Paint and another one to lazily load the rest of the data you require for the page.
- works both for the start-up mode and the in-page navigations (because they decrease front-end time)
- Some SPA frameworks, such as React, Angular or Vue, allow developers to split the application code into several bundles, that you can load simultaneously or when necessary

### 5. Cache the static content

- investigate your SPA to figure out when you can store images and other static resources on the user’s device.
- Pulling data from memory or Web Storage takes a lot less time than sending HTTP requests, even with the best servers.
- For massive collections you can:
    - use some kind of pagination and rely on the server for persistence
    - write an LRU algorithm to erase the spare items from the storage.
- Service Workers:
    - client-side scripts running in the background
    - used to decrease the traffic and enable offline functionality
    - how it works:
        - when the browser makes a request for content if first goes through a service worker
        - if the requested content is present in the cache the service worker
            - retrieve it
            - the service worker display it on screen
        - in other cases request the resource from the network
- IndexedDB API
    - used to cache large amounts of structured data

### 6. Use WebSockets where appropriate (i.e. for highly interactive apps)

WebSocket protocol:

- allows bidirectional communication between the user's browser and the server
- unlike with HTTP, the client doesn’t have to constantly send requests to the server to get new messages
- instead, the browser simply listens to the server and receive the message when it’s ready.
- to implement WebSockets various services can be used, for example socket.io

### 7. Employ JSONP/CORS to bypass the same-origin policy

- due to the same-origin policy, you can’t make AJAX calls to the pages your browser perceives as located on another server. To gain access to a third-party API, the app will have to use an origin server as a proxy.
- you can request the resource directly if you don’t process the retrieved data and don’t store it in your system.

**JSON with Padding (JSONP)**

- leverages the fact that browsers allow you to add the `<script>` tags coming from other domains
- with a JSONP request you dynamically construct these tags, passing the URL parameters to the necessary resources
- it only works for <GET> requests.
- adding async and defer attributes to the external scripts will improve performance (without these attributes, the browser would have to download and execute the script before it can display the rest of the page.

**CORS**

- allows you to define who and in what ways can access the content on your server.
- But there is an issue: the preflight checks add a second roundtrip, they can effectively double your latency:
    - How it works:
        - The requests that use any method besides GET, HEAD, and POST initiate a preflight check to confirm the server is ready for cross-origin requests.
        - To run the check, the client sends another request describing the origin, method, and headers of the cross-origin AJAX call.
        - Based on this information, the server decides whether to handle the call.
        - After receiving the response, the client initiates the request for a third-party resource.
    - Possible solutions:
        - 1. Write your APIs and serve your content using only HEAD, GET, POST, Accept, Accept-Language, Content-Language, and Content-Type as they don’t initiate preflight requests.
            - The Accept header gives you the ability to define the acceptable types of content. The preferred type by default is text/html, but you can make application/json or any other type of content as the sole acceptable.
        - 2. Cache the preflight responses to decrease the subsequent checks.
            - In this case, you can’t rely on the usual Cache-Control header to define the caching policies. But you can use the new Access-Control-Max-Age header instead. Its numeric value defines how many seconds it would take to cache the response.

    ### 8. Use a content delivery network (CDN)

    - it's a network of servers located all over the world
    - can be used to deliver static resources like images in more efficient way.
    - each server in the network contains the cached version of the content hosted on the origin server
    - how it works; example:
        - If a user from Melbourne requests an image, the network won’t fetch it from the origin server located in New York. A CDN would use the Australian server (or the alternative with the least latency) to serve the cached content.
    - examples:
        - Cloudflare
        - Amazon CloudFront
    - When selecting a CDN, make sure that the CDN supports HTTP/2. HTTP/2 is a new protocol for delivering content on the web that can help speed up the loading time significantly.

### 9. Optimize images

- **Choose the right image format**
    - consider whether the effect you want can be achieved without images (e.g. by CSS effects)
    - if you have images containing geometric shapes use vector format (SVG)
        - SVG is an XML-based format so it can be minified and gzipped as other source files.
    - if you need to preserve fine detail with highest resolution use PNG.
        - PNG does not apply any lossy compression algorithms beyond the choice of the size of the color palette
        - the highest quality image but at a cost of significantly  higher file size than other formats
    - if you need to optimize a photo or screenshot use JPEG.
        - JPEG uses a combination of lossy and lossless optimization to reduce file size of the image
        - you can try several JPEG quality levels
- **Reduce image file size - this topic should be separate notes, but let's dive in**
    - What is a raster image?
        - It's simply a two-dimensional grid of individual "pixels"
        - Each pixel stores the RGBA values (RGB - colors, A - transparency (alpha channel))
            - Internally, the browser allocates 256 values (shades) for each pixel channel
                - 2^8 = 256, so 8 bits (1 byte) per channel
                - we have 4 channel, so browser allocates 4 bytes for each pixel
                - it doesn't matter what image format is used to transfer data from the server to the client, when the image is decoded by the browser, each pixel always occupies 4 bytes of memory (uncompressed variant)
    - **Reduce *bit depth* of the image**
        - *Big depth* specifies how much color information is available for each pixel in an image
        - More bits of information per pixel = more accurate color representation in an image
        - The file size of an image increases with bit depth
        - Examples:
            - An image with a bit depth of 1 has pixels with two possible values: black and white.
            - An image with a bit depth of 8 has 28, or 256, possible values.
            - Grayscale mode images with a bit depth of 8 have 256 possible gray values.
            - RGB mode images are made of three color channels. An 8‑bit per pixel RGB image has 256 possible values for each channel which means it has over 16 million possible color values.
            - RGB images with 8‑bits per channel (Bits/Channel or bpc) are sometimes called 24‑bit images (8 bits x 3 channels = 24 bits of data for each pixel)
            - Images with 32 Bits/Channel are also known as high dynamic range (HDR) images.
        - Compression example:
            - Reducing the palette from >16 million colors to 256 colors
                - one byte is occupied by transparency (alpha) channel and one byte is occupied by RGB channels (instead of 3!)
                - that's 50% compression savings over the original 4 bytes per pixel!
        - works great when the photo contains only a few colors
    - **Delta encoding**
        - When on the photo many nearby pixels has similar colors we can use delta encoding
        - Instead of storing the individual values for each pixel we can store the difference between adjacent pixels
        - If they are the same, then the difference is 0, so we have to store only a single bit!
        - Saving ever more
            - The human eye has different level of sensitivity to different colors
            - You can optimize your color encoding to account for this by reducing or increasing the palette for those colors.
            - Instead of looking at just the immediate neighbors for each pixel, you can look at larger blocks of nearby pixels and encode different blocks with different settings.

    - **A typical image optimization pipeline**
        1. Image is processed with a [lossy](https://en.wikipedia.org/wiki/Lossy_compression) filter that eliminates some pixel data.
        2. Image is processed with a [lossless](https://en.wikipedia.org/wiki/Lossless_compression) filter that compresses the pixel data.
        - In fact, the difference between various image formats, such as GIF, PNG, JPEG, and others, is in the combination of the specific algorithms they use (or omit) when applying the lossy and lossless steps.
- **Summary:**
    1. Prefer vector formats
    2. Minify and GZIP vector graphics
    3. Prefer WebP over older raster formats
    4. Pick best raster image format
    5. Experiment with optimal quality settings for raster formats.
    6. Remove unnecessary image metadata
    7. Serve scaled images
    8. Use automated tools that will ensure that images are always optimized.
- **Compression tools:**
    - **Imagemin**
        - is built around plugins - a plugin is an npm package that compresses a particular image format

## Sources:

[https://www.mindk.com/blog/how-to-speed-up-your-single-page-application/](https://www.mindk.com/blog/how-to-speed-up-your-single-page-application/)

[https://calibreapp.com/blog/react-performance-profiling-optimization](https://calibreapp.com/blog/react-performance-profiling-optimization)

[https://designingforperformance.com/measuring-and-iterating/](https://designingforperformance.com/measuring-and-iterating/)

[https://stackify.com/rum-vs-synthetic-monitoring/](https://stackify.com/rum-vs-synthetic-monitoring/)

[https://developers.google.com/web/fundamentals/performance/get-started](https://developers.google.com/web/fundamentals/performance/get-started)

[https://engineering.linkedin.com/blog/2017/02/measuring-and-optimizing-performance-of-single-page-applications](https://engineering.linkedin.com/blog/2017/02/measuring-and-optimizing-performance-of-single-page-applications)

[https://css-tricks.com/the-difference-between-minification-and-gzipping/](https://css-tricks.com/the-difference-between-minification-and-gzipping/)

[https://web.dev/fast/#optimize-your-images](https://web.dev/fast/#optimize-your-images)

[https://helpx.adobe.com/photoshop/using/bit-depth.html](https://helpx.adobe.com/photoshop/using/bit-depth.html)

[https://akshayranganath.github.io/what-powers-rum-solutions/](https://akshayranganath.github.io/what-powers-rum-solutions/)
