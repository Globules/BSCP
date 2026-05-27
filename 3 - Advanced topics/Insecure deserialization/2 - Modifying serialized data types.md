## Modifying serialized data types

This lab uses a serialization-based session mechanism and is vulnerable to authentication bypass as a result. To solve the lab, edit the serialized object in the session cookie to access the administrator account. Then, delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Hint :

To access another user's account, you will need to exploit a quirk in how PHP compares data of different types.

Note that PHP's comparison behavior differs between versions. This lab assumes behavior consistent with PHP 7.x and earlier.

## Notes

- /admin
- /login
- PHP Object in cookie

## Resume

1. Log in using your own credentials.
2. Open the post-login `GET /my-account` request in Burp.
3. Reveal the serialized PHP object in the session cookie.
4. Modify the object so `username` becomes `administrator`.
5. Change `access_token` to integer `0`.
6. Send the request and access the admin panel.
7. Delete `carlos`.

## Solve

Log in using your own credentials.

In Burp, open the post-login `GET /my-account` request and inspect the session cookie in the **Inspector** panel. You should see a serialized PHP object.

Send this request to Burp Repeater.

In Burp Repeater, use the Inspector panel to modify the session cookie as follows:

- Update the length of the `username` attribute to `13`.
- Change the username to `administrator`.
- Change the access token to the integer `0`.
- Remove the double quotes around the access token value, since it is no longer a string.
- Update the data type label for the access token by replacing `s` with `i`.

The final serialized object should look like this:

```http
O:4:"User":2:{s:8:"username";s:13:"administrator";s:12:"access_token";i:0;}
```

Click **Apply changes**. The modified object will be automatically re-encoded and updated in the request.

Send the request. The response should now contain a link to the admin panel at `/admin`, which confirms that you are being treated as the administrator.

Change the path of your request to `/admin` and resend it. The `/admin` page should contain links for deleting user accounts.

Finally, change the path to:

```http
/admin/delete?username=carlos
```

Send the request to solve the lab.

Payload :

```http
O:4:"User":2:{s:8:"username";s:13:"administrator";s:12:"access_token";i:0;}
```

```http
Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjEzOiJhZG1pbmlzdHJhdG9yIjtzOjEyOiJhY2Nlc3NfdG9rZW4iO2k6MDt9
```