## Features to Enumerate

- Exploit Server
- Ads feature
- Comment feature
- Product page
- Home page
- Back to blog
- `lastViewedProduct` cookie

---

## What to look for

- `window.addEventListener('message')`
- `postMessage()`
- Missing `e.origin` validation
- `JSON.parse(e.data)`
- `innerHTML` sink
- `location.href` sink
- `iframe.src` sink
- Weak `indexOf()` validation
- `url` parameter
- `lastViewedProduct` cookie

## Common endpoint / page

- Home page `/`
- Blog post page `/post?postId=`
- Product page `/product?productId=`
- Exploit Server

## Common sources

- `postMessage()` / `e.data`
- `location`
- `url` parameter
- Product URL
- `lastViewedProduct` cookie

## Common sinks

- `innerHTML`
- `location.href`
- `iframe.src`
