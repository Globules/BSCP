## Features to Enumerate

- Forgot password functionality
- Login functionality
- Admin panel
- Internal admin panel
- Homepage script import
- Exploit Server
- Burp Collaborator interactions

---

## What to look for

- `Host` header used to generate password reset links
- `Host` header trusted for authentication decisions
- Duplicate `Host` headers
- Reflected host value inside script URL
- Ambiguous request parsing
- Absolute URL in the request line
- Internal IP range `192.168.0.0/24`
- Redirect to `/admin`
- Cache hit with `X-Cache: hit`
- Connection state behavior across multiple requests

## Common endpoint

- /login
- /forgot-password
- /admin
- /admin/delete
- /resources/js/tracking.js

## Common Headers

- Host
- Cookie
- Set-Cookie
- X-Cache
- Connection

