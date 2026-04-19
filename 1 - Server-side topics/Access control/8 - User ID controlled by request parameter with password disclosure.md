## User ID controlled by request parameter with password disclosure

This lab has user account page that contains the current user's existing password, prefilled in a masked input.

To solve the lab, retrieve the administrator's password, then use it to delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

## Notes

- /admin
- /login

## Resume

1. Login with wiener:peter
2. Update the `id=administrator` on profil page
3. Check the response
4. Get administrator password
5. Login in with administrator and delete Carlos


## Solve 

Login with wiener:peter and access to the **My account** page. Update the parameter in the url : 

`/my-account?id=wiener` to `/my-account?id=administrator`

Check the request that redirect to the **login** page : 

```
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
Cache-Control: no-cache
X-Frame-Options: SAMEORIGIN

                           <input required type="hidden" name="csrf" value="fl27Q3cUwdzaXSs6pLiOgEJjMBUh6ZFx">
                            <input required type=password name=password value='01mn374sxeyeem1wnwr4'/>
                            <button class='button' type='submit'> Update password 
```

Login with administrator account and delete Carlos