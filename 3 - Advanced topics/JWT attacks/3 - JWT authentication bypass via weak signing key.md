## JWT authentication bypass via weak signing key

This lab uses a JWT-based mechanism for handling sessions. It uses an extremely weak secret key to both sign and verify tokens. This can be easily brute-forced using a wordlist of common secrets.

To solve the lab, first brute-force the website's secret key. Once you've obtained this, use it to sign a modified session token that gives you access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Tip
We recommend familiarizing yourself with how to work with JWTs in Burp Suite before attempting this lab.

We also recommend using hashcat to brute-force the secret key. For details on how to do this, see Brute forcing secret keys using hashcat.

## Notes

- /admin
- Search page
- Possibility to leave a comment on a post

## Resume

1. Log in as your own user.
2. Send the post-login `GET /my-account` request to Burp Repeater.
3. Confirm that `/admin` is only accessible as `administrator`.
4. Brute-force the JWT secret key.
5. Recreate the key in Burp as a symmetric JWK.
6. Change the JWT payload so `sub` becomes `administrator`.
7. Sign the token with the recovered key.
8. Access the admin panel and delete `carlos`.

## Solve

In Burp, load the JWT Editor extension from the BApp store.

In the lab, log in to your own account and send the post-login `GET /my-account` request to Burp Repeater.

In Burp Repeater, change the path to `/admin` and send the request. You should observe that the admin panel is only accessible when logged in as the `administrator` user.

Copy the JWT and brute-force the secret. Using hashcat, the command is:

```bash
hashcat jwt.txt /usr/share/wordlists/rockyou.txt
```

Output:

```bash
hashcat (v7.1.2) starting in autodetect mode

Hash-mode was not specified with -m. Attempting to auto-detect hash mode.
The following mode was auto-detected as the only one matching your input hash:

16500 | JWT (JSON Web Token) | Network Protocol

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

eyJraWQiOiI2MTA2ZDE1MC05OTUwLTQ0YTQtYjQzNi1kYTg3OGIwY2I5M2EiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc3OTk1NzgzMCwic3ViIjoid2llbmVyIn0.ApniWyb20iFhh6sNl8LfiLwpbOaoiPZUZmmpjOIBshA:secret1
```

If you run the command again, use the `--show` flag to display the result.

Using Burp Decoder, Base64-encode the recovered secret:

```text
secret1
```

Then in Burp, open the **JWT Editor Keys** tab and click **New Symmetric Key**.

In the dialog, click **Generate** to create a JWK-formatted key. You do not need to choose a key size manually, because it will be updated later.

Replace the generated value for the `k` property with the Base64-encoded secret.

Save the key by clicking **OK**.

Go back to the `GET /admin` request in Burp Repeater and switch to the extension-generated **JSON Web Token** editor tab.

Modify the payload so that the `sub` claim becomes:

```json
"administrator"
```

Then, at the bottom of the tab, click **Sign** and select the symmetric key you created in the previous step.

Make sure **Don't modify header** is selected, then click **OK**.

The token is now signed with the correct signature.

Send the request. You should now successfully access the admin panel.

The admin page contains a delete link for `carlos`, or you can send this request directly:

```http
GET /admin/delete?username=carlos HTTP/2
Host: 0aad002d0437e77d80865de000460060.web-security-academy.net
Cookie: session=eyJraWQiOiI2MTA2ZDE1MC05OTUwLTQ0YTQtYjQzNi1kYTg3OGIwY2I5M2EiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc3OTk1NzgzMCwic3ViIjoiYWRtaW5pc3RyYXRvciJ9.ajHLDXgAD_JFiMdm5E0jRuI75xNbFvceosbBtrMxQkw
```

Send that request to solve the lab.