## Client-side prototype pollution via flawed sanitization

This lab is vulnerable to DOM XSS via client-side prototype pollution. Although the developers have implemented measures to prevent prototype pollution, these can be easily bypassed.

To solve the lab:

1. Find a source that you can use to add arbitrary properties to the global Object.prototype.
2. Identify a gadget property that allows you to execute arbitrary JavaScript.
3. Combine these to call alert().

## Notes



## Resume



## Solve