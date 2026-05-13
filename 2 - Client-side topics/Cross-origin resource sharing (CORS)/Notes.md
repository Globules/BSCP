## Labs tips

Every CORS lab generally contains:

- `Access-Control-Allow-Origin`
- `Access-Control-Allow-Credentials`
- Sensitive API endpoints:
  - `/accountDetails`
  - `/api/user`
  - `/my-account`
- Cross-origin JavaScript requests using:
  - `fetch()`
  - `XMLHttpRequest`

Common issues:

- Reflected Origin header
- Wildcard `*`
- `null` origin trusted
- Insecure subdomain trust
- Credentials allowed with arbitrary origins

## Link

### BSCP Academy

- [CORS](https://portswigger.net/web-security/cors)

### BSCP Labs

- [CORS vulnerability with basic origin reflection](https://portswigger.net/web-security/cors/lab-basic-origin-reflection-attack)
- [CORS vulnerability with trusted null origin](https://portswigger.net/web-security/cors/lab-null-origin-whitelisted-attack)
- [CORS vulnerability with trusted insecure protocols](https://portswigger.net/web-security/cors/lab-breaking-https-attack)
- [CORS vulnerability with internal network pivot attack](https://portswigger.net/web-security/cors/lab-internal-network-pivot-attack)

### Other resources

- [MDN - CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)
- [Same-Origin Policy explained](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)
- [CORS Misconfiguration explained](https://portswigger.net/web-security/cors/access-control-allow-origin)