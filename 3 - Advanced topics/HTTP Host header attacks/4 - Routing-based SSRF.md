## Routing-based SSRF

This lab is vulnerable to routing-based SSRF via the Host header. You can exploit this to access an insecure intranet admin panel located on an internal IP address.

To solve the lab, access the internal admin panel located in the 192.168.0.0/24 range, then delete the user carlos.

Note :

To prevent the Academy platform being used to attack third parties, our firewall blocks interactions between the labs and arbitrary external systems. To solve the lab, you must use Burp Collaborator's default public server.

## Notes

- /login

## Resume

1. Confirm that the middleware issues requests to attacker-controlled hosts.
2. Use Intruder on the `Host` header to find the internal admin host.
3. Access `/admin` on the discovered host.
4. Capture the CSRF token and session value from the response.
5. Submit a crafted POST request to `/admin/delete`.
6. Delete `carlos`.

## Solve

Send the `GET /` request that received a `200` response to Burp Repeater.

In Burp Repeater, select the `Host` header value, right-click, and choose **Insert Collaborator payload**. Replace it with a Burp Collaborator domain name, then send the request.

Go to the **Collaborator** tab and click **Poll now**. You should see network interactions, including an HTTP request. This confirms that the middleware can issue requests to arbitrary servers.

Send the `GET /` request to Burp Intruder.

In Intruder, deselect **Update Host header to match target**.

Delete the `Host` header value and replace it with:

```http
Host: 192.168.0.§0§
```

In the **Payloads** side panel, select **Numbers** and configure:

```http
From: 0
To: 255
Step: 1
```

Start the attack.

When the attack finishes, sort by the **Status** column. One request should return a `302` redirect to `/admin`. Send that request to Burp Repeater.

In Burp Repeater, change the request line to:

```http
GET /admin HTTP/2
```

Send it. The response should show the admin panel.

Study the delete-user form. It generates a POST request to `/admin/delete` with a `csrf` token and a `username` parameter. You need to manually craft the equivalent request to delete `carlos`.

Change the path in your request to:

```http
/admin/delete
```

Copy the CSRF token from the HTML response and add it as a parameter. Also add:

```http
username=carlos
```

The request line should become:

```http
GET /admin/delete?csrf=6qt9BQ0akl6N6DYJxv9eIfyIoY7pvWpQ&username=carlos
```

Copy the session cookie from the `Set-Cookie` header in the response and add it to your request.

Right-click the request and select **Change request method**. Burp will convert it to a POST request.

Your final request should look like this:

```http
POST /admin/delete HTTP/2
Host: 192.168.0.128
Cookie: session=mFceQJOgrZGXXe5jvbOxiiJnFZAFUfv6; _lab=47%7cMC0CFQCEkaFCrVT5%2bZONl41iexYGCv7ywwIULQeDAM0wYiP9lNVBXAhElEiHgm9PlVmI6dlYEa7tT5ZgIzb81xLNJjxyzUxjstZHPCuCuNJJ%2bdosU%2bNutcGPn27UUvosU0NcJ9RMWSIYGrcjVvDfMt4QztN2ZR7qH8KR420xo6LewnLp
Content-Type: application/x-www-form-urlencoded
Content-Length: 53

username=carlos&csrf=6qt9BQ0akl6N6DYJxv9eIfyIoY7pvWpQ
```

Send the request to delete `carlos` and solve the lab.