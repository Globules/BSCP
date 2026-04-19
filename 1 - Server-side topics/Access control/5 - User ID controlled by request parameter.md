## User ID controlled by request parameter 

This lab has a horizontal privilege escalation vulnerability on the user account page.

To solve the lab, obtain the API key for the user carlos and submit it as the solution.

You can log in to your own account using the following credentials: wiener:peter

## Notes

- /login

## Resume

1. Login with wiener:peter
2. Update the `id=wiener` on profil page
3. Get Carlos's API key


## Solve 

Login with wiener:peter and access to the **My account** page. Update the parameter in the url : 

`/my-account?id=wiener` to `/my-account?id=carlos`

Get Carlo's API key : **Your API Key is: AGmMvu1nY0lgW1LRelLyVKisvbiyadgW**