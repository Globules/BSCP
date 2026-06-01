## Labs tips — Prototype Pollution

Every PortSwigger Prototype Pollution lab follows the same core idea:

- JavaScript objects inherit properties from `Object.prototype`.
- attacker-controlled input is merged into objects without proper sanitization.
- pollution usually targets special properties like:
  - `__proto__`
  - `constructor.prototype`
  - nested JSON merge behavior
- the initial goal is to confirm pollution by injecting harmless keys, for example `polluted: true`.
- successful pollution often appears in:
  - DOM changes
  - feature flags
  - client-side logic shifts
- client-side labs focus on browser behavior and DOM XSS.
- server-side labs focus on Node.js or backend prototype chain behavior.
- escalation leads to:
  - DOM XSS
  - authentication bypass
  - privilege escalation

## Link

### BSCP Academy

- [Prototype pollution](https://portswigger.net/web-security/prototype-pollution)

### BSCP Labs (PortSwigger Web Security Academy)

- [Client-side prototype pollution via browser APIs](https://portswigger.net/web-security/prototype-pollution/client-side/lab-prototype-pollution-client-side-via-browser-apis)
- [DOM XSS via client-side prototype pollution](https://portswigger.net/web-security/prototype-pollution/client-side/lab-prototype-pollution-dom-xss-via-client-side-prototype-pollution)
- [DOM XSS via an alternative prototype pollution vector](https://portswigger.net/web-security/prototype-pollution/client-side/lab-prototype-pollution-dom-xss-via-an-alternative-prototype-pollution-vector)
- [Client-side prototype pollution via flawed sanitization](https://portswigger.net/web-security/prototype-pollution/client-side/lab-prototype-pollution-client-side-via-flawed-sanitization)
- [Client-side prototype pollution in third-party libraries](https://portswigger.net/web-security/prototype-pollution/client-side/lab-prototype-pollution-client-side-in-third-party-libraries)
- [Privilege escalation via server-side prototype pollution](https://portswigger.net/web-security/prototype-pollution/server-side/lab-prototype-pollution-privilege-escalation-via-server-side-prototype-pollution)
- [Detecting server-side prototype pollution without polluted property reflection](https://portswigger.net/web-security/prototype-pollution/server-side/lab-prototype-pollution-detecting-without-reflection)
- [Bypassing flawed input filters for server-side prototype pollution](https://portswigger.net/web-security/prototype-pollution/server-side/lab-prototype-pollution-bypassing-filters)
- [Remote code execution via server-side prototype pollution](https://portswigger.net/web-security/prototype-pollution/server-side/lab-prototype-pollution-rce)

### Other resources

- [Prototype pollution explained](https://www.youtube.com/watch?v=KX4mQGJ7Z6Q)
- [PayloadsAllTheThings - Prototype Pollution](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Prototype%20Pollution)
- [OWASP Prototype Pollution](https://owasp.org/www-community/attacks/Prototype_Pollution)