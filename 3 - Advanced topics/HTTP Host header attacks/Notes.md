## Labs tips

Every lab from HTTP Host Header Attacks has:

- HTTP Host header is used for routing, redirects, or generating links
- Backend trusts `Host` without proper validation
- Possibility of virtual host routing (multiple domains on same IP)
- SSRF triggered via Host header manipulation
- Password reset flows using Host header to generate poisoned links
- Cache poisoning via ambiguous or duplicated Host headers
- Internal admin panels reachable via host-based routing
- Weak parsing differences between proxy, cache, and origin server
- Burp Suite (Proxy, Repeater, Intruder) heavily used
- Collaborator often used for detecting outbound interactions
- Misconfigured reverse proxies or load balancers

## Link

### BSCP Academy

- [HTTP Host header attacks](https://portswigger.net/web-security/host-header)

### BSCP Labs

- [Basic password reset poisoning](https://portswigger.net/web-security/host-header/lab-password-reset-poisoning)
- [Host header authentication bypass](https://portswigger.net/web-security/host-header/lab-host-header-authentication-bypass)
- [Web cache poisoning via ambiguous requests](https://portswigger.net/web-security/host-header/lab-web-cache-poisoning-via-ambiguous-requests)
- [Routing-based SSRF](https://portswigger.net/web-security/host-header/lab-routing-based-ssrf)
- [SSRF via flawed request parsing](https://portswigger.net/web-security/host-header/lab-ssrf-via-flawed-request-parsing)
- [Host validation bypass via connection state attack](https://portswigger.net/web-security/host-header/lab-host-validation-bypass-via-connection-state-attack)

### Other resources

- [HTTP Host header attacks overview](https://portswigger.net/web-security/host-header)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [Host header injection explained](https://www.youtube.com/watch?v=example1)
- [Web cache poisoning concepts](https://www.youtube.com/watch?v=example2)
- [SSRF explained](https://www.youtube.com/watch?v=example3)