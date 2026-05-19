## 7 - Reflected XSS into attribute with angle brackets HTML-encoded

This lab contains a reflected cross-site scripting vulnerability in the search blog functionality where angle brackets are HTML-encoded. To solve this lab, perform a cross-site scripting attack that injects an attribute and calls the alert function.

Hint :

Just because you're able to trigger the alert() yourself doesn't mean that this will work on the victim. You may need to try injecting your proof-of-concept payload with a variety of different attributes before you find one that successfully executes in the victim's browser.

## Notes

- Search page
- Possibility to leave a comment

## Resume

1. Go on search page
2. Exploit XSS

## Solve

First, submit a random value in the search box and intercept the request using `Burp Suite`.

Send the request to `Burp Repeater` and observe that the search parameter is reflected inside an HTML attribute value.

Example request:

```http
GET /?search=w2z6i%22onfocus%3d%22alert(1)%22autofocus%3d%22jh58c HTTP/2
```

The payload used is:

```text
"onfocus="alert(1)"autofocus="
```

This payload breaks out of the original quoted attribute and injects:

- `onfocus="alert(1)"` → executes JavaScript when the element receives focus.
- `autofocus` → automatically focuses the element when the page loads.

To test the payload:

1. Right-click the request in Burp Repeater.
2. Select `Copy URL`.
3. Paste the URL into the browser.

When the page loads, the injected element automatically receives focus and triggers:

```javascript
alert(1)
```

This confirms the reflected XSS vulnerability.