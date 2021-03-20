# Speeding-up SPA

**Created:** 19.03.2021, **last updated:** 20.03.2021

The process of speeding-up SPA may be divided into two parts: monitoring & improving. 

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

## 2. Improving

### 1. Eliminate unnecessary downloads

- measure the performance of each asset: its value and its technical performance
- determine if the resources are providing sufficient values

### 2. Compress data

- 

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

## Sources:

[https://www.mindk.com/blog/how-to-speed-up-your-single-page-application/](https://www.mindk.com/blog/how-to-speed-up-your-single-page-application/)

[https://calibreapp.com/blog/react-performance-profiling-optimization](https://calibreapp.com/blog/react-performance-profiling-optimization)

[https://designingforperformance.com/measuring-and-iterating/](https://designingforperformance.com/measuring-and-iterating/)

[https://stackify.com/rum-vs-synthetic-monitoring/](https://stackify.com/rum-vs-synthetic-monitoring/)

[https://developers.google.com/web/fundamentals/performance/get-started](https://developers.google.com/web/fundamentals/performance/get-started)