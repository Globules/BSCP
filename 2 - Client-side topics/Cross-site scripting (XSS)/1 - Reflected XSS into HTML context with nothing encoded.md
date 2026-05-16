## 1 - Reflected XSS into HTML context with nothing encoded

This lab contains a simple reflected cross-site scripting vulnerability in the search functionality.

To solve the lab, perform a cross-site scripting attack that calls the alert function.

## Notes

- Search page
- Possibility to leave a comment

## Resume

1. Go on search page
2. Exploit XSS

## Solve

Go on `/?search` and use the following paylload to perform a XSS that calls an `alert()` : 

```js
<script>alert()</script>
```