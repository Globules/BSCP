## Bypassing flawed input filters for server-side prototype pollution

This lab is built on Node.js and the Express framework. It is vulnerable to server-side prototype pollution because it unsafely merges user-controllable input into a server-side JavaScript object.

To solve the lab:

1. Find a prototype pollution source that you can use to add arbitrary properties to the global Object.prototype.
2. Identify a gadget property that you can use to escalate your privileges.
3. Access the admin panel and delete the user carlos.

You can log in to your own account with the following credentials: wiener:peter

Note :

When testing for server-side prototype pollution, it's possible to break application functionality or even bring down the server completely. If this happens to your lab, you can manually restart the server using the button provided in the lab banner. Remember that you're unlikely to have this option when testing real websites, so you should always use caution.

## Notes



## Resume



## Solve