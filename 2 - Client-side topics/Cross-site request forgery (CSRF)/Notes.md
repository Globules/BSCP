## CSRF labs tips

CSRF labs usually revolve around one core idea: the server trusts a request that *looks legitimate* because it comes with cookies, even though the user didn’t intend to trigger it.

You’ll typically see:

- State-changing endpoints without proper protection
  - `/my-account/change-email`
  - `/my-account/delete`
  - `/transfer`
- Session-based authentication (cookies automatically sent by the browser)
- Missing or weak CSRF defenses:
  - No CSRF token
  - Token not validated properly
  - Token not tied to session
- Referer / Origin-based validation (often flawed)
- SameSite cookie behavior:
  - `Lax`, `Strict`, or missing entirely
- OAuth / SSO flows that refresh session cookies
- Cross-site WebSocket or sibling domain trust issues (advanced chaining)

Common exploitation patterns:

- Auto-submitted HTML forms (`document.forms[0].submit()`)
- Forced navigation (`window.location`, `window.open`)
- Cookie refresh tricks (OAuth or login endpoints)
- Referer manipulation via:
  - `history.pushState`
  - `<meta name="referrer">`
  - `Referrer-Policy`
- Method confusion (GET/POST overrides depending on backend)
- Exploiting SameSite Lax timing window (navigation-based POST acceptance)
- Chaining with XSS or sibling domains to bypass cookie restrictions

Common injection / delivery points:

- Exploit server (PortSwigger)
- `<form action=... method=POST>`
- JavaScript redirects
- Hidden inputs
- URL parameters (for GET-based CSRF or method override)
- Popups or user interaction handlers (`onclick`)

## Link

### BSCP Academy

- [Cross-Site Request Forgery (CSRF)](https://portswigger.net/web-security/csrf)

### BSCP Labs

- [CSRF vulnerability with no defenses](https://portswigger.net/web-security/csrf/lab-no-defenses)
- [CSRF where token validation depends on request method](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-validation-depends-on-request-method)
- [CSRF where token is not tied to user session](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-not-tied-to-session)
- [CSRF where token is tied to non-session cookie](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-tied-to-non-session-cookie)
- [CSRF where token is duplicated in cookie](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-duplicated-in-cookie)
- [SameSite Lax bypass via method override](https://portswigger.net/web-security/csrf/bypassing-samesite-restrictions/lab-method-override)
- [SameSite Strict bypass via client-side redirect](https://portswigger.net/web-security/csrf/bypassing-samesite-restrictions/lab-client-side-redirect)
- [SameSite Strict bypass via sibling domain](https://portswigger.net/web-security/csrf/bypassing-samesite-restrictions/lab-sibling-domain)
- [SameSite Lax bypass via cookie refresh](https://portswigger.net/web-security/csrf/bypassing-samesite-restrictions/lab-cookie-refresh)
- [CSRF where Referer validation depends on header being present](https://portswigger.net/web-security/csrf/bypassing-referer-based-defenses/lab-referer-validation-depends-on-header-present)
- [CSRF with broken Referer validation](https://portswigger.net/web-security/csrf/bypassing-referer-based-defenses/lab-broken-referer-validation)