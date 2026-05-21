## Client-side prototype pollution via browser APIs

This lab is vulnerable to DOM XSS via client-side prototype pollution. The website's developers have noticed a potential gadget and attempted to patch it. However, you can bypass the measures they've taken.

To solve the lab:

1. Find a source that you can use to add arbitrary properties to the global Object.prototype.
2. Identify a gadget property that allows you to execute arbitrary JavaScript.
3. Combine these to call alert().

You can solve this lab manually in your browser, or use DOM Invader to help you.

This lab is based on real-world vulnerabilities discovered by PortSwigger Research. For more details, check out Widespread prototype pollution gadgets by Gareth Heyes.

## Notes



## Resume



## Solve