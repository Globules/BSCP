## Client-side prototype pollution via browser APIs

This lab is vulnerable to DOM XSS via client-side prototype pollution. The website's developers have noticed a potential gadget and attempted to patch it. However, you can bypass the measures they've taken.

To solve the lab:

1. Find a source that you can use to add arbitrary properties to the global Object.prototype.
2. Identify a gadget property that allows you to execute arbitrary JavaScript.
3. Combine these to call alert().

You can solve this lab manually in your browser, or use DOM Invader to help you.

This lab is based on real-world vulnerabilities discovered by PortSwigger Research. For more details, check out Widespread prototype pollution gadgets by Gareth Heyes.

## Notes

- Search page 
- Possibility to leave a comment
- Feedback feature on /feedback
- /deparam.js
- /searchLoggerConfigurable.js

## Resume

1. Identify the pollution source.
2. Confirm prototype pollution with the `search` parameter.
3. Find the gadget that reads the inherited `value` property.
4. Observe the `script.src` sink.
5. Set `value` to a `data:` URL.
6. Trigger `alert(1)` and solve the lab.

## Solve

Load the lab in Burp’s built-in browser, then enable DOM Invader with prototype pollution detection.


Reload the page and observe that DOM Invader detects prototype pollution vectors in the `search` query parameter.


Use the scan result to confirm the sink chain:

- source: `search`
- gadget: `value`
- sink: `script.src`

The proof of concept is to set the polluted `value` property to a JavaScript `data:` URL:

```http
GET /?search=rxnnr82h&__proto__[value]=data:,alert(1)
```

When the page later creates a script element, it reads the inherited `value` property and assigns it to `script.src`.

That makes the browser load:

```text
data:,alert(1)
```

which executes JavaScript and triggers the alert.
