## Features to Enumerate

- Front-end and back-end request parsing differences
- CL.TE desynchronization
- TE.CL desynchronization
- CL.0 desynchronization
- H2.CL request smuggling
- H2.TE request smuggling
- HTTP/2 downgrading behavior
- Response queue poisoning
- Request queue poisoning
- Header parsing inconsistencies
- CRLF injection opportunities
- Hidden internal endpoints behind reverse proxies
- Front-end security controls bypasses
- Internal IP restrictions bypasses
- Front-end request rewriting disclosure
- Victim request capture
- Reflected XSS delivery through poisoned requests

---

## What to look for

- `404 Not Found` on every second request
- Request timeouts / `500 Proxy Error`
- `Unrecognized method GPOST`
- Unexpected redirects / `302 Found`
- Responses belonging to another user
- Reflected fragments of smuggled requests
- Mixed request/response boundaries
- Both `Content-Length` and `Transfer-Encoding` in one request
- Obfuscated `Transfer-Encoding` header
- Chunk size manipulation
- HTTP/2 header injection
- CRLF (`\r\n`) sequences

## Common endpoint

- /
- /admin
- /admin/delete?username=carlos
- /post/comment
- /post?postId=
- /resources
- /resources/images/blog.svg
- /my-account

## Common Headers

- Content-Length
- Transfer-Encoding: chunked
- Content-Type
- Host
- Cookie
- X-Ignore
- Connection

