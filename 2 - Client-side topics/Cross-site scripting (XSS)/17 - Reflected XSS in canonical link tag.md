## 17 - Reflected XSS in canonical link tag

This lab reflects user input in a canonical link tag and escapes angle brackets.

To solve the lab, perform a cross-site scripting attack on the home page that injects an attribute that calls the alert function.

To assist with your exploit, you can assume that the simulated user will press the following key combinations:

```
ALT+SHIFT+X
CTRL+ALT+X
Alt+X
```

Please note that the intended solution to this lab is only possible in Chrome.

## Notes

- Possibility to leave a comment on a post

## Resume

1. Inject an attribute payload in the URL
2. Add an access key and event handler
3. Trigger the payload with keyboard shortcuts
4. Execute the XSS

## Solve

The application reflects user input inside an HTML attribute without proper sanitization.

Use the following payload in the URL:

```text
https://0af90097040971d180c4c115001c00b2.web-security-academy.net/?%27accesskey=%27x%27onclick=%27alert(1)
```

Decoded payload:

```html
'accesskey='x'onclick='alert(1)
```

- The first `'` closes the existing HTML attribute.
- `accesskey='x'` creates a keyboard shortcut using the `X` key.
- `onclick='alert(1)'` injects a JavaScript event handler.

The payload becomes part of the generated HTML and assigns an `onclick` event to the element.

Press the corresponding keyboard shortcut for your operating system:

- **Windows** → `ALT + SHIFT + X`
- **macOS** → `CTRL + ALT + X`
- **Linux** → `ALT + X`

When the shortcut is pressed, the browser activates the element associated with the access key and executes:

```javascript
alert(1)
```

This triggers the XSS and solves the lab.
