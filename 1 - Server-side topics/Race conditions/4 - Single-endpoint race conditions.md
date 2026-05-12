## Single-endpoint race conditions

This lab's email change feature contains a race condition that enables you to associate an arbitrary email address with your account.

Someone with the address carlos@ginandjuice.shop has a pending invite to be an administrator for the site, but they have not yet created an account. Therefore, any user who successfully claims this address will automatically inherit admin privileges.

To solve the lab:

1. Identify a race condition that lets you claim an arbitrary email address.
2. Change your email address to carlos@ginandjuice.shop.
3. Access the admin panel.
4. Delete the user carlos

You can log in to your own account with the following credentials: wiener:peter.

You also have access to an email client, where you can view all emails sent to @exploit-<YOUR-EXPLOIT-SERVER-ID>.exploit-server.net addresses.

Note :

Solving this lab requires Burp Suite 2023.9 or higher.

## Notes

- `/login`
- `/my-account/change-email`
- Vulnerability type: Race condition / state collision

---

## Resume

1. Analyze the email change workflow
2. Confirm that only one pending email is stored server-side
3. Benchmark sequential vs parallel requests
4. Trigger a collision using parallel requests
5. Receive Carlos' confirmation link on attacker mailbox
6. Confirm the email change
7. Access admin panel and delete user `carlos`

## Solve

Change your email address to an attacker-controlled address:

```http
POST /my-account/change-email HTTP/2
```

Example:

```txt
email=test@exploit-server.net
```

A confirmation email is sent containing a unique token.

After clicking the link, the email address is updated successfully.

Submit two different email addresses one after another:

```txt
test1@exploit-server.net
test2@exploit-server.net
```

Observe that:

- Only the latest confirmation link remains valid
- The first token becomes invalid

This indicates that the application stores only **one pending email address** per account.

Potential collision identified.

Send the request to Burp Repeater and create multiple duplicates.

Request #1

```http
POST /my-account/change-email HTTP/2
Host: 0a1a000f04ed8997829192dc002a00d9.web-security-academy.net
Cookie: session=h1dpAOg8h8gTqlAZctyxYMOuoJzB5k7x
Content-Type: application/x-www-form-urlencoded

email=carlos@ginandjuice.shop&csrf=0tUcDogyLGm0SRnwEQBXXexUa0ogeH1x
```

Request #2

```http
POST /my-account/change-email HTTP/2
Host: 0a1a000f04ed8997829192dc002a00d9.web-security-academy.net
Cookie: session=h1dpAOg8h8gTqlAZctyxYMOuoJzB5k7x
Content-Type: application/x-www-form-urlencoded

email=test1@exploit-0a1b00a504a5899b823c910101b70053.exploit-server.net&csrf=0tUcDogyLGm0SRnwEQBXXexUa0ogeH1x
```

Send multiple requests **in sequence**.

Result:

- One confirmation email per request
- Email recipient always matches the requested address

No collision occurs.

Send the same requests **in parallel**.

Observe the received emails carefully.

Sometimes the confirmation email sent to the attacker mailbox contains:

```txt
carlos@ginandjuice.shop
```

inside the email body or confirmation link.

This indicates that:

- The application sends the email asynchronously
- Another request modifies the pending email value before the template is rendered
- A race condition occurs between:
  1. Saving the pending email
  2. Generating/sending the confirmation email

Create a group containing only two requests:

```txt
email=carlos@ginandjuice.shop
```

```txt
email=test1@exploit-0a1b00a504a5899b823c910101b70053.exploit-server.net
```

Send both requests **in parallel** repeatedly until:

- The attacker mailbox receives a confirmation email
- The email content references:
  
```txt
carlos@ginandjuice.shop
```

Click the confirmation link received in the attacker mailbox.

The application updates the current account email to:

```txt
carlos@ginandjuice.shop
```

After the email change succeeds:

- Visit `/my-account`
- Observe that an **Admin Panel** link is now visible

Open the admin panel and delete the user carlos