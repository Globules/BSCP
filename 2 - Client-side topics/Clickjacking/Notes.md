## Labs tips

Every Clickjacking lab generally contains:

- Sensitive actions that can be framed:
  - Account deletion
  - Email change
  - Password update
  - Purchase confirmation
  - Admin actions
- Missing or weak anti-framing protections:
  - No `X-Frame-Options`
  - Weak `Content-Security-Policy`
  - Misconfigured `frame-ancestors`
- User interaction requirements:
  - Click button
  - Hover action
  - Drag & drop
  - Multiple-step confirmation

Common testing workflow:

1. Check if the page can be framed
2. Create an iframe PoC
3. Hide or style the iframe
4. Align the victim click with the target action
5. Trigger the action
6. Bypass frame protections if possible

Useful Burp features:

- Burp Browser
- Repeater
- Response headers inspection
- Match & Replace
- Embedded browser DevTools

---

## Link

### BSCP Academy

- [Clickjacking](https://portswigger.net/web-security/clickjacking)

### BSCP Labs

- [Basic clickjacking with CSRF token protection](https://portswigger.net/web-security/clickjacking/lab-basic-csrf-protected)
- [Clickjacking with form input data prefilled from a URL parameter](https://portswigger.net/web-security/clickjacking/lab-prefilled-form-input)
- [Clickjacking with a frame buster script](https://portswigger.net/web-security/clickjacking/lab-frame-buster-script)
- [Exploiting clickjacking vulnerability to trigger DOM-based XSS](https://portswigger.net/web-security/clickjacking/lab-exploiting-to-trigger-dom-based-xss)
- [Multistep clickjacking](https://portswigger.net/web-security/clickjacking/lab-multistep)

---

## Common Headers

### X-Frame-Options

```http
X-Frame-Options: DENY
X-Frame-Options: SAMEORIGIN
```

### CSP frame-ancestors

```http
Content-Security-Policy: frame-ancestors 'self'
```

### Search for protections

```http
X-Frame-Options
Content-Security-Policy
frame-ancestors
```

---

## Other resources

- [Clickjacking documentation](https://portswigger.net/web-security/clickjacking)
- [OWASP Clickjacking Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html)
- [MDN X-Frame-Options](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options)
- [MDN CSP frame-ancestors](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
- [UI Redressing attacks explained](https://owasp.org/www-community/attacks/Clickjacking)