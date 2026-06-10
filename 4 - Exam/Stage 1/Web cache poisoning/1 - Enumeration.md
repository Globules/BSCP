## Features to Enumerate

- Cache behavior
- Reflected headers
- Unkeyed inputs
- Open redirect
- Static resources
- JavaScript imports
- Exploit Server

---

## What to look for

- Unkeyed headers
- Cacheable responses
- Reflected values
- Host header handling
- `X-Forwarded-*` headers
- `X-Host`
- `X-Forwarded-Host`
- `X-Forwarded-Scheme`
- Cache key flaws
- Dynamic content cached

## Common endpoint

- /
- /resources/js/tracking.js
- /post
- /login
- /my-account

## Useful Burp features

- Param Miner
