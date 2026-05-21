## DOM XSS via an alternative prototype pollution vector

This lab is vulnerable to DOM XSS via client-side prototype pollution. To solve the lab:

1. Find a source that you can use to add arbitrary properties to the global Object.prototype.
2. Identify a gadget property that allows you to execute arbitrary JavaScript.
3. Combine these to call alert().

You can solve this lab manually in your browser, or use DOM Invader to help you.

Hint:

Pay attention to the XSS context. You need to adjust your payload slightly to ensure that the JavaScript syntax remains valid following your injection.

## Notes



## Resume



## Solve