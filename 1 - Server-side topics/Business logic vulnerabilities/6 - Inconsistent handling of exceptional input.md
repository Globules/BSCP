## Inconsistent handling of exceptional input

This lab doesn't adequately validate user input. You can exploit a logic flaw in its account registration process to gain access to administrative functionality. To solve the lab, access the admin panel and delete the user carlos.

Hint :

You can use the link in the lab banner to access an email client connected to your own private mail server. The client will display all messages sent to @YOUR-EMAIL-ID.web-security-academy.net and any arbitrary subdomains. Your unique email ID is displayed in the email client.

## Notes

- /admin
- /register
- Exploit server

## Resume

1. Discover hidden `/admin` endpoint
2. Register with a very long email address
3. Confirm that emails are truncated to 255 characters
4. Craft a malicious email ending with `@dontwannacry.com`
5. Gain admin access
6. Delete `carlos`

## Solve

While proxying traffic through Burp, go to **Target > Site map**. Right click the lab domain and select:

`Engagement tools > Discover content`

Start the discovery scan. After a few seconds, a new endpoint appears:

`/admin`

Trying to access it directly returns an error saying only **DontWannaCry** users can access the page.

Go to the registration page. A message indicates that employees must register using a `@dontwannacry.com` email address.

Open the email client from the lab banner and note your mail domain:

`@YOUR-ID.web-security-academy.net`

Register a first account using an extremely long email address:

```text
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa@YOUR-ID.web-security-academy.net
```

Confirm the account using the email received.

Once logged in, go to **My account** and notice the email address is truncated after **255 characters**.

Now register another account, but this time inject `dontwannacry.com` before your email domain:

```text
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa@dontwannacry.com.exploit-0a5000d404c581b081a62417013700b6.exploit-server.net```

The goal is to make the final `m` of `dontwannacry.com` land exactly at character **255**.

The confirmation email is still delivered to your inbox because the real domain remains:

`YOUR-ID.web-security-academy.net`

However, the backend truncates the stored email value to:

```text
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa@dontwannacry.com
```

After confirming the account and logging in, access to the `/admin` panel is now granted.

Delete the user `carlos` to solve the lab.
