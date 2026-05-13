## Labs tips

Every WebSocket lab generally contains:

- A WebSocket endpoint such as:
  - `/chat`
  - `/ws`
  - `/notifications`
  - `/support`
- A client-side JavaScript file handling the socket connection:
  - `/resources/js/chat.js`
  - `/resources/js/livechat.js`
  - `/resources/js/websocket.js`
- A WebSocket upgrade request:

```http
GET /chat HTTP/1.1
Upgrade: websocket
Connection: Upgrade
```

Useful Burp features:

- Proxy → WebSockets history
- Repeater → WebSocket tab
- Intercept WebSocket messages
- Match & Replace rules
- Burp DOM Invader (for WebSocket XSS sinks)

---

## Link

### BSCP Academy

- [WebSockets](https://portswigger.net/web-security/websockets)

### BSCP Labs

- [Manipulating WebSocket messages to exploit vulnerabilities](https://portswigger.net/web-security/websockets/lab-manipulating-messages-to-exploit-vulnerabilities)
- [Manipulating the WebSocket handshake to exploit vulnerabilities](https://portswigger.net/web-security/websockets/lab-manipulating-handshake-to-exploit-vulnerabilities)
- [Cross-site WebSocket hijacking](https://portswigger.net/web-security/websockets/cross-site-websocket-hijacking/lab)
- [WebSocket vulnerabilities](https://portswigger.net/web-security/websockets)

### Other resources

- [WebSockets explained](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API)
- [Introduction to WebSocket Security](https://portswigger.net/web-security/websockets)
- [RFC 6455 - The WebSocket Protocol](https://datatracker.ietf.org/doc/html/rfc6455)
- [WebSocket Security Testing Guide](https://cheatsheetseries.owasp.org/cheatsheets/WebSocket_Security_Cheat_Sheet.html)
- [WebSockets in 100 Seconds & Beyond with Socket.io](https://www.youtube.com/watch?v=1BfCnjr_Vjg)