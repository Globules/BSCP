## Remote code execution via server-side prototype pollution

This lab is built on Node.js and the Express framework. It is vulnerable to server-side prototype pollution because it unsafely merges user-controllable input into a server-side JavaScript object.

Due to the configuration of the server, it's possible to pollute Object.prototype in such a way that you can inject arbitrary system commands that are subsequently executed on the server.

To solve the lab:

1. Find a prototype pollution source that you can use to add arbitrary properties to the global Object.prototype.
2. Identify a gadget that you can use to inject and execute arbitrary system commands.
3. Trigger remote execution of a command that deletes the file /home/carlos/morale.txt.

In this lab, you already have escalated privileges, giving you access to admin functionality. You can log in to your own account with the following credentials: wiener:peter

Hint :

The command execution sink is only invoked when an admin user triggers vulnerable functionality on the site.

Note :

When testing for server-side prototype pollution, it's possible to break application functionality or even bring down the server completely. If this happens to your lab, you can manually restart the server using the button provided in the lab banner. Remember that you're unlikely to have this option when testing real websites, so you should always use caution.

## Notes



## Resume



## Solve