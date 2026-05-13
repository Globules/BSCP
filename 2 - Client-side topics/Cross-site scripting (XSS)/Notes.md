## Labs tips

Every CSRF lab generally contains:

- Sensitive state-changing actions:
  - Email change
  - Password reset
  - Account deletion
  - Role modification
- CSRF tokens:
  - Missing
  - Weak validation
  - Token duplicated in cookie
  - Token not tied to session
- Session cookies:
  - `SameSite=None`
  - `SameSite=Lax`
  - No CSRF protection

Common testing workflow:

1. Capture the sensitive request
2. Remove or modify the CSRF token
3. Replay the request
4. Build a CSRF PoC
5. Deliver the exploit through HTML or JavaScript

---

## Link

### BSCP Academy

- [CSRF](https://portswigger.net/web-security/csrf)

### BSCP Labs

- [CSRF vulnerability with no defenses](https://portswigger.net/web-security/csrf/lab-no-defenses)
- [CSRF where token validation depends on request method](https://portswigger.net/web-security/csrf/lab-token-validation-depends-on-request-method)
- [CSRF where token validation depends on token being present](https://portswigger.net/web-security/csrf/lab-token-validation-depends-on-token-being-present)
- [CSRF where token is not tied to user session](https://portswigger.net/web-security/csrf/lab-token-not-tied-to-user-session)
- [CSRF where token is tied to non-session cookie](https://portswigger.net/web-security/csrf/lab-token-tied-to-non-session-cookie)
- [CSRF where token is duplicated in cookie](https://portswigger.net/web-security/csrf/lab-token-duplicated-in-cookie)
- [SameSite Lax bypass via method override](https://portswigger.net/web-security/csrf/bypassing-samesite-restrictions/lab-samesite-lax-bypass-via-method-override)
- [SameSite Strict bypass via client-side redirect](https://portswigger.net/web-security/csrf/bypassing-samesite-restrictions/lab-samesite-strict-bypass-via-client-side-redirect)

### Other resources

- [MDN - CSRF](https://developer.mozilla.org/en-US/docs/Glossary/CSRF)
- [OWASP CSRF Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)
- [SameSite cookies explained](https://web.dev/articles/samesite-cookies-explained)