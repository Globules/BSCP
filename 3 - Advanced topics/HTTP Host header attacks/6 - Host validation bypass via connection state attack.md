## Host validation bypass via connection state attack

This lab is vulnerable to routing-based SSRF via the Host header. Although the front-end server may initially appear to perform robust validation of the Host header, it makes assumptions about all requests on a connection based on the first request it receives.

To solve the lab, exploit this behavior to access an internal admin panel located at 192.168.0.1/admin, then delete the user carlos.

Hint : 

This lab is based on real-world vulnerabilities discovered by PortSwigger Research. For more details, check out Browser-Powered Desync Attacks: A New Frontier in HTTP Request Smuggling.

## Notes



## Resume

1. Send `GET /` to Burp Repeater.
2. Change the path to `/admin` and the `Host` header to `192.168.0.1`.
3. Observe that the request is redirected to the homepage.
4. Duplicate the tab and add both tabs to a new group.
5. Change the first tab back to `/` and restore the lab host.
6. Send the group in sequence over a single connection.
7. Access the admin panel from the second request.
8. Copy the delete form details.
9. Replay the delete request for `carlos`.

## Solve

Send the `GET /` request to Burp Repeater.

Make the following adjustments:

- Change the path to `/admin`.
- Change the `Host` header to `192.168.0.1`.

Send the request. You should be redirected to the homepage.

Duplicate the tab, then add both tabs to a new group.

Select the first tab and make the following adjustments:

- Change the path back to `/`.
- Change the `Host` header back to `0ac200f30398a80a82d8106e00d3000e.h1-web-security-academy.net`.

Using the drop-down menu next to the Send button, change the send mode to:

```http
Send group in sequence (single connection)
```

Change the `Connection` header to:

```http
keep-alive
```

Send the sequence and check the responses. The second request should now successfully access the admin panel.

Study the response and note the delete form details:

- Action attribute: `/admin/delete`
- Input name: `username`
- CSRF token: `2dds76BbLQ5ehahSctAFNplgbJXhpZWH`

On the second tab in your group, use these details to replicate the request issued when submitting the form.

Your final request should look like this:

```http
POST /admin/delete HTTP/1.1
Host: 192.168.0.1
Cookie: session=UNWj73JdSbjvZ7nI1fwBZfNTWkb0RoCW; _lab=47%7cMC0CFCtSc0N0nc3rkhrboFTlVXH%2b%2bMuQAhUAgzktqx6Gg%2f5hXnLaUmKnX2HZkkzC2ewy1sJo2u9l8shrlB5jKu5yOMUrsY59enKb1ZI2jkjp%2bCJ8SvWiO0fs2W3ZxWu63EcWbiPcHmaBqWyFbRZrKSRmZIjNBKE7DKBmbEeClPYJyTNXGpU
Connection: keep-alive
Content-Length: 53

username=carlos&csrf=2dds76BbLQ5ehahSctAFNplgbJXhpZWH
```

Send the requests in sequence down a single connection to solve the lab.