## 11 - DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded

This lab contains a DOM-based cross-site scripting vulnerability in a AngularJS expression within the search functionality.

AngularJS is a popular JavaScript library, which scans the contents of HTML nodes containing the ng-app attribute (also known as an AngularJS directive). When a directive is added to the HTML code, you can execute JavaScript expressions within double curly braces. This technique is useful when angle brackets are being encoded.

To solve this lab, perform a cross-site scripting attack that executes an AngularJS expression and calls the alert function.

## Notes

- Search page
- Possibility to leave a comment
- `/resources/js/angular_1-7-7.js`

## Resume

1. Go to the search page
2. Identify the AngularJS context
3. Inject an AngularJS payload
4. Trigger the XSS

## Solve

First, enter a random alphanumeric string into the search box.

Then, inspect the page source and observe that the input is reflected inside an AngularJS application context using the `ng-app` directive.

Example:

```html
<div ng-app>
    searchterm
</div>
```

Since AngularJS expressions are evaluated automatically, it is possible to inject JavaScript using an AngularJS sandbox escape.

Use the following payload in the search box:

```text
{{constructor.constructor('alert(1)')()}}
```

After submitting the search request, AngularJS evaluates the expression and executes:

```javascript
alert(1)
```

This confirms the reflected XSS vulnerability through AngularJS expression injection.
