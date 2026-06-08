## Labs tips

Every HTTP Request Smuggling lab generally contains:

- Front-end and back-end servers parse requests differently
- Desynchronization between `Content-Length` and `Transfer-Encoding`
- CL.TE, TE.CL, CL.0, H2.CL, or H2.TE vulnerabilities
- HTTP/2 downgrading issues
- Response queue poisoning
- Request queue poisoning
- Header parsing inconsistencies
- CRLF injection opportunities
- Hidden internal endpoints behind reverse proxies
- Front-end security controls bypasses
- Internal IP restrictions bypasses
- Request rewriting disclosure
- Session hijacking through desynchronization
- Victim request capture
- XSS delivery through poisoned requests
- Burp Repeater heavily used
- "Update Content-Length" often needs to be disabled
- HTTP Request Smuggler extension can simplify payload creation

Common indicators:

- `404 Not Found` on every second request
- Request timeouts (`500 Proxy Error`)
- `Unrecognized method GPOST`
- Unexpected redirects (`302 Found`)
- Responses belonging to another user
- Reflected fragments of smuggled requests
- Mixed request/response boundaries

Common payload components:

- `Content-Length`
- `Transfer-Encoding: chunked`
- Obfuscated `Transfer-Encoding`
- Chunk size manipulation
- Smuggled `GET` or `POST` requests
- `Connection: keep-alive`
- HTTP/2 header injection
- CRLF (`\r\n`) sequences

## Link

### BSCP Academy

- [HTTP request smuggling](https://portswigger.net/web-security/request-smuggling)

### BSCP Labs

- [HTTP request smuggling, confirming a CL.TE vulnerability via differential responses](https://portswigger.net/web-security/request-smuggling/finding/lab-confirming-cl-te-via-differential-responses)
- [HTTP request smuggling, confirming a TE.CL vulnerability via differential responses](https://portswigger.net/web-security/request-smuggling/finding/lab-confirming-te-cl-via-differential-responses)
- [Exploiting HTTP request smuggling to bypass front-end security controls, CL.TE vulnerability](https://portswigger.net/web-security/request-smuggling/exploiting/lab-bypass-front-end-controls-cl-te)
- [Exploiting HTTP request smuggling to bypass front-end security controls, TE.CL vulnerability](https://portswigger.net/web-security/request-smuggling/exploiting/lab-bypass-front-end-controls-te-cl)
- [Exploiting HTTP request smuggling to reveal front-end request rewriting](https://portswigger.net/web-security/request-smuggling/exploiting/lab-reveal-front-end-request-rewriting)
- [Exploiting HTTP request smuggling to capture other users' requests](https://portswigger.net/web-security/request-smuggling/exploiting/lab-capture-other-users-requests)
- [Exploiting HTTP request smuggling to deliver reflected XSS](https://portswigger.net/web-security/request-smuggling/exploiting/lab-deliver-reflected-xss)
- [Response queue poisoning via H2.TE request smuggling](https://portswigger.net/web-security/request-smuggling/advanced/lab-request-smuggling-h2-request-splitting-via-crlf-injection)
- [H2.CL request smuggling](https://portswigger.net/web-security/request-smuggling/advanced/lab-h2-cl-request-smuggling)
- [HTTP/2 request smuggling via CRLF injection](https://portswigger.net/web-security/request-smuggling/advanced/lab-request-smuggling-h2-via-crlf-injection)
- [HTTP/2 request splitting via CRLF injection](https://portswigger.net/web-security/request-smuggling/advanced/lab-request-smuggling-h2-request-splitting-via-crlf-injection)
- [CL.0 request smuggling](https://portswigger.net/web-security/request-smuggling/browser/cl-0)
- [HTTP request smuggling, basic CL.TE vulnerability](https://portswigger.net/web-security/request-smuggling/lab-basic-cl-te)
- [HTTP request smuggling, basic TE.CL vulnerability](https://portswigger.net/web-security/request-smuggling/lab-basic-te-cl)
- [HTTP request smuggling, obfuscating the TE header](https://portswigger.net/web-security/request-smuggling/lab-obfuscating-te-header)

### Other resources

- [HTTP Request Smuggling Academy Topic](https://portswigger.net/web-security/request-smuggling)
- [HTTP Desync Attacks - PortSwigger Research](https://portswigger.net/research/http-desync-attacks-request-smuggling-reborn)
- [Browser-Powered Desync Attacks](https://portswigger.net/research/browser-powered-desync-attacks)
- [HTTP/2: The Sequel Is Always Worse](https://portswigger.net/research/http2)
- [HTTP Request Smuggler Burp Extension](https://github.com/PortSwigger/http-request-smuggler)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)