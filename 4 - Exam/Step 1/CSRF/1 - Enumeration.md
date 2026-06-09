## Features to Enumerate

- Update email adress
- Social login
- Chat with websocket

---

## What to look for

### Token validation

- No CSRF token
- Token not validated
- Token validated but not tied to session
- Token duplicated in cookie
- Token validation depends on request method


### Headers

- Same site :
    * Lax
    * Strict
- Referer


### Cookies

- csrfKey

---

## Other

- CRLF injection (most of the time to update cookies)
