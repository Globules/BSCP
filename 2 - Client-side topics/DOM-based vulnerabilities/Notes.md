## Labs tips

Every DOM-based vulnerability lab generally contains:

- A client-side JavaScript file:
  - `/resources/js/search.js`
  - `/resources/js/tracking.js`
  - `/resources/js/commentConfirmationRedirect.js`
  - `/resources/js/loadCommentsWithVulnerableEscapeHtml.js`
- User-controlled DOM sources such as:
  - `location.search`
  - `location.hash`
  - `document.referrer`
  - `postMessage()`
  - `document.cookie`
- Dangerous sinks such as:
  - `innerHTML`
  - `document.write()`
  - `eval()`
  - `location`
  - `setTimeout()`
  - `WebSocket()`

Common testing workflow:

1. Identify the source
2. Trace the tainted data flow
3. Find the sink
4. Test payload execution
5. Bypass filters / encoding
6. Trigger the vulnerability

Useful Burp features:

- Burp Proxy
- DOM Invader
- Burp Repeater
- Match & Replace
- Browser DevTools
- Logger++

---

## Link

### BSCP Academy

- [DOM-based vulnerabilities](https://portswigger.net/web-security/dom-based)

### BSCP Labs

#### DOM XSS

- [DOM XSS using document.write](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink)
- [DOM XSS using innerHTML](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-innerhtml-sink)
- [DOM XSS using jQuery selector sink](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-jquery-selector-hash-change-event)
- [DOM XSS using web messages](https://portswigger.net/web-security/dom-based/controlling-the-web-message-source/lab-dom-xss-using-web-messages)
- [DOM XSS using web messages and JSON.parse](https://portswigger.net/web-security/dom-based/controlling-the-web-message-source/lab-dom-xss-using-web-messages-and-json-parse)
- [DOM XSS with AngularJS sandbox escape](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-angularjs-expression)

#### DOM Open Redirection

- [DOM-based open redirection](https://portswigger.net/web-security/dom-based/open-redirection/lab-dom-open-redirection)

#### Web Message Vulnerabilities

- [DOM XSS using postMessage](https://portswigger.net/web-security/dom-based/controlling-the-web-message-source/lab-dom-xss-using-web-messages)
- [DOM XSS using postMessage and wildcard target](https://portswigger.net/web-security/dom-based/controlling-the-web-message-source/lab-dom-xss-using-web-messages-and-json-parse)

#### DOM Clobbering

- [DOM clobbering using HTML injection](https://portswigger.net/web-security/dom-based/dom-clobbering/lab-dom-clobbering-html-sink)


## Useful Tools

### DOM Invader

- Automatically detects sources and sinks
- Helps identify DOM XSS
- Detects prototype pollution gadgets
- Available in Burp Browser DevTools

Documentation:

- https://portswigger.net/burp/documentation/desktop/tools/dom-invader

---

## Other resources

- [DOM-based vulnerabilities documentation](https://portswigger.net/web-security/dom-based)
- [DOM XSS explained](https://portswigger.net/kb/issues/00200310_cross-site-scripting-dom-based)
- [DOM Invader documentation](https://portswigger.net/burp/documentation/desktop/tools/dom-invader)
- [OWASP DOM based XSS Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/DOM_based_XSS_Prevention_Cheat_Sheet.html)
- [MDN Web APIs](https://developer.mozilla.org/en-US/docs/Web/API)
- [RFC 6455 - WebSocket Protocol](https://datatracker.ietf.org/doc/html/rfc6455)