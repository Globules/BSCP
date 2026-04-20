## Inconsistent security controls

This lab's flawed logic allows arbitrary users to access administrative functionality that should only be available to company employees. To solve the lab, access the admin panel and delete the user carlos.


## Notes

- /admin
- /register
- Exploit server

## Resume

1. Register an account using the exploit server domain
2. Login with your account
3. Update the mail using `@dontwannacry.com`
4. Access the admin panel
5. Delete Carlos

## Solve 

Register an account using `attacker@exploit-0ac300fb0458aac181c9243d01800034.exploit-server.net@dontwannacry.com`

On the exploit server go the inbox and use the url in the mail to activate the account then login.

Once logged, use the `update email` feature to update the domain to `@dontwannacry.com` :

`attacker@dontwannacry.com`

Then access the admin panel and delete Carlos's account.